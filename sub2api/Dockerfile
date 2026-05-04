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
echo "starting embedded redis..."
mkdir -p /app/data /data
chown -R sub2api:sub2api /app/data /data
redis-server /app/redis.conf &
REDIS_PID=$!
echo "redis pid: $REDIS_PID"
for i in $(seq 1 30); do
if redis-cli -h 127.0.0.1 -p 6379 ping >/tmp/redis-ping.log 2>&1; then
echo "redis is ready"
cat /tmp/redis-ping.log
exec /app/docker-entrypoint.sh "$@"
fi
echo "waiting redis... $i"
cat /tmp/redis-ping.log || true
sleep 1
done
echo "redis failed to start"
ps aux
netstat -lntp || true
exit 1
EOF
RUN chmod +x /app/123.sh
EXPOSE 8080
ENTRYPOINT ["/app/123.sh"]
CMD ["/app/sub2api"]
