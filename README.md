### mercure
---
https://github.com/dunglas/mercure

```go
const url = new URL('https://example.com/hub');
url.searchParams.append('topic', 'https://example.com/books/{id}');
url.searchParams.append('topic', 'https://example.com/users/dunglas');

const eventSource = new EventSource(url);
eventSource.onmessage = e => console.log(e);

fetch('https://example.com/book/1')
  .then(response => {
    const hubUrl = response.headers.get('Link').match(/<([^>]+)>;\s+rel=(?:mercure|"[^"]*mercure[^"]*")/)[1];
  });
```

```js
const https = require('https');
const querystring = require('querystring');

const postData = querystring.stringify({
  'topic': 'https://example.com/books/1',
  'data': JSON.stringify({ foo: 'updated value' }),
});

const req = https.request({
  hostname: 'example.com',
  port: '443',
  path: '/hub',
  method: 'POST',
  headers: {
    Authorization: 'Bearer <valid-jwt-token>',
    'Content-Type': 'application/x-www-form-urlencoded',
    'Content-Length': Buffer.byteLength(postData),
  }
},  );
req.write(postData);
req.end();
```

```
JWT_KEY='myJWTKey' ADDR=':3000' DEMO=1 ALLOW_ANONYMOUS=1 CORS_ALLOWED_ORIGINS=* PUBLISH_ALLOWED_ORIGINS='http://localhost:3000' ./mercure
JWT_KEY='myJWTKey' ACME_HOSTS='example.com' ./mercure

docker run \
  -e JWT_KEY='myJWTKey' -e DEMO=1 -e ALLOW_ANONYMOUS=1 -e PUBLISH_ALLOWED_ORIGINS='http://localhost' \
  -p 80:80 \
  dunglas/mercure
  
docker run \
  -e JWT_KEY='myJWTKey' -e ACME_HOSTS='example.com' \
  -p 80:80 -p 443:443 \
  dunglas/mercure

helm install stable/mercure
```

```
server {
  listen 80 ssl http2;
  listen [::]:80 ssl http2;
  
  ssl_certificate /path/to/ssl/cert.crt;
  ssl_certificate_key /path/to/ssl/cert.key;
  
  location / {
    proxy_pass http://url-of-your-mercure-hub;
    proxy_read_timeout 24h;
    proxy_http_version 1.1;
    proxy_set_header Connection "";
  }
}
```


