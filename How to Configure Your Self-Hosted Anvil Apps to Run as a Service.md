# Configuring Your Self-Hosted Anvil App to Run as a Service

### Introduction
Welcome to the guide on configuring your self-hosted Anvil app to run as a service! 
[Anvil](https://anvil.works/) is a platform for building full-stack web apps with nothing but Python. No need to wrestle with JS, HTML, CSS, Python, SQL and all their frameworks – just build it all in Python.

Anvil also offers [one-click deployment](https://anvil.works/docs/deployment/quickstart) to the cloud. Cloud deployment is, in the vast majority of scenarios, the quickest and easiest way to get apps up and running. However, we understand that there are scenarios in which you require your app to be deployed to your own server. To deploy your app to your own server, Anvil provides the open source [Anvil App Server](https://anvil.works/docs/deployment/quickstart).

You can read these excellent guides written by **Ryan** on [how to host anvil apps on your own server.](https://anvil.works/docs/how-to/app-server/cloud-deployment-guides) He covers deploying anvil apps to popular cloud services such as Google Compute Engine, DigitalOcean Droplet, Linode and more!

If you have gone through the trouble of hosting anvil app on your own server you might want to configure your app to run as a service so that it starts automatically whenever your server is restarted, ensuring seamless operation without manual intervention. This guide will show you how to set that up!

## Prerequisites
Before we begin, ensure you meet the following prerequisites:
- Comfortable executing commands on a Linux terminal.
- A running Anvil app on your server. If you have followed Ryan's guides, you are probably running your app using a command similar to this
```bash
~$ cd ~
~$ source env/bin/activate
~$ anvil-app-server --app CRUDnewsappwalkthrough --origin https://<your-app-domain>/
```

## Step 1 - Creating a systemd Configuration File
On Linux systems, [systemd](https://systemd.io) is an init system used to bootstrap userspace and manage user processes. At the time of its introduction it was controversial, but now is used by the majority of all modern Linux distributions.

So when you want to automatically run an application at every start of the Linux operating system, you need to create a configuration file. 
Open a terminal and execute the following command:

```bash
~$ sudo nano /etc/systemd/system/anvild.service
```
This will create and open ``anvild.service`` file in the ``/etc/systemd/system/`` directory with the [nano](https://www.nano-editor.org/docs.php) text editor. You can use `vi` to edit the file if you prefer. 

I am naming it ``anvild.service`` short for anvil daemon service. Of course, you can name it anything you want.



Type in or paste the following script into the file, replacing placeholders with your specific details:
```bash
[Unit]
Description=Anvil App Service

[Service]
User=your_username
WorkingDirectory=/path/to/your/app
ExecStart=/path/to/your/env/bin/anvil-app-server --app your_app_name --origin https://<your-app-domain>/

[Install]
WantedBy=multi-user.target
```
Hit ``CTRL + X`` then ``y`` to confirm the write and finally press ``Enter`` to save the file and exit out of nano editor.

Here is how my ``anvild.service`` looks like:
```bash
[Unit]
Description="Anvil App Service"

[Service]
User=bbytelab
WorkingDirectory=/home/bbytelab
ExecStart=/home/bbytelab/env/bin/anvil-app-server --app CRUDnewsappwalkthrough --port 80
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Note :
``multi-user.target`` normally defines a system state where 
all network services are started up the system will aceept logins. If your service is not wanted by anybody or no other service depends on it,
it will not start automatically at boot even if you have it enabled.

## Step 2 - Reloading systemd
When you edit any systemd script use the following command to load the changes:
```bash
~$ sudo systemctl daemon-reload
```

## Step 3 - Starting Your Service
You can now start your Anvil app service using the following command:
```bash
~$ sudo systemctl start anvild
```

## Step 4 - Enabling Auto Start at Boot
To ensure your service starts automatically at boot, run:
```bash
~$ sudo systemctl enable anvild
```
This should give following output indicating the symlink has been created.

```bash
Created symlink /etc/systemd/system/multi-user.target.wants/anvild.service → /etc/systemd/system/anvild.service.
```

Now if you reboot your server your anvil app should start automatically.


## Step 5 - Checking App Logs with journalctl

You can use the following command to view and tail your app logs:
```bash
~$ journalctl -u anvild.service -f
```
To learn more about journalctl you can check out this great tutorial at DigitalOcean Community on
[How To Use Journalctl to View and Manipulate Systemd Logs](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs)


### More Interaction with the Service
To check the status of your service, use:
```bash
~$ sudo systemctl status anvild
```
To stop the service, run:
```bash
~$ sudo systemctl stop anvild
```
To disable auto start at boot, run:
```bash
~$ sudo systemctl disable anvild
```

Congratulations! Your Anvil app is now configured to run as a service on your self-hosted server, ensuring automatic startup on system reboot.

If you face any difficulties following this guide, feel free to let me know!


