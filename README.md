# PriviateImageRegistry
specifiy the guide how to setup the priviate image registry

# **Description**
This repository is to specify the guide how to setup one private docker image registry.

# **Prerequiste**
a. linux server
b. storage size 100G (the size depends on the planned image sizes stored in registry)
c. docker
d. openssl
e. htpasswd  (installation: apt-get install apache2-util)

# **Install steps**
## **1 create the certificates**
### **1.1 create one directory for certificates**
mkdir -p docker_reg_certs
### **1.2 generate the certificates**
test:~/sam/openshift/off-registry$ openssl req  -newkey rsa:4096 -nodes -sha256 -keyout docker_reg_certs/domain.key -x509 -days 365 -out docker_reg_certs/domain.crt -addext "subjectAltName = IP:**xxx.yyy.zzz.ttt**"
....+......+.+........+.......+........+....+...+..+.+..+............+.+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.........+...+......+..+....+......+..+.+.....+....+...+..+.......+......+..+...+...+.+.....+.........+.+.....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+..............................+......+....+.....+..................+.......+...............+...........+...+.+.....+...+......+..........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
......+.....+...+...+.+.....+.+...+.....+......+.+...............+..+......+....+...........+....+...+.........+........+..........+......+......+.....+.+........+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+....+.....+.+........+.+.........+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*......+........+.+..+.........+...+............+....+.....+.......+..+.+......+........+......+.+...+......+..............+......+......+.+...............+..+...+...............+.......+...+..............+......+..................+....+......+.....+.+.....+.+.....+...............+...+..........+...................................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:Texas
Locality Name (eg, city) []:Dallas
Organization Name (eg, company) [Internet Widgits Pty Ltd]:tom
Organizational Unit Name (eg, section) []:jack
Common Name (e.g. server FQDN or YOUR name) []:test.test
Email Address []:www.ttt@xxxx.com

### **1.3 update the certificates in server**
a. sudo mkdir -p /etc/docker/certs.d/ip_address:5000   (ip_address is the registry IP)

b. sudo cp docker_reg_certs/domain.crt /etc/docker/certs.d/ip_address:5000/ca.crt

for Ubuntu/Debian system:
c. sudo cp docker_reg_certs/domain.crt /usr/local/share/ca-certificates/ca.crt

for CentOS/Redhat system:
c. sudo cp docker_reg_certs/domain.crt /usr/share/pki/ca-trust-source/anchors/ca.crt

d. update-ca-trust

## **2 create the auth**
### **2.1 create the auth directory**
mkdir docker_reg_auth

### **2.2 create the credentials**
htpasswd -Bbc ./docker_reg_auth/htpasswd xxx yyy  (xxx: username  yyy: password)


## **3. create the docker registry**
docker run -d -p 5000:5000 --restart=always --name registry -v $PWD/docker_reg_certs:/certs -v $PWD/docker_reg_auth:/auth -v /reg:/var/lib/registry -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd -e REGISTRY_AUTH=htpasswd registry:2












