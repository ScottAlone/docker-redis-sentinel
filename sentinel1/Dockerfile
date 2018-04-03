FROM redis:4
ADD sentinel.conf /etc/redis/sentinel.conf
RUN chown redis:redis /etc/redis/sentinel.conf
CMD ["redis-server", "/etc/redis/sentinel.conf", "--sentinel"]