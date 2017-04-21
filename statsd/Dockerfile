FROM node:6

RUN git clone https://github.com/etsy/statsd.git /src/statsd

ADD ./config.json /src/statsd/config.json
CMD node /src/statsd/stats.js /src/statsd/config.json


# StatsD UDP port
EXPOSE  8125/udp

# StatsD Management port
EXPOSE  8126