FROM redis:4
ARG PORT=7002
ADD redis.conf /usr/local/redis/
RUN sed -i -e "s/6379/$PORT/g" /usr/local/redis/redis.conf
WORKDIR /data
VOLUME /data
CMD [ "redis-server", "/usr/local/redis/redis.conf", "--slaveof 0.0.0.0 7001" ]