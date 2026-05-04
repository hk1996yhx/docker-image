FROM weishaw/sub2api:latest
USER root
RUN apk add --no-cache redis
RUN mkdir -p /data && chown -R sub2api:sub2api /data
RUN cat > /app/redis.conf <<'EOF'
bind 127.0.0.1
protected-mode yes
port 6379
dir /data
appendonly yes
save ""
EOF
RUN cat > /app/123.sh <<'EOF'
#!/bin/sh
set -e
mkdir -p /app/data /data
chown -R sub2api:sub2api /app/data /data
redis-server /app/redis.conf --daemonize yes
for i in $(seq 1 30); do
redis-cli -h 127.0.0.1 -p 6379 ping >/dev/null 2>&1 && break
sleep 1
done
redis-cli -h 127.0.0.1 -p 6379 ping
exec /app/docker-entrypoint.sh "$@"
EOF
RUN chmod +x /app/123.sh
EXPOSE 8080
ENTRYPOINT ["/app/123.sh"]
CMD ["/app/sub2api"]
