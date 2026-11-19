FROM alpine
RUN apk add --no-cache curl
CMD ["sh", "-c", "if curl -f -m docker exec backend cat /tmp/notatka.txt5 -o /dev/null http://backend:5001/health; then echo 'backend: OK'; else echo 'backend: UNREACHABLE'; fi"]