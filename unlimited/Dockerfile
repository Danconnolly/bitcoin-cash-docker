FROM debian:stretch-slim

ENV BCH_VERSION 1.1.2.0
ENV BCH_URL https://www.bitcoinunlimited.info/downloads/BUcash-1.1.2.0-linux64.tar.gz
ENV BCH_SHA256 cd12e77ba3fb5d744a74d1f3d58fbd60c1f96a35db6ebcfcee21810d49e2e2e9

ADD $BCH_URL /tmp/bitcoin.tar.gz
RUN cd /tmp \
	&& echo "$BCH_SHA256  bitcoin.tar.gz" | sha256sum -c - \
	&& tar -xzvf bitcoin.tar.gz -C /usr/local --strip-components=1 --exclude=*-qt \
	&& rm bitcoin.tar.gz

RUN addgroup bitcoin && adduser --gecos "" --home /home/bitcoin --disabled-password --ingroup bitcoin bitcoin
ENV BCH_DATA /data
RUN mkdir "$BCH_DATA" \
	&& chown -R bitcoin:bitcoin "$BCH_DATA" \
	&& ln -sfn "$BCH_DATA" /home/bitcoin/.bitcoin \
	&& chown -h bitcoin:bitcoin /home/bitcoin/.bitcoin
VOLUME /data

COPY entrypoint.sh /entrypoint.sh
USER bitcoin
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8332 8333 18332 18333
CMD ["bitcoind"]

