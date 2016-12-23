:octocat: [denlyou/TroubleShooting](https://github.com/denlyou/TroubleShooting)
> Trouble Shooting

## nodejs에서 sub domain별 다른 express 프로젝트를 연결하기
- 하나의 http(80포트)로 request를 분배
- nodejs-nginx reverse proxy

#### case :
`nodejs에서 서브도메인을 이용하여 여러 express 프로젝트를 연결하고 싶었다. 기존의 웹서버(apache, nginx)에서는 vertual host를 이용하여 사용하고 있었다. nodejs에서도 vhost모듈을 이용해 분할 해보려했으나 생각처럼 잘 분할할 수 없었다. (초보라 실력 미달일수도 있음...) `

- nodejs만으로 해결해야할 필요가 없었음
- nginx나 apache의 reverse proxy 설정으로 충분히 해결 가능해짐

##### nginx 설정 파일 (/etc/nginx/sites-available/nodeserver)
```
server {
	listen 80;
	server_name sub.domain.co.kr;

	location / {
        proxy_pass http://127.0.0.1:3000/;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
	}
}
```

- 설정을 조금만 더 손보면 MEAN stack과 LAMP를 적절히 병행도 가능
- 주의 : express 프로젝트라면 `app.set('trust proxy', true)` 설정 추가
    - request의 header ip 주소 문제


##### 심화 설정의 예 (주 정적 파일들 nginx로 그외 3000포트(nodejs)로 넘김)
```
server {
  listen 80;
  listen [::]:80;

  server_name sub.domain.co.kr;

  root /var/www/html/sub.domain;
  index index.php index.html;

  location ~* \.(html|js|css|jpe?g|png|gif|ico|zip|pdf|mp3|mp4|avi|)$ {
    root /var/www/html/sub.domain;
  }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/run/php/php7.0-fpm.sock;
  }

  location / {
		proxy_pass http://127.0.0.1:3000/;
		proxy_redirect off;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $http_host;
		proxy_set_header X-NginX-Proxy true;
  }
}
```

---

- [nginx 설정 파일의 location expression](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms)
