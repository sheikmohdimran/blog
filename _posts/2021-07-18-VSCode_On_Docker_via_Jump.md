---
title: "VS Code on Container"
toc: false
layout: post
categories: [JumpServer, Docker, VSCode]
comment: true
---

Long story short, in my workplace I had to use VSCode on a Container, running inside a docker host, which is accessible only via a Jump Server (security measures). Looked up at many tutorials on the net to get this done, but more or less all of them involved installing docker on my window laptop. Tinkered around a few steps and made it work. Below given is the step by step instruction on how to do this, if you had a similar problem.

![]({{site.baseurl}}/images/2021-07-18-VSCode_On_Docker_via_Jump/image-20210718203123454.png "Connecting to VSCode Server on Docker Container")

I had already setup passwordless login to the `Jump server` and `DockerHost`, there are already many articles on how to get this done, so I am going to skip that part.

So first, start the container on the docker host and setup SSH Server in the container.

```bash
#On Container:
#install ssh server
apt update && apt install  openssh-server 

#change root password
passwd 

#Enable to login as root via ssh
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

#Start SSH Server
/etc/init.d/ssh start
```

Once done get the IP address of the container with the following command

{% raw %}
```bash
#From Host
docker inspect -f "{{ .NetworkSettings.IPAddress }}" <DockerContainer_Name>
```
{% endraw %}

Check if the login works with the IP identified in the earlier step.

```bash
ssh <container_username>@<container_IP>
```

Next we need to install '[Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)' in our local system. This helps connecting your local VSCode with VSCode Server on the remote server. 

![SSH Architecture](https://code.visualstudio.com/assets/docs/remote/ssh/architecture-ssh.png)

Add necessary configurations in `ssh_config` of 'Remote - SSH', so that you can login without password to the main server 

```bash
Host JumpHost
  HostName <JumpHost_IP>
  User <JumpHost_username>
  IdentityFile <Location_of_id_rsa_configured_for_passwordless_login>
Host DockerHost
  HostName <DockerHost_IP>
  User <DockerHost_Username>
  ProxyCommand ssh.exe -q -W %h:%p Jump
  IdentityFile <Location_of_id_rsa_configured_for_passwordless_login>
Host DockerContainer
  HostName <DockerContainer_IP>
  User <DockerContainer_Username>
  ProxyCommand ssh.exe -q -W %h:%p DockerHost
```

Once the setup is completed, login to the docker container through VSCode.

If you are using Jupyter with VSCode ensure to [Disable experiments](https://github.com/microsoft/vscode-python/issues/14977#issuecomment-831304980) to make sure the python connect works with .

```bash
#Edit in settings.json

{
    "notebook.experimental.useMarkdownRenderer": false,
    "terminal.integrated.experimentalLinkProvider": false,
    "python.languageServer": "Pylance",
    "terminal.integrated.shell.linux": "/bin/bash",
    "python.defaultInterpreterPath": "/usr/bin/python3",
    "python.experiments.enabled": false
}
```



Note: If re-connecting to a same IP address with a new container:

```bash
Edit path of "known_hosts" given in output window to remove entry for the IP address.
```

