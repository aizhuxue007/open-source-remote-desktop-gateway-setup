# Open Source Remote Desktop Gateway Setup

## Project Overview
Use Apache Guacamole to create a remote desktop access server, with Apache Tomcat serving as the web application interface.

## Objectives
- [ ] **Objective 1**: Update and install dependencies
- [ ] **Objective 2**: Install and configure Guacamole server
- [ ] **Objective 3**: Set up Tomcat as the web interface
- [ ] **Objective 4**: Confirm the Guacamole interface works

## Setup Instructions

### 1. Prerequisites
   - **Operating System**: Ubuntu Server 24.04 or newer
   - **Internet Access**: Required
   - **Permissions**: Must have `sudo` privileges
   - **Environment**: SSH into your server for easy copying and pasting commands
   ![prerequisites]()

### 2. Update and Install Dependencies

   - **First**, update and upgrade the system packages:
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```

   - **Install Java** (required by Tomcat):
     ```bash
     sudo apt install -y openjdk-11-jdk
     ```

   - **Install Additional Dependencies** (required for building Guacamole):
     ```bash
     sudo apt install -y build-essential libcairo2-dev libjpeg-turbo8-dev libpng-dev libossp-uuid-dev \
         libavcodec-dev libavutil-dev libswscale-dev freerdp2-dev libpango1.0-dev \
         libssh2-1-dev libtelnet-dev libvncserver-dev libpulse-dev libssl-dev libvorbis-dev \
         libwebp-dev
     ```
![downloading dependencies]()
---

### 3. Set Up Tomcat

   - **Download and Extract Tomcat** (9.x is preferred for Guacamole compatibility):
     ```bash
     cd /opt
     sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz
     sudo tar -xzf apache-tomcat-9.0.96.tar.gz
     sudo mv apache-tomcat-9.0.96 tomcat
     ```

   - **Set Permissions**:
     ```bash
     sudo chown -R $USER:$USER /opt/tomcat
     ```

   - **Start Tomcat**:
     ```bash
     /opt/tomcat/bin/startup.sh
     ```
![tomcat setup]()
---

### 4. Install Guacamole and Initialize Server

   - **Download Guacamole Server Source**:
     ```bash
     cd ~
     sudo wget https://downloads.apache.org/guacamole/1.5.5/source/guacamole-server-1.5.5.tar.gz
     ```

   - **Move and Extract** the file:
     ```bash
     sudo mv guacamole-server-1.5.5.tar.gz /opt
     cd /opt
     sudo tar -xzf guacamole-server-1.5.5.tar.gz
     ```

   - **Build and Install**:
     ```bash
     cd guacamole-server-1.5.5
     sudo ./configure --with-init-dir=/etc/init.d
     sudo make
     sudo make install
     sudo ldconfig
     ```

   - **Initialize `guacd` Service**:
     ```bash
     sudo systemctl enable guacd
     sudo systemctl start guacd
     ```
![setup guac server]()
---

### 5. Set Up Guacamole Web Application

   - **Download the Guacamole Web Application (WAR file)**:
     ```bash
     wget -O guacamole.war https://downloads.apache.org/guacamole/1.5.5/binary/guacamole-1.5.5.war
     ```

   - **Move to Tomcat’s `webapps` Directory**:
     ```bash
     sudo mv guacamole.war /opt/tomcat/webapps/
     ```
![guac web app setup]()
---

### 6. Configure Guacamole Properties

   - **Create the Configuration Directory**:
     ```bash
     sudo mkdir /etc/guacamole
     ```

   - **Set Up `guacamole.properties` File**:
     ```bash
     sudo nano /etc/guacamole/guacamole.properties
     ```

     Add these lines:

     ```properties
     guacd-hostname: localhost
     guacd-port: 4822
     ```

   - **Link Configuration Directory to Tomcat**:
     ```bash
     sudo ln -s /etc/guacamole /opt/tomcat/.guacamole
     ```
![configure guac]()
---

### 7. Verify Interface Functionality

   - **Restart Tomcat**:
     ```bash
     /opt/tomcat/bin/shutdown.sh
     /opt/tomcat/bin/startup.sh
     ```

   - **Access the Guacamole Web Interface**:
     - Open a browser and go to `http://your_server_ip:8080/guacamole`.
     - Login with default credentials:
       - **Username**: `guacadmin`
       - **Password**: `guacadmin`

     _(Be sure to change the default password for security purposes.)_
![show login page]()
---

Congratulations! You should now see the Guacamole login screen and be able to access the remote desktop gateway.
