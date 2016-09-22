# You have Dockerfile and Mac. What's next?

A very common situation: you have a Dockerfile and you're on Mac, and you need to run the app. And for some reason, it won't run locally, and you really need to use that Dockerfile and run the app in a container. What to do?

Simple!

Install [Homebrew](http://brew.sh/) if you still haven't. Also, install VirtuabBox.

After that, do

```bash
brew update
brew install docker-machine
docker-machine create --driver virtualbox default
brew install docker
brew services start docker-machine
cd ~/path/to/project # the path to your project where Dockerfile is located
docker build -t whatev ./
eval "${docker-machine env default}"
docker run whatev
```

then do

```bash
open `docker-machine ip default`
```

to open the app running in the container at port 80.

Done.
