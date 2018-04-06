FROM debian:stretch-slim

ENV BCH_VERSION 0.17.0
ENV BCH_URL https://download.bitcoinabc.org/${BCH_VERSION}/linux/bitcoin-abc-${BCH_VERSION}-x86_64-linux-gnu-debug.tar.gz
ENV BCH_SHA256 14edfe40edca9f614f34e90f5c4dc4dea4e496361f4bdd8d02197a44dbd34a6c

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

