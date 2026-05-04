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
READY=0
for i in $(seq 1 60); do
if redis-cli -h 127.0.0.1 -p 6379 ping 2>/tmp/redis-error.log | grep -q PONG; then
READY=1
break
fi
echo "waiting redis... $i"
cat /tmp/redis-error.log || true
sleep 1
done
if [ "$READY" != "1" ]; then
echo "redis did not become ready"
ps aux
netstat -lntp || true
exit 1
fi
echo "redis is ready"
redis-cli -h 127.0.0.1 -p 6379 ping
echo "effective redis env:"
env | grep '^REDIS' || true
echo "starting sub2api..."
exec /app/docker-entrypoint.sh "$@"
EOF
RUN chmod +x /app/123.sh
EXPOSE 8080
ENTRYPOINT ["/app/123.sh"]
CMD ["/app/sub2api"]
