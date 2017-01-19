# galaxy-docker-training

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
         <tool id="bedtools_clusterbed" destination="docker_local"/>
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
