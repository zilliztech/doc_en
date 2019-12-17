---
id: "install_megawise_powerpc"
lang: "en"
title: "Install MegaWise (PowerPC Platform)"
---
# Install MegaWise (PowerPC Platform)


This document introduces how to install and configure MegaWise Docker in the PowerPC platform.


## Prerequisites

### Hardware requirements


| Component                     | Configuration                   |
|--------------------------|-------------------------|
| GPU |  NVIDIA Pascal or higher            |
| CPU                 |Intel CPU Sandy Bridge or higher|
| RAM         | 16 GB or higher           |
| Hard disk                  | 1 TB or higher         |

### Software requirements


| Component                     | Version                    |
|--------------------------|-------------------------|
| Operating system                 | CentOS 7 or higher |
| NVIDIA driver          | 410 or higher. The latest version is recommended.          |
| Docker                   | 18.03 only         |
| NVIDIA Container Runtime |  Latest           |


> Note: The PowerPC platform has different software requirements from the x86 platform. Please check whether the operating system and the Docker version are supported. The PowerPC platform does not support NVIDIA Container Toolkit, you must use NVIDIA Container Runtime instead. 

### Install NVIDIA driver, Docker, and NVIDIA Container Runtime

Refer to the following websites to learn how to install these software products:

- Docker: [https://www.docker.com/](https://www.docker.com/)
- NVIDIA driver: [https://www.nvidia.com/Download/index.aspx?lang=en-us](https://www.nvidia.com/Download/index.aspx?lang=en-us)
- NVIDIA container runtime: [https://github.com/NVIDIA/nvidia-container-runtime](https://github.com/NVIDIA/nvidia-container-runtime)

> Note: Use the following command to check the installed NVIDIA driver version:

```bash
$ sudo nvidia-smi
```

> Note: Use the following command to check whether Docker and NVIDIA Container Runtime are successfully installed:

```bash
$ docker run --runtime=nvidia --rm nvidia/cuda-ppc64le nvidia-smi
```

> Note: If you are a non-root user, you are recommended to add the user to the `docker` user group. Otherwise, you need to add `sudo` before `docker`
. Refer to [https://docs.docker.com/install/linux/linux-postinstall/](https://docs.docker.com/install/linux/linux-postinstall/) for more information.

### Install and run MegaWise

> Note: Do not use a root user to install or run MegaWise Docker.

1. Get the 0.5.0-ppc64le docker image of MegaWise.

    ```bash
    $ docker pull zilliz/megawise:0.5.0-ppc64le
    ```

2. Install PostgreSQL client.

    ```bash
    $ sudo apt-get install curl ca-certificates
    $ curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    $ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    $ sudo apt-get update
    $ sudo apt-get install postgresql-client-11
    ```

    PostgreSQL client is installed to `/usr/lib/postgresql/11/bin/` by default. After installation, run `which psql`. If the terminal does not return the correct location of the PostgreSQL client, please add the installation path to the environment variable.

    ```bash
    $ export PATH=/usr/lib/postgresql/11/bin:$PATH
    ```

3. Create a new folder as the working folder.

    ```bash
    $ cd $WORK_DIR
    $ mkdir conf
    ```

4. Get MegaWise config files.

    ```bash
    $ cd $WORK_DIR/conf
    $ wget https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/db/user_config.yaml \
    https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/db/etcd.yaml \
    https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/db/megawise_config_template.yaml \
    https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/db/etcd_config_template.yaml \
    https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/db/megawise_config.yaml

    ```

5. Modify config files based on the hardware environment of MegaWise.

   1. Open `user_config.yaml` in the `conf` directory.
   
      1. Navigate to the following code:

          ```yaml
          memory:
            cpu:
              physical_memory: 16 # size in GB
              partition_memory: 16 # size in GB
            gpu:
              num: 2
              physical_memory: 2  # size in GB
              partition_memory: 2 # size in GB
          ```

          Configure the parameters based on the hardware environment of the server (The numbers are in GBs).

          For the `cpu` part, `physical_memory` and `partition_memory` respectively represents the available memory size for MegaWise and the memory size for the data cache partition. It is recommended that you set both `partition_memory` and `physical_memory` to more than 70 percent of the server memory.
      
          For the `gpu` part, `num` represents the number of GPUs used by MegaWise. `physical_memory` and `partition_memory` respectively represents the available video memory size for MegaWise and the video memory size for the data cache partition. It is recommended that you reserve 2 GB of video memory to store the intermediate results during computation by setting `partition_memory` and `physical_memory` to a value that equals the video memory of a single GPU minus 2.

   
    2. Open `megawise_config_template.yaml` in the `conf` directory.
   
        1. Navigate to the following code and set parameter values:

            ```yaml
              worker_config:
                bitcode_lib: @bitcode_lib@
                precompile: true
                stage:
                  build_task_context_parallelism: 1
                  fetch_meta_parallelism: 1
                  compile_parallelism: 1
                  fetch_data_parallelism: 1
                  compute_parallelism: 1
                  output_parallelism: 1
                worker_num : 2
                gpu:
                  physical_memory: 2    # unit: GB
                  partition_memory: 2   # unit: GB
                cuda_profile_query_cnt: -1 #-1 means don't profile, positive integer means the number of queries to profile, other value invalid
            ```

            Set the values of some parameters per the following table:

            | Parameter                    | Value                   |
            |--------------------------|-------------------------|
            | `worker_num` | The value of `gpu_num` in `user_config.yaml`           |
            | `physical_memory` |   The value of `physical_memory` in `user_config.yaml`          |
            | `partition_memory` |   The value of `partition_memory` in `user_config.yaml`        |

        2. Navigate to the following code and set parameter values:

            ```yaml
              string_config:
                dict_config:
                  cache_size: 21474836480  # 20G
                  split_threshold: 1000000
                  split_each: 100000
                  small_scale_num: 4000    # try not to use the temporary id
                hash_config:
                  cache_size: 21474836480  # 20G
                  bucket_num: 1999993      # prime number is a good choice
                  bucket_size: 500         # make sure that each string is shorter than bucket_size-5
                  file_size: 104857600     # 100M
            ```

            `cache_size` in `dict_config` represents the memory size for encoding string dictionaries in bytes. 

            `cache_size` in `hash_config` represents the memory size for encoding string hashes in bytes.


6. Run MegaWise.

    ```bash
    $ docker run -d --runtime=nvidia --shm-size 17179869184 \
                        -e USER=`id -u` -e GROUP=`id -g` \
                        -v $WORK_DIR/conf:/megawise/conf \
                        -v $WORK_DIR/data:/megawise/data \
                        -v $WORK_DIR/logs:/megawise/logs \
                        -v $WORK_DIR/server_data:/megawise/server_data \
                        -v /home/$USER/.nv:/home/megawise/.nv \
                        -v /tmp:/tmp \
                        -p 5433:5432 \
                        $IMAGE_ID
    ```

    > Note: `$IMAGE_ID` is the image ID of the MegaWise Docker and can be acquired by the following command:

    ```bash
    $ docker image ls
    ```
    
    > Note: `-v /tmp:/tmp` specifies mapping to the `tmp` folder. In this guide, it is used to store example data. You can also customize the mapping folder.


    Parameter description

    - `--shm-size`

      The allocated memory size for a running Docker image in bytes. Use the value in the `physical_memory` parameter under `cpu`->`cache` in `user_config.yaml`.

    - `-v`

      Directory mapping between the host and the Docker image. Separated by `:`, the former part is the directory of the host and the latter part is the directory of the Docker image.

      When launching the container, you can use `-v` to map local data files to the container to import local files to the MegaWise database.

    - `-p`

      Port mapping between the host and the Docker image. Separated by `:`, the former part is the port of the host and the latter part is the port of the Docker image. You can set the host to use any unoccupied port. In this tutorial, we use 5433.

    Logging starts when the container starts running. If you can find the following content in the log, you can assume the MegaWise server is successfully running.

    ```bash
    MegaWise server is running...
    ```

### Connect to MegaWise

You can either connect to MegaWise inside the Docker or outside the Docker.

> Note: To use the Infini interface, you must connect to MegaWise outside the Docker.

#### Connect to MegaWise Inside the Docker

1. Enter the bash command of MegaWise docker and connect to MegaWise:

   ```bash
   $ docker exec -u megawise -it <$MegaWise_Container_ID> bash
   $ cd script && ./connect.sh
   ```
    
   If the terminal displays the following information, you can assume that the connection to MegaWise is successful.

    ```bash
    psql (11.6 (Ubuntu 11.6-1.pgdg18.04+1), server 11.1)
    Type "help" for help.

    postgres=#
    ```

#### Connect to MegaWise Outside the Docker

1. Stop MegaWise.

   ```bash
   $ docker stop <$MegaWise_Container_ID>
   ```

2. Navigate to the working directory of MegaWise and make the following changes:

   1. Open `postgresql.conf` under the `data` folder and change the value of `listen_addresses` to `'*'` (note the quote sign). You must also remove the comment sign `#`.
   2. Open `pg\_hba.conf` under the `data` folder and add the following line after `# IPv4 local connections`:
   ```
   host   all      all     0.0.0.0/0      trust
   ```
3. Restart MegaWise.

   > Note: Do not use `docker start <$MegaWise_Container_ID>` to restart MegaWise.

   ```bash
    $ docker run -d --runtime=nvidia --shm-size 17179869184 \
                        -e USER=`id -u` -e GROUP=`id -g` \
                        -v $WORK_DIR/conf:/megawise/conf \
                        -v $WORK_DIR/data:/megawise/data \
                        -v $WORK_DIR/logs:/megawise/logs \
                        -v $WORK_DIR/server_data:/megawise/server_data \
                        -v /home/$USER/.nv:/home/megawise/.nv \
                        -v /tmp:/tmp \
                        -p 5433:5432 \
                        $IMAGE_ID
    ```

    > Note: `$IMAGE_ID` is the image ID of the MegaWise Docker and can be acquired by the following command:

    ```bash
    $ docker image ls
    ```
    
    > Note: `-v /tmp:/tmp` specifies mapping to the `tmp` folder. In this guide, it is used to store example data. You can also customize the mapping folder.

    Parameter description

    - `--shm-size`

      The allocated memory size for a running Docker image in bytes. Use the value in the `physical_memory` parameter under `cpu`->`cache` in `user_config.yaml`.

    - `-v`

      Directory mapping between the host and the Docker image. Separated by `:`, the former part is the directory of the host and the latter part is the directory of the Docker image.

      When launching the container, you can use `-v` to map local data files to the container to import local files to the MegaWise database.

   - `-p`

      Port mapping between the host and the Docker image. Separated by `:`, the former part is the port of the host and the latter part is the port of the Docker image. You can set the host to use any unoccupied port. In this tutorial, we use 5433.

    Logging starts when the container starts running. If you can find the following content in the log, you can assume the MegaWise server is successfully running.

    ```bash
    MegaWise server is running...
    ```
4. Use MegaWise.
  
    ```bash
    $ psql -U zilliz -p 5433 -h $IP_ADDR -d postgres
    ```

    > `$USER_ID` can be acquired by the following command:

    ```bash
    $ id -u
    ```

    > `$IP_ADDR` can be acquired by the following command:

    ```bash
    $ ifconfig
    ```
    
    
    If the terminal displays the following information, you can assume that the connection to MegaWise is successful.

    ```bash
    psql (11.6 (Ubuntu 11.6-1.pgdg18.04+1), server 11.1)
    Type "help" for help.

    postgres=#
    ```

    > Note：If the connection timeouts, check whether the firewall settings are correct. In the current version, you must import data every time MegaWise is restarted.
    
## Create a MegaWise user and import data

1. Create a user in `postgres` with username `zilliz` and password `zilliz`.

   ```bash
   postgres=# CREATE USER zilliz WITH PASSWORD 'zilliz';
   postgres=# grant all privileges on database postgres to zilliz;
   ```

2. After creating a user, you can use the created user to import data. 
   
   Get example data:
   
   ```bash
   $ wget -P /tmp https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/sample_data/nyc_taxi_data.csv
   ```

   Create an extension, create a table, and import example data.

   ```bash
   postgres=# create extension zdb_fdw;
   postgres=# create table nyc_taxi(
    vendor_id text,
    tpep_pickup_datetime timestamp,
    tpep_dropoff_datetime timestamp,
    passenger_count int,
    trip_distance float,
    pickup_longitute float,
    pickup_latitute float,
    dropoff_longitute float,
    dropoff_latitute float,
    fare_amount float,
    tip_amount float,
    total_amount float
    );
   postgres=# copy nyc_taxi from '/tmp/nyc_taxi_data.csv'
    WITH DELIMITER ',' csv header;
   ```

> Note: If you need to create charts in the Infini interface, you must create the `zdb_fdw` extension. When you use `copy` to import data, ensure that the folder that the data resides is already mapped to MegaWise docker. This install guide has mapped the `tmp` folder to MegaWise Docker. So, you can use the `tmp` folder to store and import data.

## What's next

[Install Infini](./install_infini)
