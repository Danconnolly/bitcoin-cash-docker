FROM debian:stretch-slim

ENV BCH_VERSION 0.16.1
ENV BCH_URL https://download.bitcoinabc.org/0.16.1/linux/bitcoin-abc-0.16.1-x86_64-linux-gnu.tar.gz
ENV BCH_SHA256 aa561770a850a8cdc1872eabc67502e5533e8ad16bafc20ea14700811426d538

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

