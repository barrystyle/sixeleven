# 611 (SixEleven) docker iamge

FROM debian:jessie

MAINTAINER Florian Fuessl <flo@degnet.de>
ENV REFRESHED_AT 20160321

RUN groupadd -r sixeleven && useradd -r -m -g sixeleven sixeleven

# grab the latest 611 source code from git, compile, install and clean up
RUN set -ex \
	&& apt-get update \

	# install dependencies
	&& apt-get install -y --no-install-recommends ca-certificates wget \
		build-essential libssl-dev libboost-all-dev git \

	# grab gosu for easy step-down from root
	&& gpg --keyserver pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
	&& wget -qO /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture)" \
	&& wget -qO /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture).asc" \
	&& gpg --verify /usr/local/bin/gosu.asc \
	&& rm /usr/local/bin/gosu.asc \
	&& chmod +x /usr/local/bin/gosu \

	# compile and install 611
	&& cd /usr/src \
	&& git clone https://github.com/fflo/sixeleven.git sixeleven \
	&& cd sixeleven/contrib \
	&& ./build-static-with-db48.sh \
	&& cp ../src/611d /usr/local/bin \
	&& strip /usr/local/bin/611d \

	# cleanup
	&& cd ../.. && rm -rf /usr/src/sixeleven \
	&& apt-get -y purge build-essential libssl-dev libboost-all-dev git \
	&& apt-get -y autoremove \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/*

ENV SIXELEVEN_DATA /sixeleven
RUN mkdir $SIXELEVEN_DATA \
	&& chown sixeleven:sixeleven $SIXELEVEN_DATA \
	&& ln -s $SIXELEVEN_DATA /home/sixeleven/.611
VOLUME ["/sixeleven"]

COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8661 8663
CMD ["611d"]
