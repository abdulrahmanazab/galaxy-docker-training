# Galaxy Docker Training (hosted by [cPouta](https://research.csc.fi/cpouta))

Login to your VM

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
Now login again!

Install your Galaxy on the front end VM:
 ```
mkdir your-first-name
cd your-first-name
git clone -b dev https://github.com/galaxyproject/galaxy.git
```
Edit ``galaxy.ini``:
```
cd galaxy
cp config/galaxy.ini.sample config/galaxy.ini
vim config/galaxy.ini
...
port = <your-port-number>
host = 0.0.0.0
```
Start Galaxy:
```
./run.sh --daemon
```
Galaxy docker integration
---------------------------
A modified guide of [This repo](https://github.com/apetkau/galaxy-hackathon-2014/edit/master/README.md)

* Construct a basic `job_conf.xml` with the following command.
   
   ```bash
   cp job_conf.xml.sample_basic job_conf.xml
   ```
   
* Add a docker destination in `job_conf.xml` to enable running through docker:
   
   ```xml
  <destination id="docker_local" runner="local">
           <param id="docker_enabled">true</param>
           <param id="docker_volumes">$galaxy_root:ro,$galaxy_root/database/tmp:rw,$tool_directory:ro,$job_directo
   ry:ro,$working_directory:rw,$default_file_path:rw</param>
           <param id="docker_sudo">false</param>
           <param id="docker_net">bridge</param>
           <param id="docker_auto_rm">false</param>
          <!-- <param id="docker_host">tcp://0.0.0.0:6000</param> -->
   </destination> 
   …
   <tools>
           <tool id="smalt_wrapper (docker)" destination="docker_local"/>

   </tools>

   ```
   Construct a basic `tool_conf.xml` with the following command.
   
   ```bash
   cp tool_conf.xml.sample tool_conf.xml
   ```
   Then add a new section for this tool:
   
   ```xml
  <section id="docker" name="Docker tools">
      <tool file="docker/smalt_wrapper.xml"/>
  </section>
   ```
 * Go to **2. Installing Tool configuration** in [this repo](https://github.com/apetkau/galaxy-hackathon-2014/tree/master/smalt) and follow the guide
 
Enable Galaxy to use BioContainers (Docker)
-------------------------------------------
This is a very cool feature where Galaxy automatically detects that your tool has an associated docker image, pulls it and runs it for you. These images (when available) have been generated using [mulled](https://github.com/mulled). To test, install the [IUC bedtools](https://toolshed.g2.bx.psu.edu/repository?repository_id=8d84903cc667dbe7&changeset_revision=7b3aaff0d78c) from the toolshed. When you try to execute *ClusterBed* for example. You may get a missing dependancy error for *bedtools*. But bedtools has an associated docker image on [quay.io](https://quay.io/). Add the following to ``tool_conf.xml``:
```xml
<tools>
         ....
         <tool id="bedtools_clusterbed" destination="docker_local"/>
 </tools>
```
When you execute the tool again, Galaxy will pull the image from Biocontainers ([quay.io/biocontainers](https://quay.io/organization/biocontainers)), run the tool inside of this container to produce the desired output.
 
Galaxy with Docker swarm
-------------------------
Install and configure sawrm [here](swarm.md)
To let Galaxy submit jobs to swarm:
* Add/uncomment the following line in ``job_conf.xml``within the docker destination section:
```xml
<param id="docker_host">tcp://your-local-ip:6000</param>
```
* Place Galaxy's database directory inside the shared area ``/shared``
