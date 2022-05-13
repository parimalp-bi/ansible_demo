# This is simple demo for Ansible

## 1. Install all Software dependencies
  - Install Vagrant on Windows Desktop
  - install Oracle Virtual Machine

## 2. Fireup virtual machine using vagrant and install required softwares
  - command **vagrant up** in same directory as **Vagrantfile**. This will create a ubuntu vm.
  - ssh into machine named dockerhost and perform **sudo su**
  - [Install Anisble](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-ubuntu) and [Install Docker](https://docs.docker.com/engine/install/ubuntu/)

## 3. Start Docker Containers inside vagrant vm using below command
**_NOTE:_**  Our vagrant vm has opened two ports 5000 and 4141. We will run docker containers on those ports
  - vagrant ssh 
  - sudo su 
  - docker container run -d -p 5000:5000 --name target1 parimalppatil/ubuntu_ssh
  - docker container run -d -p 4141:4141 --name target2 parimalppatil/ubuntu_ssh


## 3. Inspect docker containers
  - use ip address  to update **inventory.txt**
  - docker inspect target1 | grep -i ipaddress
  - docker inspect target2 | grep -i ipaddress

## 4. Test connectivity to docker containers from vagrat vm
**_NOTE:_** : Use ip address got in above commands. Password is **root**
  - ssh root@172.17.0.2
  - ssh root@172.17.0.3

## 5. Test connectivity to docker containers from vagrat vm using ansible ping module
  - ansible target* -m ping -i /vagrant/inventory.txt
  - output of above command should give yo below
```
  target1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
  }
  target2 | SUCCESS => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/bin/python3"
      },
      "changed": false,
      "ping": "pong"
  }
```

## 5. We will configure vms(docker containers created above)
  - This Configuration is to make servers ready to run python flask application. 
    Below are the steps required to get this working on a base linux system.
  
  - ssh to one of container(target2) using ssh root@172.17.0.3 and password root and perform below steps
  - Install all required dependencies

    ```
      apt-get install -y python python-setuptools python-dev build-essential python-pip python-mysqldb
    ```
  
  - Install python dependencies

      ```  
        pip install flask
        pip install flask-mysql
      ```
  - copy source code
  - Configure Web Server and Start Web Server

      ```
        FLASK_APP=/opt/app.py flask run --host=0.0.0.0
      ```

## 6. Test

  - Open a browser and go to URL
```
    http://localhost:5000                            => Welcome
    http://localhost:5000/how%20are%20you            => I am good, how about you?
    http://localhost:5000/read%20from%20database     => JOHN
```

## 7. Destroy container and lets automate all above steps using ansible playbook

  - Follow below steps

    - docker container stop target2
    - docker container prune
    **_NOTE:_** At this stage create another container using below command check for ipaddress and update inventory file accordingly. 
      ```
      docker container run -d -p 4141:4141 --name target2 parimalppatil/ubuntu_ssh
      ```
    - ansible-playbook /vagrant/playbook.yml -i /vagrant/inventory.txt

      Open a browser and go to URL
    ```
      http://localhost:5000                            => Welcome
      http://localhost:5000/how%20are%20you            => I am good, how about you?
      http://localhost:5000/read%20from%20database     => JOHN

      http://localhost:4141                            => Welcome
      http://localhost:4141/how%20are%20you            => I am good, how about you?
      http://localhost:4141/read%20from%20database     => JOHN    
    ```