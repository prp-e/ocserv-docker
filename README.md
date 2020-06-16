# Openconnect using Docker Compose 

## Dependencies 

Just install these dependencies : 

```
sudo apt install docker docker-compose 
``` 

On a server you want to be the main openconnect server. 

## Running the server 

For running the server, you just need to run this command: 

```
docker-compose up -d
``` 

And it will make you an openconnect server. 

## Configuration 

The file is okay, it works fine without any excess configuration. But some datacenters may send you warnings for using "192.168.0.0/24" range. So just run this command : 

```
docker-compose exec ocserv sh 
``` 

then just edit `/etc/ocserv/ocserv.conf`. Find this : 

```
ipv4-network = 192.169.1.0
```

and change it to : 

```
ipv4-network = 10.10.0.0 
``` 

Or any other range you want, to prevent the server from "netscan" issues. Then stop the container and restart it. 

## Using HAProxy 

If you want to send your traffic to a server first, then send it to your ocserv server, you need `haproxy`. 
Just install the damn thing : 

```
sudo apt install haproxy
``` 

Then edit the file : 

```
/etc/haproxy/haproxy.cfg 
``` 

and add these lines to the end of file : 

```
listen ocserv
        bind 0.0.0.0:4443
        mode tcp
        balance leastconn
        server ocserv <YOUR_REMOTE_OCSERV_IP>:4443
``` 

## Adding or deleting users from OCServ 

Just connect to your *main* ocserv server, then 

### Adding a user 

```
docker exec -ti ocserv ocpasswd -c /etc/ocserv/ocpasswd -g "Route,All" <username>
``` 

### Deleting a user 

```
docker exec -ti ocserv ocpasswd -c /etc/ocserv/ocpasswd -d <username>
``` 


## Connecting to your server 

For Windows/iOS/Android users, you just need to install an opennconnect client on your phone. For Linux users, just install this : 

```
sudo apt install openconnect 
``` 

then : 

```
sudo openconnect <YOUR_HAPROXY_IP>:4443 
``` 

NOTE : If you changed the port, use your own port here. 

When it asks for *group*, just type **Route**. Then login as usuall. 

## NOTES 

* Linux's `openconnect` asks for _username_ for both __Group__ and __Username__. But, what it asks first is the group. Just type `Route` and press enter. 
* It seems the original image I used handles volumes and if you reboot your freaking server, it will keep credentials. You will only need to type `docker-compuse up -d` to take your server back to life.

## TODO 

- [] Making a new image and fix something. 
- [] Providing a tool to automate creating an `openconnect` server. 
- [] Providing a tool to create users easier (A web-based GUI, eh?).  
- [x] fixing this TODO :P. 
