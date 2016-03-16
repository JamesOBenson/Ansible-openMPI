# Ansible OpenMPI deploy
This script is designed to use Ansible to deploy either the repository version (v1.6.5) of OpenMPI or the current version (1.10.2) of MPI.

**Requirements:**
* Ansible
* Ubuntu
* hosts file

##How to execute
If you want to run OpenMPI 1.10.2:
- Verify the correct temperary directory to download, compile and install OpenMPI 
- Verify the correct destination path for the end user to be updated.

**Execute using:**

 * for 1.6.5: ```ansible-playbook OpenMPI.yml -t easy```
 * for 1.10.2: ```ansible-playbook OpenMPI.yml -t latest```
 * To verify installation only: ```ansible-playbook OpenMPI.yml -t verify```


**General Workflow:**

1. Updates Ubuntu
2. Installs required packages and pip packages
3. Disable Strict Host Key Checking in SSH Config
4. Start Install

    Easy Install:

    1. Installs repository packages for OpenMPI 1.6.5 

    Latest Install:
    
    1. Configure/Makes/Make Install OpenMPI 1.10.2
    2. Adds lines to bashrc

5. Runs verifications

##Some helpful command:
To verify version:
```ompi_info | grep 'Open MPI:' | awk '{print $3}'```

To run with 2 nodes and verify connectivity:
```
echo <Your Server IP's> >> mpi_hosts 
mpirun -np 2 -machinefile mpi_hosts connectivity
```
To run the HelloWorld.py on 2 nodes:
```
mpirun -np 2 -machinefile mpi_hosts python HelloWorld.py 
```

##To verify installation manually:
### From Open-mpi.org
####Hello World:
```
wget http://svn.open-mpi.org/svn/ompi/tags/v1.6-series/v1.6.4/examples/hello_c.c
mpicc hello_c.c -o hello
mpirun ./hello
```
Output:    
```
Hello, world, I am 0 of 1
```

####Connectivity:
```
wget http://svn.open-mpi.org/svn/ompi/tags/v1.6-series/v1.6.4/examples/connectivity_c.c
mpicc connectivity_c.c -o connectivity
mpirun ./connectivity
```
Output:
```
Connectivity test on 1 processes PASSED.
```

### From Community:
```
wget http://help.eclipse.org/mars/topic/org.eclipse.ptp.pldt.doc.user/html/samples/testMPI.c
mpicc -o testMPI testMPI.c
mpirun -np 4 testMPI
```
Output:
```
    Hello MPI World the original.
    Hello MPI World the original.
    Hello MPI World the original.
    Hello MPI World the original.
    From process 0: Num processes: 4
    Greetings from process 1!
    Greetings from process 2!
    Greetings from process 3!
```

## Notes:
If using something like Openstack, once successfully deployed, a user can snapshot the image and deploy n number of instances, update the hosts file with the additional IP's and execute an mpi job.

Or if doing it manually, just write the hosts file and execute.  Be sure to update the "np" to the number of servers you are running it across.

``` mpirun -np 2 -machinefile mpi_hosts connectivity```

Also, don't forget you need to have your private keys loaded on the machines to be able to SSH back and forth between for MPI to work properly.

##Notes about verification:
Your output should be similiar to this when it works:
```
$ ansible-playbook OpenMPI.yml -t "verify"

PLAY [OpenMPI Deployment beginning ...] **************************************

TASK [download hello_c.c from open-mpi.org] **********************************
ok: [x.x.x.x]

TASK [hello world test compile] **********************************************
changed: [x.x.x.x]

TASK [hello world test] ******************************************************
changed: [x.x.x.x]

TASK [debug] *****************************************************************
ok: [x.x.x.x] => {
    "msg": "Hello world success!"
}

TASK [debug] *****************************************************************
skipping: [x.x.x.x]

TASK [download connectivity_c.c from open.mpi.org] ***************************
ok: [x.x.x.x]

TASK [connectivity compile] **************************************************
changed: [x.x.x.x]

TASK [Connectivity test] *****************************************************
changed: [x.x.x.x]

TASK [debug] *****************************************************************
ok: [x.x.x.x] => {
    "msg": "Connectivity success!"
}

TASK [debug] *****************************************************************
skipping: [x.x.x.x]

PLAY RECAP *******************************************************************
x.x.x.x               : ok=8    changed=4    unreachable=0    failed=0 
```
*The skipped debug tasks will notify you if the verification fails.*

*You want to see the two messages mentioned above:*     
"msg": "Hello world success!"      
"msg": "Connectivity success!"

##How is this playbook licensed?

It's licensed under the Apache License 2.0. The quick summary is:

> A license that allows you much freedom with the software, including an explicit right to a patent. “State changes” means that you have to include a notice in each file you modified. 

[Pull requests](https://github.com/JamesOBenson/openMPI/pulls) and [Github issues](https://github.com/JamesOBenson/openMPI/issues) are welcome!

-- James