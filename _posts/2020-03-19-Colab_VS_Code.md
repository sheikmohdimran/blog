---
title: "# Fast.AI Course V4 with Google Colab and VS Code"
toc: true
layout: post
categories: [colab, fastai, VSCode]
authors: Sheik Mohamed Imran
---

The latest version of the popular Fast.AI course is now underway and due to the limitation of Corona Virus, this has moved online. Though the videos will be available to general public on July, you can still access the notebooks of the latest version here. 

```
https://github.com/fastai/course-v4
```

The course will be largely based out of the [book](https://www.amazon.com/Deep-Learning-Coders-fastai-PyTorch/dp/1492045527), which is scheduled to release on July 2020 as well.



Though Google Colab gives you a notebook "like" interface, it would be difficult in using this as a development environment. In this post we will see on how we can use the underling hardware of Google Colab to setup a VS Code based GPU/TPU environment.

To do this we will use the two services listed below.


- [Coder](https://coder.com/), which provides us with the option to run VS Code, accessible via browser through [Code-server](https://github.com/cdr/code-server).
- [Ngrok](https://ngrok.com/), which provides public and secure access by enabling URLs to your local application.



### Mount Google Drive

Mount your Google drive with the command below, you’ll be asked to enter an authorization code that will be provided as output of the cell below.

```python
from google.colab import drive
drive.mount('/content/gdrive/')
```



### Clone fastbook

Now clone the latest version of 'fastbook' from the github repository, into the Google Drive.

```console
!git clone https://github.com/fastai/course-v4 'drive/My Drive/course-v4'
```



### Download and unzip the required packages

We will need latest release of code-server and ngrok, to enable the setup. Let us download and decompress the files.

```console
!curl -sSL https://github.com/cdr/code-server/releases/download/2.1698/code-server2.1698-vsc1.41.1-linux-x86_64.tar.gz| tar -xvz
!wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
!unzip ngrok-stable-linux-amd64.zip
```



### Generate Authtoken

Create a free account in [Ngrok](https://ngrok.com/), this will be used forward the port where code-server runs. This step needs to be done to avoid too many connections error (greater than 20 connections within 60 seconds, which is a limitation for un-registered accounts)

```python
print("Get your authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()
```



### Create tunnel

```console
!./ngrok authtoken $authtoken & ./ngrok http 8443 --log=stdout & ./code-server2.1698-vsc1.41.1-linux-x86_64/code-server --port '8443' --auth 'none'
```

Output from the cell will have a link, ending with the format **http://<random_number>.ngrok.io**

![image-20200319194723416]({{site.baseurl}}/images/2020-03-19-Colab_VS_Code/\image-20200319194723416.png)



Access the URL to get a VS Code interface. Now let us enable ipython notebook support by installing the [Microsoft Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Go to 'Extensions' option in the VS Code, and search for 'Python' and click on 'Install'.

![image-20200319194735469]({{site.baseurl}}/images/2020-03-19-Colab_VS_Code/\image-20200319194735469.png)



Once done, reload the VS Code UI by clicking on 'Reload Required'. This will enable the installed plugin.

![image-20200319195926238]({{site.baseurl}}/images/2020-03-19-Colab_VS_Code/\image-20200319195926238.png)

To set the working directory, click on the 'Explorer' button, on the text box, type the directory where 'fasbook' was cloned initially.

![image-20200319204759447]({{site.baseurl}}/images/2020-03-19-Colab_VS_Code/\image-20200319204759447.png)

This will list all the notebooks on the left, select the notebook you want to work with and proceed as usual.

![image-20200319200242198]({{site.baseurl}}/images/2020-03-19-Colab_VS_Code/\image-20200319200242198.png)



You can also clone the notebook from [here](https://github.com/sheikmohdimran/Experiments_2020/blob/master/code_server.ipynb) into your account to see this live in action.

>**Note:** 
>
>Prior to starting any new notebook, ensure you execute the command below, to avoid import errors..
```console
!pip install -r requirements.txt
```



##### References:

* https://forums.fast.ai/
* https://unix.stackexchange.com/questions/478544/connect-to-vm-through-ssh-google-colab-and-putty
* https://imadelhanafi.com/posts/google_colal_server/