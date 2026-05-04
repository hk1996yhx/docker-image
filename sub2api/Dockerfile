FROM weishaw/sub2api:latest
USER root
RUN apk add --no-cache redis
RUN mkdir -p /data && chown -R sub2api:sub2api /data
RUN cat > /app/redis.conf <<'EOF'
bind 127.0.0.1
protected-mode no
port 6379
dir /data
appendonly yes
save ""
logfile ""
daemonize no
EOF
RUN cat > /app/123.sh <<'EOF'
#!/bin/sh
set -e
: "${REDIS_URL:=127.0.0.1:6379}"
: "${REDIS_ADDR:=127.0.0.1:6379}"
: "${REDIS_HOST:=127.0.0.1}"
: "${REDIS_PORT:=6379}"
: "${REDIS_ENABLE_TLS:=false}"
: "${REDIS_TLS:=false}"
export REDIS_URL REDIS_ADDR REDIS_HOST REDIS_PORT REDIS_ENABLE_TLS REDIS_TLS
mkdir -p /app/data /data
chown -R sub2api:sub2api /app/data /data
redis-server /app/redis.conf &
for i in $(seq 1 60); do
if redis-cli -h 127.0.0.1 -p 6379 ping 2>/dev/null | grep -q PONG; then
exec /app/docker-entrypoint.sh "$@"
fi
sleep 1
done
echo "redis did not become ready"
exit 1
EOF
RUN chmod +x /app/123.sh
EXPOSE 8080
ENTRYPOINT ["/app/123.sh"]
CMD ["/app/sub2api"]
