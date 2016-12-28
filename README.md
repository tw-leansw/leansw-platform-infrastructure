#Leansw-platform-infrastrcture
-------------------------------------

__Leansw-platform-infrastrcture__ is a platform to provide a set of nexus,CI/CD and other basic services so that people can set up infrastructure quickly and easily.

##The Linux distribution we supported
__Centos , Ubuntu , Debian , Redhat , Suse__

##Environment & tools required
To run this project you need install [vagrant](https://www.vagrantup.com/docs/installation/) and [ansible](http://docs.ansible.com/ansible/intro_installation.html)

##How to run

```
vagrant up
vagrant provision
vagrant ssh your_virtual_machine
```
Then you will get three machines now: master-1, slave-1, slave-2
you can login them with command`vagrant ssh name_of_host`,or check status by `vagrant status`

##Service providing
* nexus repo [http://localhost:8081](http://localhost:8081)
* rancher [http://10.245.6.2:8080](http://localhost:8080): master-1 as rancher master,and has two agent slave-1, slave-2 for now
* Jenkins // _TO DO_

##Configuration & Customize
For now, we boot three virtual machines, you can change the host name and its private IP in the file __development__, or choose another os box in the __Vagrantfile__, or modify each service tasks under the directory __roles__

##Trouble Shooting
- To run this project successfully, you need in the network __twdata__
- Have a key pair __~/.ssh/id_rsa.pub__, __~/.ssh/id_rsa ,__ in your local host