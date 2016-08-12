# ssh-tunnel
Local port forwarding with SSH.
Access a service on private.server.tld:port through an ssh connection between your local/docker server and public.server.tld.

# Requirements
You need to set up passwordless ssh access to your public.server.tld

# Usage
Create a folder on your local/docker server (i.e. /some/local/folder) containing 2 files:
- /some/local/folder/config
- /some/local/folder/private_key (the one used for passwordless access)

Format for /some/local/folder/config 
```
Host public.server.tld
    User sshusername
    HostName public.server.tld
    IdentityFile /root/.ssh/private_key  # (leave /root/.ssh/ as is)
    StrictHostKeyChecking no
    Port 22
```
Launch the container:
```
docker run -d -p 3306:3306 \
	-e PORT_EXPOSED=3306 \
	-e PORT_TO=3306 \
	-e ADDR_TO=private.mysql.server.tld \
	-e ADDR_TUNNEL=public.server.tld \
	-v /some/local/folder:/root/.ssh \
	--name ssh.tunnel \
	ndiazg/ssh-tunnel:latest
```
Now you can access the private service on the chosen port of your local/docker server

```
mysql -u mysqlusername -p -h local.server.tld -P 3306
```

### Tip
Connect and alias your tunnel along with other containers in the same network. 
```
docker network connect --alias some.endpoint somenetwork ssh.tunnel
```
Now your other containers can reach the remote/restricted service at some.endpoint
