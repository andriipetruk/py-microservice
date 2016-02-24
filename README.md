# py-microservice

## Purpose

Py-microservice is small Flask web application that will listen on port 8080.
The Flask application have 2 endpoints.

- The first endpoint accept only POST requests which have a json payload.
The endpoint store the data in a mongo data store.

- The second endpoint accept GET requests with an uid parameter, date parameter and will return the
number of occurrences of a given UID  for that day.



##REQUIREMENTS:
####Project tested with:
```
Ansible 1.9.2
Vagrant 1.8.1 (ubuntu/trusty64 v.20160201.0.2 VM)
Flask 0.9
Jinja2 2.6
host: Ubuntu 15.10 wily 4.2.0-23-generic x86_64

```

######Operational Infrastructure
![alt tag](https://github.com/andriipetruk/py-microservice/blob/master/infrastructure.png)


##QUICK START:
#### Install Virtualbox
```
wget http://download.virtualbox.org/virtualbox/5.0.14/virtualbox-5.0_5.0.14-105127~Ubuntu~wily_amd64.deb
sudo dpkg -i virtualbox-5.0_5.0.14-105127~Ubuntu~wily_amd64.deb
```
#### Install Vagrant
```
wget https://releases.hashicorp.com/vagrant/1.8.1/vagrant_1.8.1_x86_64.deb
sudo dpkg -i vagrant_1.8.1_x86_64.deb
vagrant plugin install vagrant-vbguest
vagrant plugin install vagrant-hostmanager
```
#### Install Ansible
```
apt-get -y install software-properties-common
apt-add-repository -y ppa:ansible/ansible
apt-get update
apt-get -y install ansible
```


5. You can access application on `http://localhost:8080/api/`

#### How to deploy and run
```
1. git clone git@github.com:andriipetruk/py-microservice.git
2. cd py-microservice/deploy/
3. vagrant up
```

to allow add hosts into /etc/hosts by vagrant-hostmanager input sudo password

run 'vagrant provision' command for provison if you have made changes on palybook, templates...
```

```
## Configuration

1. Config files of Flask app located in next path

```
py-microservice/src/settings
```

2. Example config for production enviroment
 
```
import os


MONGODB_HOST = os.getenv('MONGODB_HOST', 'mongodb')
MONGODB_PORT = os.getenv('MONGODB_PORT', '27017')
MONGODB_USER = os.getenv('MONGODB_USER', '')
MONGODB_PASSWORD = os.getenv('MONGODB_PASSWORD', '')
MONGODB_DB = os.getenv('MONGODB_DATABASE', 'pymicroservice')
DEBUG = os.getenv('DEBUG_MODE', 'False')

```

3. Environment are configurated in

```
py-microservice/src/run.py
```


## Testing

#### 1. Negative post
```
curl -H "Content-Type: application/json" -X POST -d '{"uid": "1", "name": "John Doe", "date": "2015-05-12T14:36:00.451765", "md5checksum": "e8c83e232b64ce94fdd0e4539ad0d44f"}' http://localhost:8080/api/
```
as result must be:
```
{
"response": "Error. Checksum doesn't match"
}
```

#### 2. Positive post
```
curl -H "Content-Type: application/json" -X POST -d '{"uid": "2", "name": "Jane Doe", "date": "2015-05-13T14:36:00.451765", "md5checksum": "0cf6399e2739304b73b41162735679fb"}' http://localhost:8080/api/
```
as result must be:

```
{
"response": "record successfuly added"
}
```

#### 3. GET test
```
http://localhost:8080/api/2/2015-05-13/
```
as result must be:

```
{
  "response": [
    {
      "_id": {
        "$oid": "56cc4c1ca67d3f2d63809f4a"
      }, 
      "date": {
        "$date": 1431527760451
      }, 
      "md5checksum": "0cf6399e2739304b73b41162735679fb", 
      "name": "Jane Doe", 
      "uid": 2
    }
  ]
}
```

