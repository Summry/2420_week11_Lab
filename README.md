# <ins>**2420 Week 11 Lab**</ins>

This repo contains a script, a service file, and a timer file for each of  
1. **backing up directories from the current server to a backup server every Friday at 1am**
2. **displaying weather information of a specific city everyday at 5am**

In this README, we will be working with 1.

---

## <ins>**1. Table of Contents**</ins>
- [**2420 Week 11 Lab**](#2420-week-11-lab)
  - [**1. Table of Contents**](#1-table-of-contents)
  - [**2. Set B Members**](#2-set-b-members)
  - [**3. Technologies Used**](#3-technologies-used)
  - [**4. Assumptions**](#4-assumptions)
  - [**5. Files**](#5-files)
    - [**5.1. Installation**](#51-installation)
    - [**5.2. Copy files to server-one**](#52-copy-files-to-server-one)
    - [**5.3. Moving script**](#53-moving-script)
    - [**5.4. Moving unit files**](#54-moving-unit-files)
    - [**5.5. Setting up Config File**](#55-setting-up-config-file)
    - [**5.6. Check unit files' status**](#56-check-unit-files-status)
    - [**5.7. Usage**](#57-usage)
- [**Go to top**](#go-to-top)

---

## <ins>**2. Set B Members**</ins>

Nazira Fakhrurradi - A01279940  
(Doris) Yingyi He - A01230375  
(Kooby) Hai Run Yin - A01186094

---

## <ins>**3. Technologies Used**</ins>

- Bash
- Windows Subsystem for Linux (WSL) - Ubuntu
- DigitalOcean Droplets

---

## <ins>**4. Assumptions**</ins>

> **Note:** You may need to open Windows Terminal or Powershell as Administrator.  

1. You have installed **WSL Ubuntu** on your host machine and have created a regular user.  
You can connect to **WSL** from Windows Terminal or Powershell using either:  

    ```
    wsl
    ```

    or the recommended

    ```
    wsl --user <username>
    ```

1. You have watched, reviewed, and completed the DigitalOcean (DO) setup video that will help you create and connect to a main server **server-one** and a backup server **backup-server** in DO.  

2. You also have a regular user created within each server.

If you haven't done the above yet, please [click here](https://vimeo.com/758870226/f75da348fc?embedded=true&source=vimeo_logo&owner=17609105) and follow along the video before moving onto [**Files**](#files).

To verify that everything works perfectly,  
1. from your Windows Terminal or Powershell, connect to **WSL**, then
2. from **WSL**, ssh connect to **server-one**, then
3. from **server-one**, ssh connect to **backup-server**.

If you are able to verify the above, good job!

---

## <ins>**5. Files**</ins>

### <ins>**5.1. Installation**</ins>

Clone the repo to your desired directory:  
`git clone https://github.com/Summry/2420_week11_Lab.git`

---

### <ins>**5.2. Copy files to server-one**</ins>  

> **Note:** To make things easier for you, open a few terminals for different servers and connections.  

After cloning the repo, connect to **WSL** and move to the repo directory.

Then connect to **server-one** using `sftp`:  

![Using sftp to connect](images/sftp_connect.png "sftp")

Copy `backup-script`, `backup-script.service`, and `backup-script.timer` to `/home/username` that is in **server-one:**  

![Using sftp put](images/sftp_put.png "sftp put")

Output:  
![sftp output](images/sftp_put_output.png "sftp put output")

For more help, visit [here](https://vimeo.com/770519622/1955abff3e?embedded=true&source=vimeo_logo&owner=17609105) to learn all that you need about `sftp`.

---

### <ins>**5.3. Moving script**</ins>  

Copy `backup-script` into `/opt/backup`:  

```
username@server-one:~$ sudo cp backup-script /opt/backup
```

Output:  
![Copy script to /opt/backup](images/cp_script.png "Copied script")

---

### <ins>**5.4. Moving unit files**</ins>  

Copy `backup-script.service` and `backup-script.timer` into `/etc/systemd/system`:  

```
username@server-one:~$ sudo cp backup-script.service /etc/systemd/system
username@server-one:~$ sudo cp backup-script.timer /etc/systemd/system
```

Output:  
![Copy unit files to /etc/systemd/system](images/cp_unit_files.png "Copied unit files")

---

### <ins>**5.5. Setting up Config File**</ins>

In **server-one**, create a configuration file `server_one.conf` in `/etc`:  

```
username@server-one:~$ sudo vim /etc/server_one.conf
```

Inside the configuration file, append these two variables:

```
# Configuration file server_one.conf

DIRECTORIES=<directory/directories to backup>
IPADDR=<IP Address of your backup-server from DO>
```

> **Important:** This configuration file is where you manually reconfigure which directories you want to backup, and what IP Address to back it up to.

For example:  
![server_one.conf example](images/server_one_conf.png "Config file")

--- 

### <ins>**5.6. Check unit files' status**</ins>  

To confirm changes:  

```
username@server-one:/etc/systemd/system$ sudo systemctl daemon-reload
```

Start the service and check the status:  

```
username@server-one:/etc/systemd/system$ sudo systemctl start backup-script.service
username@server-one:/etc/systemd/system$ sudo systemctl status backup-script.service
```

Desired output:  
![status service output](images/service_status.png)

> **Warning:** If the status output displays some error, make sure your DO setup is working fine. Refer back to the videos.

Enable the service and timer:  

```
username@server-one:/etc/systemd/system$ sudo systemctl enable --now backup-script.service
username@server-one:/etc/systemd/system$ sudo systemctl enable --now backup-script.timer
```

---

### <ins>**5.7. Usage**</ins>  

> **Note:** To change to your own usernames, SSH key, and destination folder, simply edit `backup-script`.  
> For more information about `rsync`, click this [video](https://vimeo.com/770523139/c54cf132e7?embedded=true&source=vimeo_logo&owner=17609105).

Changing your timezone can be a pretty nice thing to do. That way the service will work with your current/local timezone instead of UTC.  
For example:  

```
username@server-one:~$ sudo timedatectl set-timezone America/Toronto
```

To test the script itself:  

```
username@server-one:~$ ./script
```

Example output:  
![Output of running the script](images/script_output.png "Output")

Checking the output:  
![Checking the output](images/script_output_check.png "Output check")

# [<ins>**Go to top**</ins>](#2420-week-11-lab)