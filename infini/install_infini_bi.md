---
id: "install_infini"
lang: "en"
---
# Install Infini


## Prerequisites

1. Make sure you have installed the following software:
   - [Docker 19.03 or higher](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)
   - [Docker Compose](https://docs.docker.com/compose/install/)
2. Make sure you have installed MegaWise, launched the MegaWise server and imported sample data.
   - [Install Megawise](./install_megawise)

## Use Docker Compose to launch Infini

1. Make sure docker-compose is running:

   ```bash
   $ docker-compose --version
   ```

    If the terminal display the version of docker-compose, you can assume that docker-compose has been installed. The version of docker-compose is 1.24.1 in the following example:

    ```bash
    docker-compose version 1.24.1, build 4667896b
    ```

2. Download the config files to the same folder.

   ```bash
   $ wget https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/webserver/.env \
   https://raw.githubusercontent.com/zilliztech/infini/v0.5.0/config/webserver/docker-compose.yml

   ```

3. Modify the `.env` file.

   > Note：Update `192.168.1.60` to the IP address of the running Infini docker server. Update `192.168.1.106` to the IP address of the running MegaWise docker server.

   ```yml
   # default API server address
   API_URL=http://192.168.1.60:9000
   # default web server port
   LOCAL_PORT=80
   # megawise IP address
   MEGAWISE_HOST=192.168.1.106
   # megawise username
   MEGAWISE_USER=zilliz
   # megawise password
   MEGAWISE_PWD=zilliz
   # megawise database
   MEGAWISE_DB=postgres
   # megawise port
   MEGAWISE_PORT=5433
   ```

4. Launch the Infini web server.

   ```shell
   # start Infini
   $ docker-compose -f docker-compose.yml up
   ```

5. Open `/etc/hosts` and add the following content:

   > Note: Update `192.168.1.60` the IP address of the running Infini docker server. For Windows, the host file is in `C:\Windows\System32\drivers\etc\hosts`.

   ```shell
    #/etc/hosts
    192.168.1.60 infini
   ```

6. Launch a browser. Chrome and Firefox are recommended.

   ```shell
   # Please add the port number if you modified the 80 port
   http://192.168.1.60
   ```


## Configure the visualized interface


1. In the login page, enter username and password to log in.

   - Default username: zilliz
   - Default password: zilliz

2. After login, enter information for the MegaWise database. Save the information and jump to the dashboard page.


3. Click **New York Taxi Boards** to display the following page:

    ![New York Taxi data](../assets/nyc-demo.png)

    If you can see the previous page, you can assume that the Infini interface is successfully installed.


4. To stop the Infini interface, use the following command:

   ```bash
   # Stop Infini
   $ docker-compose -f docker-compose.yml down
   ```
