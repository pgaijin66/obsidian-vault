```
version: '3'

volumes:
  mongo-data:
    driver: local

  pg-data:

  cache:
    driver: local


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
  #     REDIS_URL: "cache:6379"
  #   networks:
  #     - dev

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

  cache:
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