# docker-testapp

Install the app on a docker 1-click droplet.

### Set up a non-root user
```
sudo adduser ubuntu --disabled-password
sudo usermod -aG docker ubuntu
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

### Clone the repo, and run as ubuntu user
```
git clone https://github.com/bikram20/docker-testapp.git
cd docker-testapp
docker compose up
```

### Test the application
As the application is not exposed to internet (firewall blocking), we need to create a SSH tunnel from localhost to access it.

From another terminal:
```
ssh root@<droplet> -L 5001:localhost:5000
```

Now the web application should be available on http://localhost:5001 on your local laptop.

Save some content. Verify that those content are saved even when you shutdown (docker compose down) the containers and bring them up (docker compose up). This is because those content are in a named volume used by postgres.

### Migrating the application to another docker host

For this, we do not need to copy our docker-compose.yml. We can simply clone the repo in another host and rebuild the application. **However, we need the database volume.**

#### Option 1 (Postgres backup utility)

TBD. The goal of this exercise is to demonstrate the named volume backup capability. Hence skipping this part for now.

#### Option 2 (Volume backup)

We will use the following script written by Bret Fisher.

```
git clone https://github.com/BretFisher/docker-vackup.git
cd docker-vackup
./vackup
```

To export the volume as a tar gzipped file:

```
./vackup export docker-testapp_db_data docker-testapp_db_data.tar.gz
```

Now we are ready to copy it over to another host and run the app on that host.


### Create new host and copy the gzipped file to the new host
To use scp between newly created droplets, you will need to use 'ssh-keygen' to generate ssh keys, then copy the ssh public key into the './ssh/authorized_keys' file of the other droplet. Otherwise ssh or scp will not work.

```
scp <gzipped_file> root@<new_droplet>:/root/
```

On the new droplet, create a docker volume from the gzipped file.
```
./vackup import <gzipped_file> <volume_name>
```

Now 'docker volume ls' should show the volume. You can follow the steps to run docker compose, and now you will see the data from the old droplet's app. This shows how you can migrate a docker-based application between hosts.




