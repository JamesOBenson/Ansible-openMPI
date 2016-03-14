# Ansible OpenMPI deploy
This script is designed to use Ansible to deploy either the repository version (v1.6.5) of OpenMPI or the current version (1.10.2) of MPI.

**Requirements:**
* Ansible
* Ubuntu
* hosts file

##How to execute
- Verify that correct temperary directory to download, compile and install OpenMPI
- Verify the correct destination path for the end user to be updated.

Execute using: 
 * for 1.6.5: ansible-playbook OpenMPI.yml -t easy 
 * for 1.10.2: ansible-playbook OpenMPI.yml -t latest 
 * To verify installation only: ansible-playbook OpenMPI.yml -t verify


##Some helpful command:
To verify version:
- ompi_info | grep 'Open MPI:' | awk '{print $3}'

To run with multiple servers and verify connectivity:
echo <Your Server IP's> >> mpi_hosts 
mpirun -v -np 2 --hostfile ~/mpi_hosts /connectivity


##To verify installation manually:
### From Open-mpi.org
wget http://svn.open-mpi.org/svn/ompi/tags/v1.6-series/v1.6.4/examples/hello_c.c
mpicc hello_c.c -o hello
mpirun ./hello
<<<<<<< HEAD
Output:    
Hello, world, I am 0 of 1


wget http://svn.open-mpi.org/svn/ompi/tags/v1.6-series/v1.6.4/examples/connectivity_c.c
mpicc connectivity_c.c -o connectivity
mpirun ./connectivity
Output:    
Connectivity test on 1 processes PASSED.
=======
Output:    Hello, world, I am 0 of 1

wget http://svn.open-mpi.org/svn/ompi/tags/v1.6-series/v1.6.4/examples/connectivity_c.c
mpicc connectivity_c.c -o connectivity
mpirun ./connectivity
Output:    Connectivity test on 1 processes PASSED.
>>>>>>> parent of 2dec4c5... MD fix

### From Community:
wget http://help.eclipse.org/mars/topic/org.eclipse.ptp.pldt.doc.user/html/samples/testMPI.c
mpicc -o testMPI testMPI.c
mpirun -np 4 testMPI
Output:
    Hello MPI World the original.
    Hello MPI World the original.
    Hello MPI World the original.
    Hello MPI World the original.
    From process 0: Num processes: 4
    Greetings from process 1!
    Greetings from process 2!
    Greetings from process 3!

## Notes:
If using something like Openstack, once successfully deployed, a user can snapshot the image and deploy n number of instances, update the hosts file with the additional IP's and execute an mpi job.

Or if doing it manually, just write the hosts file and execute.  Be sure to update the "np" to the number of servers you are running it across.
> mpirun -v -np 2 --hostfile ~/mpi_hosts /connectivity


##Notes about verification:
Your output should be similiar to this when it works:
$ sudo ansible-playbook OpenMPI.yml -t "verify"

PLAY [OpenMPI Deployment beginning ...] ****************************************

TASK [download hello_c.c from open-mpi.org] ************************************
ok: [x.x.x.x]

TASK [hello world test compile] ************************************************
changed: [x.x.x.x]

TASK [hello world test] ********************************************************
changed: [x.x.x.x]

TASK [debug] *******************************************************************
ok: [x.x.x.x] => {
    **"msg": "Hello world success!"**
}

TASK [debug] *******************************************************************
**skipping: [x.x.x.x]**

TASK [download connectivity_c.c from open.mpi.org] *****************************
ok: [x.x.x.x]

TASK [connectivity compile] ****************************************************
changed: [x.x.x.x]

TASK [Connectivity test] *******************************************************
changed: [x.x.x.x]

TASK [debug] *******************************************************************
ok: [x.x.x.x] => {
    **"msg": "Connectivity success!"**
}

TASK [debug] *******************************************************************
**skipping: [x.x.x.x]**

PLAY RECAP *********************************************************************
x.x.x.x               : ok=8    changed=4    unreachable=0    failed=0   

*The skipped debug tasks will notify you if the verification fails.*



##How is this playbook licensed?

It's licensed under the Apache License 2.0. The quick summary is:

> A license that allows you much freedom with the software, including an explicit right to a patent. “State changes” means that you have to include a notice in each file you modified. 

[Pull requests](https://github.com/JamesOBenson/openMPI/pulls) and [Github issues](https://github.com/JamesOBenson/openMPI/issues) are welcome!

-- James