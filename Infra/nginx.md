## 도입 계기

제가 진행한 [프로젝트](https://github.com/prgrms-web-devcourse/Team-Saiko-BidMarket-BE)에서는 JWT토큰을 기반으로 동작하는 기능이 많아 보안에 신경을 써야 했고, 토큰의 탈취에 대비하기 위해 SSL적용이 필요하다 판단했습니다.

SSL을 적용하는 방법에는 스프링 부트의 내장 톰캣에 적용하는 방법과 Nginx프록시 서버를 앞단에 두고 SSL인증을 적용하는 방법 두가지가 있었습니다.

후자의 경우 Let’s Encrypt를 통해 간편하게 SSL인증서를 발급받고 갱신하는 과정을 자동화 할 수 있고, SSL Termination을 통해 Nginx서버가 암호해독을 하도록 만듦으로써 스프링 애플리케이션 서버의 부담을 줄일 수 있다는 장점이 있었습니다. 이에따라 Nginx를 도입하여 SSL인증을 적용하였습니다.

## Nginx

Nginx이전에 자주 사용되던 Apach서버의 경우 요청마다 프로세스를 생성하는 방식으로 메모리와 CPU 문맥교환으로 인한 오버헤드로 인해 구조적 한계가있었습니다(C10K 문제). Nginx는 이러한 문제를 해결하기 위해 비동기 이벤트 기반의 구조로 설계되어, 다수의 연결을 효과적으로 처리할 수 있습니다. 주로 웹 애플리케이션 서버 앞단에 두어 프록시 서버로 활용합니다.

### Nginx를 프록시 서버로 두었을때의 장점

1. WAS를 내부망에 둠으로써 직접적인 접근을 차단하여 보안성을 강화할 수 있습니다.
2. 웹서버가 클라이언트 요청을 캐시하여 동일한 요청에 대해 성능상의 이점을 얻을 수 있습니다.
3. SSL Termination을 활용하여 백엔드 서버의 부담을 줄일 수 있습니다.

**SSL Termination**

![Untitled](https://avinetworks.com/wp-content/uploads/2018/12/ssl-termination-diagram.png)

그림과 같이 로드 밸런서가 SSL을 처리하고 로드밸런서와 웹서버는 HTTP통신을 하도록 만들어 웹서버의 SSL 처리에 대한 부담을 줄일수 있습니다.  

## Nginx 설치 과정

```
#nginx 설치
sudo apt-get update
sudo apt install nginx -y 

#nginx 설치 확인
nginx -v

#nginx 동작 확인
sudo service nginx status
```

## Let’s Encrypt(Certbot)를 통한 SSL 인증서 발급

```
# 설치
sudo add-apt-repository ppa:certbot/certbot$ sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx -y
sudo certbot certonly --nginx -d {도메인 명}

# SSL 생성 확인
ls -al /etc/letsencrypt/live/{도메인 명}

# 인증서 갱신 가능 확인
sudo certbot renew --dry-run

# 인증서 갱신
sudo certbot renew
```

`certbot`을 설치한 후 위의 과정을 통해 도메인에 대한 SSL인증서를 발급하면, 다음과 같이 4개의 .pem 파일과 1개의 readme 파일이 생성됩니다.

```
README	cert.pem  chain.pem  fullchain.pem  privkey.pem
```

README 파일을 열어보면 다음과 같이 각 파일들에 대한 설명을 확인할 수 있습니다.

```bash
`privkey.pem`  : the private key for your certificate.
`fullchain.pem`: the certificate file used in most server software.
`chain.pem`    : used for OCSP stapling in Nginx >=1.3.7.
`cert.pem`     : will break many server configurations, and should not be used
                 without reading further documentation (see link below).
```

nginx의 공식문서에 따르면 HTTP서버를 구성하기 위해서는 다음과 같이 listening socket에 ssl 파라미터를 추가하고, [server certificate](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_certificate) 과 [private key](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_certificate_key) 파일의 위치를 지정하여야 합니다.

```bash
server {
    listen              443 ssl;
    server_name         www.example.com;
    ssl_certificate     www.example.com.crt;
    ssl_certificate_key www.example.com.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ...
}
```

nginx 설정을 위해 `/etc/nginx/` 폴더의 `nginx.conf` 파일을 수정해도 되지만, `nginx.conf`파일을 살펴보면 include를 통해 외부의 설정들을 가져오는 부분이 있습니다. 이를 활용하여 설정들을 잘 모듈화하여 관리할 수 있습니다.

```bash
	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;

```

저는 `sites-enabled` 폴더에 nginx 설정파일을 다음과 같이 구성하였습니다.

```bash
#1
server {
            listen 80;
            server_name bidmarket-api.shop www.bidmarket-api.shop;
            return 301 https://bidmarket-api.shop$request_uri;
}

#2
server {
            listen 443 ssl http2;
            server_name bidmarket-api.shop www.bidmarket-api.shop;
        
            ssl_certificate /etc/letsencrypt/live/{도메인 주소}/fullchain.pem;
            ssl_certificate_key /etc/letsencrypt/live/{도메인 주소}/privkey.pem;
            
            #3
            location / {
                  proxy_pass http://localhost:8080;
                  proxy_set_header Host $http_host;
                  proxy_set_header X-Real-IP $remote_addr;
                  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                  proxy_set_header X-Forwarded-Proto $scheme;
        
            }
            
            #4
            location /ws-stomp {
                  proxy_pass http://localhost:8080;
                  proxy_http_version 1.1;
                  proxy_set_header Upgrade $http_upgrade;
                  proxy_set_header Connection "Upgrade";
                  proxy_set_header Host $host;
            }
}
```

1. http로 요청이 들어오더라도 https로 리다이렉트 되도록 설정하였습니다.
2. listening 소켓에 ssl 키워드를 추가하고, ssl 인증을 위한 .pem 파일의 위치를 지정하였습니다.
3.  proxy_pass를 통해 `/` 로 시작하는 path로 들어오는경우 `[http://localhost:8080](http://localhost:8080)` 로 요청을 돌리게 설정하였습니다.
4. 웹소켓을 사용하기 때문에 nginx 공식문서를 참고하여 stomp 엔드포인트에 대한 설정을 하였습니다.
    1. [https://www.nginx.com/blog/websocket-nginx/](https://www.nginx.com/blog/websocket-nginx/)
