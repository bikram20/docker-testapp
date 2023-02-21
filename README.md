# docker-testapp

### To run this in a DigitalOcean Docker 1-click droplet, first set up a non-root user, and copy over the .docker folder (need it for compose plugin)
```
sudo adduser ubuntu --disabled-password
cp -r /root/.docker /home/ubuntu/
chown -R ubuntu:ubuntu /home/ubuntu/.docker
su - ubuntu
```

**Create a firewall rule on the droplet (from DigitalOcean cloud console - droplet - networking) to permit only SSH inbound.** This is because we do not want to expose the DB or flask http application to public network. 

Next, verify that docker works.
```
docker version
docker compose version
```

### Clone the repo, and run.
```
git clone https://github.com/bikram20/docker-testapp.git
cd docker-testapp
docker compose up -d
```


