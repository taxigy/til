# Mongo DB on Ubuntu 14.04

On [Vscale](https://vscale.io), on a simple Ubuntu 14.04 LTS server.

After having a new Ubuntu VM created with IP `1.2.3.4`, ssh to
it and do

```bash
$ ssh root@1.2.3.4
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
$ echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
$ cat /etc/apt/sources.list.d/mongodb-org-3.2.list
$ sudo apt-get update
$ sudo apt-get install -y mongodb-org
$ sudo service mongod start
$ sudo iptables -I INPUT -p tcp -m tcp --dport 27017 -j ACCEPT
$ mongo
```

Then create a new user:

```javascript
use whatevs;
db.createUser({
  user: 'rishat',
  pwd: 'supersecurepassword',
  roles: [{
    role: 'readWrite',
    db: 'whatevs'
  }]
});
```

Then go:

```bash
vi /etc/mongod.conf
```

And replace old value of `bind_ip` parameter (presumably 127.0.0.1) to
`0.0.0.0`. And then

```bash
$ sudo service mongod restart
```

After that, it's possible to use a connection string, like

```bash
$ mongo mongodb://rishat:supersecurepassword@1.2.3.4:27017/whatevs
```

And do all the read and write operations against any collection in the
database.
