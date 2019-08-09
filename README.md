# TimescaleDB_Test

Ansible role to automate data loading test to evaluate Aura infrastructure's data storage capacity. 
Data are randomly generated and injected in TimescaleDB database.

## TimescaleDB

At first, you need to install Aura Infrastructure (https://github.com/RGirard94/TimescaleDB) with TimescaleDB

## Playbook's details

This Ansible playbook consists of two roles:
  - prerequisite : installs tools used to perform well the different python scripts.
  - copy_directories : copies a directory containing the python scripts to randomly generate data and an other directory
                       containing the python scripts to inject the generated data in TimescaleDB database. These directories are copied from your local machin to the virtual machin containing the Aura Infrastructure.
                    
## Usage

At first you need to launch a ec2 instance on Amazone and :
  - create a config file in .ssh directory and add 
  `Host aura_aws_timescale
    Hostname 'YOUR IP ADDRESS'
    User centos
    IdentityFile '/PATH/TO/YOUR/KEY/FILE'`
    
Everytime you stop and start the ec2, you need to change IP address in previous file.

At first you need to launch the playbook to install prerequisite and copy_directories roles. It allows to set the work environment to perform well the loading tests.

`ansible-playbook -i inventories/dev.yml install.yml`
                   
## Directories details

/random_data_generator/source : `sudo python3.6 random_data_generator.py -nbu ** -hr **` allows to randomly generate data for this number of user during this number of hour of collect
                        `sudo python3.6 random_data_generator_2.py -rr ** -ma ** -mg **` allow to randomly generate the exact number of data.
             
/manual_data_injection_in_timescale : `sudo python3.6 manual_data_injection.py -d /opt/docker-data/data/aura_generated_data/` allows to inject manually data located in aura_generated_data directory into TimescaleDB.

/python_test_scripts : contain wrapper for loading and reading tests

Two tests are located in python_test_scripts directory
  - loading_test_timescale.py inject daily collected data into TimescaleDB and check the number of injected data.
  - reading_test_timescale.py performs some queries.
  
To execute these scripts in background:
 `nohup sudo python3.6 loading_test_timescale.py`
 `nohup sudo python3.6 reading_test_timescale.py`
