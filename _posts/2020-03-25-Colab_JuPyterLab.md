---
title: "Google Colab with ipywidgets enabled JupyterLab"
toc: false
layout: post
categories: [colab, fastai, JupyterLab]
author: Sheik Mohamed Imran
comment: true
---

We have already seen in this [post](https://sheikmohdimran.github.io/blog/colab/fastai/vscode/2020/03/19/Colab_VS_Code.html), on how to run [Fast.AI V4 notebooks](https://github.com/fastai/course-v4) through VSCode interface in Google Colab. Though Colab gives you a notebook "like" interface, but there isn't support for [ipywidgets](https://ipywidgets.readthedocs.io/en/latest/). 

You can access in JupyterLab, but need to enable it. Lets see how to access [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) using Google Colab hardware with [ngrok](https://ngrok.com/).

### Mount Google Drive

Mount your Google drive with the command below, you’ll be asked to enter an authorization code that will be provided as output of the cell below.

```python
from google.colab import drive
drive.mount('/content/gdrive/')
```



### Download and unzip the ngrok packages

Let us download and decompress the ngrok files.

```
!wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
!unzip ngrok-stable-linux-amd64.zip
```



### Generate Authtoken

Create a free account in [Ngrok](https://ngrok.com/), and copy the generate Authentication token.

```python
print("Get your authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()
```



### Upgrade node.js to latest stable version

The default version of node.js installed in Colab doesn't support the installation of ipywidgets. We will upgrade its version with the below commands.

```
!npm cache clean -f
!npm install -g n 
!n stable
```



### Install JupyterLab and ipywidgets support

Now we install the jupyterlab package via pip. Once done we will install the widgets extension.

```
!pip install -q jupyterlab
!jupyter labextension install @jupyter-widgets/jupyterlab-manager
```



### Create tunnel

Lets launch a JupyterLab instance and forward the port to ngrok.

```console
!./ngrok authtoken $authtoken & ./ngrok http 8888 --log=stdout & jupyter lab --ip 0.0.0.0
```



Output from the cell will have a link, ending with the format **http://\<random_value>.ngrok.io**

![image-20200325190939174]({{site.baseurl}}/images/2020-03-25-Colab_JuPyterLab/image-20200325190939174.png)



Access the URL to get a JupyterLab Interface using the link above and check the 'ipywidget' support with the following code.

```python
from ipywidgets import interact, interactive, fixed, interact_manual
import ipywidgets as widgets

def f(x):
    return x

interact(f, x=10);
```


![image-20200325191036567]({{site.baseurl}}/images/2020-03-25-Colab_JuPyterLab/image-20200325191036567.png)



> Note: You can also clone this [notebook](https://github.com/sheikmohdimran/Experiments_2020/blob/master/jupyter_lab.ipynb), and try all the steps listed above.