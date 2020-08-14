## Docker 启动命令
```
docker run -d --name nginx -p 80:80 -p 443:443 -v /usr/share/nginx:/usr/share/nginx:ro -v /etc/nginx:/etc/nginx:ro -v /var/log:/var/log nginx
```