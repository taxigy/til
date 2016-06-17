# Running a Meteor app on Vscale

[Vscale](https://vscale.io) is a cheap virtual hosting based in
Russia. It's only about $3 per container with some reasonable
512MB RAM and setup that's enough to run a little app. The other
side is that Vscale doesn't have nice tools like Heroku Pipelines
or, generally, CD tools that come out of the box. Anyway, in case
of a Meteor app, it's really easy to run it on a server.

Just make sure there's a server. If none, create one and wait
until it's active. Then SSH to it on behalf of root user:

```bash
$ ssh root@12.34.56.78
```

It's only possible if you added you public key to the list of
keys allowed to connect via SSH.

After that, make sure Git and Meteor are installed. The easiest
way is to

```bash
$ apt update
$ apt install git
$ apt install curl
$ curl https://install.meteor.com/ | sh
```

This will update the reference to PPA, and also install `git` and
`curl`. The latter then is used to download latest Meteor package
from the official source.

Now, the next thing is to allow this particular virtual machine
to pull changes from Github repo. To do that, generate a new pair
of keys on the newly created virtual machine:

```bash
$ cd ~/.ssh
$ ssh-keygen -t rsa -b 4096 -C "john@doe.com" # your email instead
$ cat ./id_rsa.pub
```

Copy content of the public key, go to repo setitngs on Github,
"Deploy keys", and add a new deploy key.

After that, simply do

```bash
$ cd ~
$ git clone git@github.com:johndoe/a-new-meteor-app.git # replace with actual reference to the repo
```

The repo should be cloned. Now go to the freshly cloned folder
and run the app:

```bash
$ cd ./a-new-meteor-app
$ meteor run
```

Done.
