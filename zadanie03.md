## 1
```
❯ docker inspect --format '{{json .Config.Labels}}' bartosz:v1
{"autor":"Bartosz Prosianowski","data":"2026-10-14","maintainer":"Bartosz Prosianowski"}

breach-checker on  zadanie03 
❯ docker inspect --format '{{json .Config.Labels}}' bartosz:latest
{"autor":"Bartosz Prosianowski","data":"2026-10-14","maintainer":"Bartosz Prosianowski"}

 docker history bartosz:latest
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
f7248266e1a9   5 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      5 minutes ago   RUN /bin/sh -c date > /usr/share/nginx/html/…   29B       buildkit.dockerfile.v0
<missing>      5 minutes ago   COPY . /usr/share/nginx/html # buildkit         33.2kB    buildkit.dockerfile.v0
<missing>      5 minutes ago   LABEL autor=Bartosz Prosianowski data=2026-1…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     RUN /bin/sh -c set -x     && apkArch="$(cat …   49.3MB    buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 20-envsubst-on-templates.sh /docker-ent…   3.03kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 10-listen-on-ipv6-by-default.sh /docker…   2.14kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     RUN /bin/sh -c set -x     && addgroup -g 101…   4.66MB    buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NGINX_VERSION=1.31.8                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ADD alpine-minirootfs-3.24.3-aarch64.tar.gz …   8.66MB    buildkit.dockerfile.v0

❯ docker history bartosz:v1         
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
f7248266e1a9   5 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      5 minutes ago   RUN /bin/sh -c date > /usr/share/nginx/html/…   29B       buildkit.dockerfile.v0
<missing>      5 minutes ago   COPY . /usr/share/nginx/html # buildkit         33.2kB    buildkit.dockerfile.v0
<missing>      5 minutes ago   LABEL autor=Bartosz Prosianowski data=2026-1…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     RUN /bin/sh -c set -x     && apkArch="$(cat …   49.3MB    buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 30-tune-worker-processes.sh /docker-ent…   4.62kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 20-envsubst-on-templates.sh /docker-ent…   3.03kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 15-local-resolvers.envsh /docker-entryp…   389B      buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY 10-listen-on-ipv6-by-default.sh /docker…   2.14kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     COPY docker-entrypoint.sh / # buildkit          1.62kB    buildkit.dockerfile.v0
<missing>      2 weeks ago     RUN /bin/sh -c set -x     && addgroup -g 101…   4.66MB    buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ENV NGINX_VERSION=1.31.8                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago     ADD alpine-minirootfs-3.24.3-aarch64.tar.gz …   8.66MB    buildkit.dockerfile.v0

rozmiar 0B mają instrukcje zmieniające tylko metadane (LABEL, ENV, CMD, EXPOSE itd.), bo nie zapisują plików w systemie plików.

Widać tylko mój maintainer, poniewaz etykiety się scalają, a przy tym samym kluczu nowsza wartość z LABEL nadpisuje tę z obrazu bazowego.
```

## 2
```
Kontener:frontend
Thu Nov  5 15:09:20 UTC 2026

Kontener:frontend2
Thu Nov  5 15:09:20 UTC 2026

RUN wykonuje się w czasie budowania obrazu, a nie przy starcie kontenera. Wynik polecenia date jest zapisany na stałe w warstwie obrazu jako plik build.txt i jest taki sam dla dwóch uruchomionych kontenerów.
```

## 3
```
root   /usr/share/nginx/html;

nginx serwuje pliki z tego katalogu, dlatego frontend kopiujemy właśnie tam.
```

## 4
```
breach-checker on  zadanie03 
❯ docker exec frontend ls /usr/share/nginx      
dockerfile
html
index.html
script.js
style.css
zadanie03.md

breach-checker on  zadanie03 
❯ docker exec frontend ls /usr/share/nginx/html
50x.html
build.txt
index.html
```

## 5
```
breach-checker on  zadanie03 
❯ docker rmi breach-checker-frontend:latest
Untagged: breach-checker-frontend:latest

breach-checker on  zadanie03 
❯ docker rmi breach-checker-frontend:v2    
Untagged: breach-checker-frontend:v2
Deleted: sha256:f80858c91f0dac3eb93c5750e2c04ec22af4720b4be145897960379f0625ac34

docker rmi usuwa obraz z dysku dopiero po usunięciu ostatniego tagu wskazującego na dany image id.
```