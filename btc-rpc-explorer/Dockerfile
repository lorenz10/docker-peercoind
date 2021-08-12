FROM node:16.6.1-alpine3.12
RUN apk --no-cache add git
RUN git clone --single-branch --branch master \
    https://github.com/lorenz10/btc-rpc-explorer.git btc-rpc-explorer
WORKDIR /btc-rpc-explorer
RUN echo -e 'BTCEXP_BITCOIND_HOST=host.docker.internal \
\nBTCEXP_BITCOIND_PORT=9904 \nBTCEXP_BITCOIND_USER=<USER> \
\nBTCEXP_BITCOIND_PASS=<PASSWORD> \nBTCEXP_BITCOIND_RPC_TIMEOUT=10000 \
\nBTCEXP_ADDRESS_API=electrum \
\nBTCEXP_ELECTRUM_SERVERS=tcp://host.docker.internal:50001 \
\nBTCEXP_RPC_CONCURRENCY=10 \nBTCEXP_SLOW_DEVICE_MODE=true \
\nBTCEXP_PRIVACY_MODE=true \nBTCEXP_NO_RATES=true \
\nBTCEXP_HOST=0.0.0.0 \nBTCEXP_PORT=80' > .env
RUN npm install
CMD npm start
EXPOSE 80
