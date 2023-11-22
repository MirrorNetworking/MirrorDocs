---
description: Walkthrough for using Oracle Free Tier hosting.
---

# Oracle Free Tier

## Create and configure your cloud machine

Access [Oracle](https://oracle.com) website. Click on **View accounts**, after click on **Sign up for free Cloud Tier**.

![](../../.gitbook/assets/image\_01.png)

Click on **Start for free**, fill the form and create your account. Sign in with your Oracle account, if your account is under validation, wait a few minutes to follow steps. Click on **Set up a network with a wizard**. Select **Create VCN with internet Connectitivity**, and click on **Start VCN Wizard**.

![](<../../.gitbook/assets/image\_02 (1).png>)

Fill **VCN name** and disable option **Use DNS hostname in this VCN**, click on **Next**, and wait.

![](../../.gitbook/assets/image\_03.png)

Click on **VCN name**, to create rules. Select **Public-Subnet-network**, and after click on **Default Security List for network**.

![](../../.gitbook/assets/image\_04.png)

Click on **Add Ingress Rules**, and create the follow rules for:

* Protocol TCP
  * Port range: 80, 443 and 7777
  * Select Stateless option
* Protocol UDP
  * Por range: 80, 443 and 7777
  * Select Stateless option

![](../../.gitbook/assets/image\_05.png)

Go to **Homepage** and click on **Create a VM Instance**. Fill **Computer instance name**, and click on **Edit**, in **Image and shape** group.

![](../../.gitbook/assets/image\_06.png)

Click on **Change image**, and select **Canonical Ubuntu**, OS Version **18.04**, and click on **Select Image**.

![](../../.gitbook/assets/image\_07.png)

In **Network group** check if the network is the one created in the previous steps.

![](../../.gitbook/assets/image\_08.png)

In **Add SSH Keys**, click on **Save Private Key**. Save this in a safe place, it will be necessary to access your machine.

![](../../.gitbook/assets/image\_09.png)

In Boot Volume you can specify HD Size, or you can use your 100 GB in one machine. Click on Create and wait. Go back to Home click on **Dashboard** > **Compute** > **Instance**, select your instance and copy **IP Address**.

![](../../.gitbook/assets/image\_10.png)

## Access your machine

Open powershell editor and type the follow command:

```
ssh -i [ssh_key_place] ubuntu@[machine_ip]
```

Example:

```
ssh -i c:\users\user\desktop\ssh-key-2021-09-15.key ubuntu@0.0.0.0
```

> If you get error to connect into your cloud machine, click with right button on ssh-key, select properties > security > advanced > remove inheritance, and delete all others users, keep only your windows user.

After connect, run the follow commands, to add firewall rules into ubuntu:

```
sudo apt install firewalld  
sudo firewall-cmd --zone=public --add-port=7777/tcp –permanent  
sudo firewall-cmd --zone=public --add-port=7777/udp –permanent  
sudo firewall-cmd –reload
```

Download Fillezila, and follow steps bellow to connect into your cloud machine, using your ssh-key:

[https://filezillapro.com/docs/v3/basic-usage-instructions/ssh-private-keys-for-sftp/](https://filezillapro.com/docs/v3/basic-usage-instructions/ssh-private-keys-for-sftp/)

## Create Build

In Unity, access **File** > **Build Settings** > **Select Linux** > **Mark Server Build**. Wait for build, and then upload your files with Fillezila.

![](../../.gitbook/assets/image\_11.png)

This tutorial use a server folder, but this is optional, use the follow commands:

```
ssh -i c:\users\user\desktop\ssh-key-2021-09-15.key ubuntu@0.0.0.0
cd server
chmod +x ./[your_project_name].x86_64  
./[your_project_name].x86_64
```

Your server is running
