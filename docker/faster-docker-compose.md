# Make Docker Compose faster

How to make `docker-compose up` run faster? Add these lines to /etc/hosts:

```
127.0.0.1 localunixsocket
127.0.0.1 localunixsocket.lan
127.0.0.1 localunixsocket.local
```

[Ref](https://github.com/docker/compose/issues/3419#issuecomment-257933259).
