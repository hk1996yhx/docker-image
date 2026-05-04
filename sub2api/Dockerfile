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
exec /app/docker-entrypoint.sh "$@"
EOF
RUN chmod +x /app/123.sh
EXPOSE 8080
ENTRYPOINT ["/app/123.sh"]
CMD ["/app/sub2api"]
