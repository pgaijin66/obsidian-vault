
```yaml
version: '3'

volumes:
  data:
    driver: local

networks:
  dev:
    external: true

services:
  postgresdb:
    image: postgres
    container_name: postgres
    restart: always
    env_file: .env
    environment:
      - MONGO_INITDB_ROOT_USERNAME=${MONGO_INITDB_ROOT_USERNAME}
      - MONGO_INITDB_ROOT_PASSWORD=${MONGO_INITDB_ROOT_PASSWORD}
    volumes:
      - mongo-data:/data/db
    ports:
      - 27017:27017
    networks:
      - chhaano-dev

  mongo-express:
    container_name: chhaano-dev-mongo-express
    image: mongo-express
    restart: always
    depends_on:
      - mongo
    env_file: .env
    environment:
      - ME_CONFIG_MONGODB_SERVER=mongo
      - ME_CONFIG_MONGODB_PORT=27017
      - ME_CONFIG_MONGODB_ENABLE_ADMIN=false
      - ME_CONFIG_MONGODB_AUTH_DATABASE=admin
      - ME_CONFIG_MONGODB_AUTH_USERNAME=${ME_CONFIG_MONGODB_ADMINUSERNAME}
      - ME_CONFIG_MONGODB_AUTH_PASSWORD=${ME_CONFIG_MONGODB_ADMINPASSWORD}
      - ME_CONFIG_BASICAUTH_USERNAME=${ME_CONFIG_MONGODB_ADMINUSERNAME}
      - ME_CONFIG_BASICAUTH_PASSWORD=${ME_CONFIG_MONGODB_ADMINPASSWORD}
      - ME_CONFIG_MONGODB_ADMINUSERNAME=${ME_CONFIG_MONGODB_ADMINUSERNAME}
      - ME_CONFIG_MONGODB_ADMINPASSWORD=${ME_CONFIG_MONGODB_ADMINPASSWORD}
    ports:
      - 8081:8081
    networks:
      - chhaano-dev

  cache:
    image: redis:6.2-alpine
    restart: always
    ports:
      - '6379:6379'
    command: redis-server --save 20 1 --loglevel warning --requirepass admin
    volumes: 
      - cache:/data
    networks:
      - chhaano-dev
```