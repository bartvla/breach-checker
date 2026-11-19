## Zadanie 1 
### pełny wynik docker compose up -d --build
❯ docker compose up -d --build
[+] Building 1.2s (22/22) FINISHED
 => [internal] load local bake definitions                                                           0.0s
 => => reading from stdin 1.15kB                                                                     0.0s
 => [backend internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 199B                                                                 0.0s
 => [frontend internal] load build definition from dockerfile                                        0.0s
 => => transferring dockerfile: 302B                                                                 0.0s
 => [frontend internal] load metadata for docker.io/library/nginx:alpine                             0.0s
 => [backend internal] load metadata for docker.io/library/python:3.12-slim                          0.8s
 => [frontend internal] load .dockerignore                                                           0.0s
 => => transferring context: 65B                                                                     0.0s
 => [frontend 1/4] FROM docker.io/library/nginx:alpine                                               0.0s
 => [frontend internal] load build context                                                           0.0s
 => => transferring context: 120B                                                                    0.0s
 => CACHED [frontend 2/4] COPY index.html style.css script.js /usr/share/nginx/html/                 0.0s
 => CACHED [frontend 3/4] COPY nginx.conf /etc/nginx/conf.d/default.conf                             0.0s
 => CACHED [frontend 4/4] RUN date > /usr/share/nginx/html/build.txt                                 0.0s
 => [frontend] exporting to image                                                                    0.0s
 => => exporting layers                                                                              0.0s
 => => writing image sha256:0444831b6e5cd364e5a7ed692c497be0b09815d66dbd3759872aece927d821ae         0.0s
 => => naming to docker.io/library/breach-checker-frontend                                           0.0s
 => [frontend] resolving provenance for metadata file                                                0.0s
 => [backend internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                                      0.0s
 => [backend 1/5] FROM docker.io/library/python:3.12-slim@sha256:5e2bdb1b0d542d5039cffb8181c6593009  0.0s
 => [backend internal] load build context                                                            0.0s
 => => transferring context: 63B                                                                     0.0s
 => CACHED [backend 2/5] WORKDIR /app                                                                0.0s
 => CACHED [backend 3/5] COPY requirements.txt .                                                     0.0s
 => CACHED [backend 4/5] RUN pip install --no-cache-dir -r requirements.txt                          0.0s
 => CACHED [backend 5/5] COPY app.py .                                                               0.0s
 => [backend] exporting to image                                                                     0.0s
 => => exporting layers                                                                              0.0s
 => => writing image sha256:9fa2111b7a55543c6cc3c9e216378ede73ad0ffdfd31c3c952f6d2828d162cfd         0.0s
 => => naming to docker.io/library/breach-checker-backend                                            0.0s
 => [backend] resolving provenance for metadata file                                                 0.0s
[+] up 6/6
 ✔ Image breach-checker-frontend       Built                                                          1.3s
 ✔ Image breach-checker-backend        Built                                                          1.3s
 ✔ Network breach-checker_default      Created                                                        0.0s
 ✔ Container breach-checker-db-1       Healthy                                                        5.7s
 ✔ Container breach-checker-backend-1  Healthy                                                       11.3s
 ✔ Container breach-checker-frontend-1 Started                                                       11.5s

### wynik docker compose 
❯ docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    22 seconds ago   Up 17 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         22 seconds ago   Up 22 seconds (healthy)   5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   22 seconds ago   Up 11 seconds             0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

## Zadanie 2

- wynik po INSERT: {"found":true} - świezy INSERT dlatego baza zwraca nam ze wartość istnieje
- wynik po po down + up: {"found":true} - dane są zapisywane na wolumienie, więc usunięcie kontenera ich nie usuwa
- wynik po down -v : {"found":false} - po usunięciu wolumenu wartość nie istnieje w bazie, zwraca false
- W bazie jest 89 próbek które pochodzą z inicjalizacji bazy danych w pliku init.sql

## Zadanie 3
- docker compose z 3 replikami: 
❯ docker compose ps
NAME                        IMAGE                     COMMAND                  SERVICE    CREATED          STATUS                    PORTS
breach-checker-backend-1    breach-checker-backend    "python app.py"          backend    5 minutes ago    Up 5 minutes (healthy)    5000/tcp
breach-checker-backend-2    breach-checker-backend    "python app.py"          backend    11 seconds ago   Up 10 seconds (healthy)   5000/tcp
breach-checker-backend-3    breach-checker-backend    "python app.py"          backend    11 seconds ago   Up 10 seconds (healthy)   5000/tcp
breach-checker-db-1         postgres:16-alpine        "docker-entrypoint.s…"   db         5 minutes ago    Up 5 minutes (healthy)    5432/tcp
breach-checker-frontend-1   breach-checker-frontend   "/docker-entrypoint.…"   frontend   5 minutes ago    Up 5 minutes              0.0.0.0:8080->80/tcp, [::]:8080->80/tcp

- wynik nslookup backend:
❯ docker compose exec frontend nslookup backend
Server:         127.0.0.11
Address:        127.0.0.11:53

Non-authoritative answer:

Non-authoritative answer:
Name:   backend
Address: 172.23.0.5
Name:   backend
Address: 172.23.0.3
Name:   backend
Address: 172.23.0.6

- Logi sprzed restartu:
❯ docker compose logs backend
backend-2  | W bazie jest 89 hashy
backend-2  |  * Serving Flask app 'app'
backend-2  |  * Debug mode: off
backend-2  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-2  |  * Running on all addresses (0.0.0.0)
backend-2  |  * Running on http://127.0.0.1:5000
backend-2  |  * Running on http://172.23.0.6:5000
backend-2  | Press CTRL+C to quit
backend-3  | W bazie jest 89 hashy
backend-3  |  * Serving Flask app 'app'
backend-3  |  * Debug mode: off
backend-3  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-3  |  * Running on all addresses (0.0.0.0)
backend-3  |  * Running on http://127.0.0.1:5000
backend-3  |  * Running on http://172.23.0.5:5000
backend-3  | Press CTRL+C to quit
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:58:41] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:58:46] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:58:51] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:58:56] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:01] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:06] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:58:41] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:58:46] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:58:51] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:58:56] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:01] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:06] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:12] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:17] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:22] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:27] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:32] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:37] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:42] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 12:59:47] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:12] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:17] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:22] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:27] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:32] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:37] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:42] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 12:59:47] "GET /health HTTP/1.1" 200 -
backend-1  | W bazie jest 89 hashy
backend-1  |  * Serving Flask app 'app'
backend-1  |  * Debug mode: off
backend-1  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-1  |  * Running on all addresses (0.0.0.0)
backend-1  |  * Running on http://127.0.0.1:5000
backend-1  |  * Running on http://172.23.0.3:5000
backend-1  | Press CTRL+C to quit
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:15] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:53:18] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:25] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:40] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:45] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:50] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:56] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:01] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:06] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:11] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:16] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:21] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:26] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:31] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:36] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:42] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:47] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:52] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:57] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:02] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:07] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:12] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:18] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:23] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:28] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:33] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:38] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:43] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:48] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:53] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:58] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:03] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:09] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:14] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:19] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:24] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:29] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:34] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:39] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:44] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:49] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:55] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:00] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:05] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:10] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:15] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:25] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:41] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:46] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:51] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:56] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:01] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:07] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:12] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:17] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:22] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:27] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:32] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:37] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:42] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:48] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:53] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:58] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:03] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:08] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:13] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:18] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:23] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:28] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:33] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:39] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:44] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:49] "GET /health HTTP/1.1" 200 -

- Logi po restarcie:
❯ docker compose logs backend
backend-2  | W bazie jest 89 hashy
backend-2  |  * Serving Flask app 'app'
backend-2  |  * Debug mode: off
backend-2  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-2  |  * Running on all addresses (0.0.0.0)
backend-2  |  * Running on http://127.0.0.1:5000
backend-2  |  * Running on http://172.23.0.5:5000
backend-2  | Press CTRL+C to quit
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:03:48] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:03:53] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:03:58] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:03] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:08] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-3  | W bazie jest 89 hashy
backend-3  |  * Serving Flask app 'app'
backend-3  |  * Debug mode: off
backend-3  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-3  |  * Running on all addresses (0.0.0.0)
backend-3  |  * Running on http://127.0.0.1:5000
backend-3  |  * Running on http://172.23.0.6:5000
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:13] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:18] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:23] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | Press CTRL+C to quit
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:03:48] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:28] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:33] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:39] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:44] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:49] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:54] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:03:53] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:03:58] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:03] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:08] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:13] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:18] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:23] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:04:59] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:04] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:09] "GET /health HTTP/1.1" 200 -
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | W bazie jest 89 hashy
backend-1  |  * Serving Flask app 'app'
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-1  |  * Debug mode: off
backend-1  | WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
backend-1  |  * Running on all addresses (0.0.0.0)
backend-1  |  * Running on http://127.0.0.1:5000
backend-1  |  * Running on http://172.23.0.3:5000
backend-1  | Press CTRL+C to quit
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:15] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:14] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:19] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:25] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:53:18] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:25] "GET /health HTTP/1.1" 200 -
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:40] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:45] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:50] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:53:56] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:01] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:06] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:11] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:16] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:21] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:26] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:31] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:36] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:42] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:47] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:52] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:54:57] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:02] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:07] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:28] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:33] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:39] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:44] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:49] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:54] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:04:59] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:12] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:18] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:23] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:28] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:33] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:38] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:04] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:09] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:14] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:19] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:25] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:43] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:48] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:53] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:55:58] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:03] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:09] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:14] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:19] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:24] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:29] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:34] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:39] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:44] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:49] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:56:55] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:00] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:05] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:10] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:15] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:25] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:41] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:46] "GET /health HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:51] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:57:56] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:01] "GET /health HTTP/1.1" 200 -
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-3  | check: hash=bc454...
backend-3  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:30] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:35] "GET /health HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:40] "GET /health HTTP/1.1" 200 -
backend-2  | check: hash=bc454...
backend-2  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-3  | 127.0.0.1 - - [19/Nov/2026 13:05:45] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:30] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:35] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:40] "GET /health HTTP/1.1" 200 -
backend-2  | 127.0.0.1 - - [19/Nov/2026 13:05:45] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:07] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:12] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:17] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:22] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:27] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:32] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:37] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:42] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:48] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:53] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:58:58] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:03] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:08] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:13] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:18] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:23] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 12:59:25] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:28] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:33] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:39] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:44] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:49] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:54] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 12:59:59] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:04] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:09] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:14] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:19] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:24] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:30] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:40] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:00:44] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:45] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:50] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:00:55] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:00] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:05] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:10] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:15] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:21] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:26] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:31] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:36] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:41] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:46] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:51] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:01:56] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:01] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:06] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:12] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:17] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:22] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:27] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:32] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:37] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:42] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:47] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:53] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:02:58] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:03] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:08] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:13] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:18] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:23] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:28] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:33] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:39] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:44] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:49] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:54] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:03:59] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:04] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:09] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:11] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:12] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:14] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:19] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:24] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:04:25] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:29] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:35] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:40] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:45] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:50] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:04:55] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:00] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:05] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:10] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:15] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:20] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:26] "GET /health HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:05:26] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-1  | check: hash=bc454...
backend-1  | 172.23.0.4 - - [19/Nov/2026 13:05:27] "POST /check HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:31] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:36] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:41] "GET /health HTTP/1.1" 200 -
backend-1  | 127.0.0.1 - - [19/Nov/2026 13:05:46] "GET /health HTTP/1.1" 200 -

nginx tłumaczy nazwę backend na adresy raz, przy starcie i wczytaniu konfiguracji. 
Gdy frontend startował, replika była jedna, więc zapamiętał dokładnie jej jeden adres i trzymał się go, mimo że potem doszły dwie kolejne
restart frontend wymusza ponowne odczytanie DNS, nginx dostaje wtedy wszystkie trzy adresy i rozkłada między nie ruch.

port na hoście może zająć tylko jeden proces, więc druga replika nie wstałaby z błędem ze port jest zajęty