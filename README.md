# tel-tec - Telstra Test

Full stack followed is as below -

    Web Layer - Nginx (webserver on port 80 in a VM created by Vagrant in Google Cloud Platform (GCP))
    DB  Layer - Mongo DB (NOSQL database running in a container (port 27017) in a VM (port 32769) in GCP)
    API Layer - Spring Boot microservice (running in a container (port 8080) in a VM (port 32770) in GCP)

This repository contains following files -

<b>Vagrantfile</b> - This file is configured to create a virtual machine (named webserver) on google cloud platform (GCP). '<b>vagrant up --provider=google</b>' command is required to be executed for the virtual machine. This file is configured for installation of softwares - git and ansible by shell provisioner. Ansible provisioner is also configured and playbook webserver.yml file is executed to install nginx web server. Ansible playbook also contains the tasks for copying and renaming the index.html under webroot directory (/var/www/html).

The o/p is as below.

      $ vagrant up --provider=google
      Bringing machine 'default' up with 'google' provider...
      ==> default: Launching an instance with the following settings...
      ==> default:  -- Name:            webserver
      ==> default:  -- Type:            n1-standard-1
      ==> default:  -- Disk type:       pd-standard
      ==> default:  -- Disk size:       10 GB
      ==> default:  -- Disk name:
      ==> default:  -- Image:           debian-9-stretch-v20171213
      ==> default:  -- Zone:            australia-southeast1-a
      ==> default:  -- Network:         default
      ==> default:  -- Metadata:        '{}'
      ==> default:  -- Tags:            '[]'
      ==> default:  -- IP Forward:
      ==> default:  -- External IP:
      ==> default:  -- Preemptible:     false
      ==> default:  -- Auto Restart:    true
      ==> default:  -- On Maintenance:  MIGRATE
      ==> default:  -- Autodelete Disk: true
      ==> default:  -- Scopes:
      ==> default: Waiting for instance to become "ready"...
      ==> default: Machine is booted and ready for use!

<b>webserver.yml</b> - ansible playbook for installation and configuration of nginx on webserver virtual machine. The command <b>ansible-playbook webserver.yml</b> will be run by ansible provisioner.

The o/p from ansible provisioner is 

      PLAY [localhost] ***************************************************************

      TASK [Install nginx] ***********************************************************
      changed: [127.0.0.1]

      TASK [rename index file] *******************************************************
      changed: [127.0.0.1]

      TASK [copy index html file] ****************************************************
      changed: [127.0.0.1]

      TASK [rename index html file] **************************************************
      changed: [127.0.0.1]

      RUNNING HANDLER [restart nginx] ************************************************
      changed: [127.0.0.1]

      PLAY RECAP *********************************************************************
      127.0.0.1  : ok=5    changed=5    unreachable=0    failed=0   

<b>index.html</b> - This file serves two purposes

1. retrieving and showing people data present in mongo database in a html div.
2. html form containing firstname, lastname, company and role to be saved in mongo database.

Mongo Container - mongo database is running in a docker container (name <b>mongodb</b>) on another virtual machine (with name <b>rest-api</b>) in google cloud platform. 

Spring Boot API Container - Spring Boot based microservice is deployed in a separate container (name <b>people-api</b>) on <b>rest-api</b> virtual machine for retrieving and saving the data in mongo database.

Containers on <b>rest-api</b> machine are as below.

      CONTAINER ID                    PORTS                                               NAMES
      1d51de7f85ff                    0.0.0.0:32770->8080/tcp                             people-api
      83e67c6d8539                    0.0.0.0:32769->27017/tcp                            mongodb

The rest endpoints exposed are:

      GET         http://35.201.0.239:32770/people            Returns list of Person.
      GET         http://35.201.0.239:32770/people/{name}     Returns Person having either firstname as name or lastname as name.
      POST        http://35.201.0.239:32770/people            Saves Person in the request body in mongo database.
