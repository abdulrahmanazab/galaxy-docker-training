Installing a local swarm cluster
=================================
Login to your VM:
```bash
ssh -i docker-tutorial.pem cloud-user@<front-end-vm-ip>
[cloud-user@gfront-end-vm ~]$ ssh -i docker-tutorial.pem <your-vm-ip>
```
Add yourself to the ``docker`` group:
```bash
sudo -i
usermod -aG docker cloud-user
exit
exit
```
Configure Docker for swarm
----------------------------
As root:
* Edit ``/lib/systemd/system/docker.service``: 
```
ExecStart=/usr/bin/dockerd -H tcp://<your-local-ip>:2375 -H unix:///var/run/docker.sock
```
* ``systemctl daemon-reload``
* ``service docker restart``
* ``sudo systemctl enable docker``

More on Docker configuration can be found [here](https://docs.docker.com/engine/admin/) and [here](https://docs.docker.com/engine/admin/systemd/)

Run docker swarm
-------------------
As root:
* Create a node addresses text file and type in your local IP into it: 
``echo <your-local-ip>:2375 > cluster.txt``
* Run docker swarm (unsafe):
```bash
docker run -d -p 6000:2375 -v ~/cluster.txt:/cluster.txt swarm -l debug manage file:///cluster.txt
docker run -H 0.0.0.0:6000 info
```
* Run docker swarm (safe):
```bash
docker run -d -p <local-IP>:6000:2375 -v ~/cluster.txt:/cluster.txt swarm -l debug manage file:///cluster.txt
docker run -H <local-IP>:6000 info
```
* Now adding multiple nodes to the cluster.txt file **(this is a demo)**

Shared disk between VMs using sshfs
------------------------------------
We have a VM *shared-vm* where we have the shared disk. This is how to configure it *(already done)*:
As root:
- create a directory to share: ``/local-shared``
- set ``PasswordAuthentication yes`` in ``/etc/ssh/sshd_config``
- Restart the sshd service: ``service sshd restart``
- set a password for root

Now in Your VM (as root):
* ``yum install sshfs``
* uncomment “user_allow_other” in ``/etc/fuse.conf``
* Create a directory to mount the shared area and attach the volume ``sshfs``:
```bash
mkdir /shared
chmod +rw /shared
sshfs -o allow_other,auto_unmount root@<shared-vm IP>:/local-shared /shared
```
*ask your instructor for the root paassword*
Now all users will have access to /shared 

* Now something cool! test writing your name to a file in the shared area via a docker container:
```bash
docker run --rm -v /shared/:/shared/ busybox sh -c "echo your-name >> /shared/users.txt"
```
* Test it again and this time, run the container through swarm:
```bash
docker run -H <local-IP>:6000 --rm -v /shared/:/shared/ busybox sh -c "echo your-name >> /shared/users.txt"
```
Useful info:
* To unmount: ``fusermount -u /shared``
* To activate auto-mount the shared disk at startup: 
Add the following line to ``/etc/crontab``: 
```bash
@reboot sshfs -o allow_other,auto_unmount root@<shared-vm IP>:/local-shared /shared 
```
