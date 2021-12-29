FROM node:16.6.1-alpine3.12

RUN apk --no-cache add git \
    && git clone --single-branch --branch release-3 \
    https://github.com/n3srl/btc-rpc-explorer.git \
    btc-rpc-explorer

WORKDIR /btc-rpc-explorer

RUN npm install

COPY [".env",".env"]

CMD npm start

EXPOSE 80
