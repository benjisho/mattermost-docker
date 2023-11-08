server {
  listen 80;
  server_name yourdomain.com;
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 ssl;
  server_name yourdomain.com;

  ssl_certificate /etc/nginx/certs/cert.crt;
  ssl_certificate_key /etc/nginx/certs/key.key;

  location / {
    client_max_body_size 50M;
    proxy_set_header Connection "";
    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Frame-Options SAMEORIGIN;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-Ssl on;
    proxy_buffers 256 16k;
    proxy_buffer_size 16k;
    proxy_read_timeout 600s;
    proxy_pass http://mattermost:8065;
  }
}
