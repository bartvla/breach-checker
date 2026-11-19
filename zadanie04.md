## 1 
breach-checker on  main [?] 
❯ curl -i http://localhost:5001/health                                              
HTTP/1.1 200 OK
Server: Werkzeug/3.1.9 Python/3.12.16
Date: Thu, 12 Nov 2026 15:12:28 GMT
Content-Type: application/json
Content-Length: 16
Access-Control-Allow-Origin: http://localhost:8080
Connection: close

{"status":"ok"}

breach-checker on  main [?] 
❯ docker logs backend        

Wczytano 89 hashy z /app/data/leaked-sha1.txt
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.22.0.2:5000
Press CTRL+C to quit
192.168.65.1 - - [12/Nov/2026 15:11:59] "GET /health HTTP/1.1" 200 -
192.168.65.1 - - [12/Nov/2026 15:12:28] "GET /health HTTP/1.1" 200 -

## 2

# działający backend
breach-checker on  main [?] 
❯ docker run --rm --network breach-net healthcheck-probe
backend: OK

# bez --network
❯ docker run --rm healthcheck-probe
backend: UNREACHABLE

# w sieci, ale backend zatrzymany
breach-checker on  main [?] 
❯ docker stop backend              
backend

breach-checker on  main [?] 
❯  docker run --rm healthcheck-probe      
backend: UNREACHABLE

## 3
# po restarcie
breach-checker on  main [?] 
❯ docker exec backend sh -c "echo notatka > /tmp/notatka.txt"


breach-checker on  main [?] 
❯ docker restart backend
backend

breach-checker on  main [?] took 3s 
❯ docker exec backend cat /tmp/notatka.txt
notatka

# po rm + run
breach-checker on  main [?] 
❯ docker rm -f backend
backend

breach-checker on  main [?] 
❯ docker run -d --name backend --network breach-net -p 5001:5000 -v ./data/leaked-sha1.txt:/app/data/leaked-sha1.txt:ro breach-checker-backend:v1
4b3a951fcb50219d4e81f8ee60675070db5a93a6a0daf8d3e9d3368fbb73417a

breach-checker on  main [?] 
❯ docker exec backend cat /tmp/notatka.txt
cat: /tmp/notatka.txt: No such file or directory

# wolumen
breach-checker on  main [?] 
❯ docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt"

breach-checker on  main [?] 
❯ docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt; cat /dane/historia.txt"
Thu Nov 12 15:27:08 UTC 2026
Thu Nov 12 15:27:15 UTC 2026