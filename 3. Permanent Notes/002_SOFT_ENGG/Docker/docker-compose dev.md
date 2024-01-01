```
version: '3'

volumes:
  mongo-data: {}
  pg-data: {}
  prometheus_data: {}
  grafana_data: {}
  cache: {}

networks:
  dev:
    external: true

services:
  # api:
  #   container_name: api
  #   build:
  #     context: .
  #   image: api:latest
  #   restart: unless-stopped
  #   volumes:
  #     - ./data:/app/data
  #   ports:
  #     - 9090:9090
  #   depends_on:
  #     - mongo
  #     - mongo-express
  #     - redis
  #   environment:
  #     MONGO_URI: "mongodb://${MONGO_INITDB_ROOT_USERNAME}:${MONGO_INITDB_ROOT_PASSWORD}@mongo"
  #     TOKEN_SYMMETRICKEY: ${TOKEN_SYMMETRICKEY}
  #     AUTH_BASIC_USERNAME: ${AUTH_BASIC_USERNAME}
  #     AUTH_BASIC_PASSWORD: ${AUTH_BASIC_PASSWORD}
  #     REDIS_URL: "redis:6379"
  #   networks:
  #     - dev

  #  Demo application
  hotrod:
    image: jaegertracing/example-hotrod:latest
    container_name: hotrod-app
    ports: 
      - "8080:8080"
    command: ["all"]
    environment:
      - JAEGER_AGENT_HOST=jaeger
      - JAEGER_AGENT_PORT=6831
    networks:
      - dev
    depends_on:
      - jaeger

  jaeger:
    image: jaegertracing/all-in-one:latest
    container_name: jeager
    ports:
      - "6831:6831/udp"
      - "16686:16686"
    networks:
      - dev

  elasticsearch:
    image: elasticsearch:7.9.1
    container_name: elasticsearch
    ports:
      - "9200:9200"
      - "9300:9300"
    # volumes:
    #   - test_data:/usr/share/elasticsearch/data/
    #   - ./elk-config/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
    environment:
      - discovery.type=single-node
      - http.host=0.0.0.0
      - transport.host=0.0.0.0
      - xpack.security.enabled=false
      - xpack.monitoring.enabled=false
      - cluster.name=elasticsearch
      - bootstrap.memory_lock=true
    networks:
      - dev

  logstash:
    image: logstash:7.9.1
    container_name: logstash
    ports:
      - "5044:5044"
      - "9600:9600"
    # volumes:
    #   - ./elk-config/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    #   - ./elk-config/logstash/logstash.yml:/usr/share/logstash/config/logstash.yml
    #   - ls_data:/usr/share/logstash/data
    networks:
      - dev
    depends_on:
      - elasticsearch

  kibana:
    image: kibana:7.9.1
    container_name: kibana
    ports:
      - 5601:5601
    # volumes:
    #   - ./elk-config/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml
    #   - kb_data:/usr/share/kibana/data
    networks:
      - dev
    depends_on:
      - elasticsearch


  alertmanager:
      container_name: alertmanager
      hostname: alertmanager
      image: prom/alertmanager
      # volumes:
      #   - ./alertmanager.conf:/etc/alertmanager/alertmanager.conf
      # command:
      #   - '--config.file=/etc/alertmanager/alertmanager.conf'
      ports:
        - 9093:9093
      networks:
        - dev
    

  prometheus:
    container_name: prometheus
    hostname: prometheus
    image: prom/prometheus
    volumes:
      # - ./prometheus.yml:/etc/prometheus/prometheus.yml
      # - ./alert_rules.yml:/etc/prometheus/alert_rules.yml
      - prometheus_data:/prometheus
    # command:
    #   - '--config.file=/etc/prometheus/prometheus.yml'
    links:
      - alertmanager:alertmanager
    ports:
      - 9090:9090
    networks:
      - dev
    

  grafana:
    container_name: grafana
    hostname: grafana
    image: grafana/grafana
    volumes:
      # - ./grafana_datasources.yml:/etc/grafana/provisioning/datasources/all.yaml
      # - ./grafana_config.ini:/etc/grafana/config.ini
      - grafana_data:/var/lib/grafana
    ports:
      - 3000:3000
    networks:
      - dev
    

  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - 2181:2181
    networks:
      - dev
    
  kafka:
    image: confluentinc/cp-kafka:latest
    container_name: kafka
    depends_on:
      - zookeeper
    ports:
      - 9092:9092
      - 29092:29092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
    networks:
      - dev

  postgres:
    image: postgres:latest
    container_name: postgres
    restart: always
    environment:
      - POSTGRES_PASSWORD=admin
      - POSTGRES_USER=root
      - POSTGRES_DB=mydb
    volumes:
      - pg-data:/var/lib/postgresql/data
    ports:
      - 5432:5432
    networks:
      - dev

  mongo:
    image: mongo:latest
    container_name: mongo
    restart: always
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=admin
    volumes:
      - mongo-data:/data/db
    ports:
      - 27017:27017
    networks:
      - dev

  mongo-express:
    container_name: mongo-express
    image: mongo-express:latest
    restart: always
    depends_on:
      - mongo
    environment:
      - ME_CONFIG_MONGODB_SERVER=mongo
      - ME_CONFIG_MONGODB_PORT=27017
      - ME_CONFIG_MONGODB_ENABLE_ADMIN=false
      - ME_CONFIG_MONGODB_AUTH_DATABASE=admin
      - ME_CONFIG_MONGODB_AUTH_USERNAME=admin
      - ME_CONFIG_MONGODB_AUTH_PASSWORD=admin
      - ME_CONFIG_BASICAUTH_USERNAME=admin
      - ME_CONFIG_BASICAUTH_PASSWORD=admin
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=admin
    ports:
      - 8081:8081
    networks:
      - dev

  redis:
    image: redis:latest
    restart: always
    ports:
      - '6379:6379'
    command: redis-server --save 20 1 --loglevel warning --requirepass admin
    volumes: 
      - cache:/data
    networks:
      - dev
```