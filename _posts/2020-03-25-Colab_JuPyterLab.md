---
title: "Google Colab with ipywidgets enabled JupyterLab"
toc: false
layout: post
categories: [colab, fastai, JupyterLab]
author: Sheik Mohamed Imran
comment: true
---

The latest version of the popular Fast.AI course is now underway and has moved [online](https://twitter.com/jeremyphoward/status/1236088745251581952), due to the restritions in-lieu Corona Pandemic. Though the videos will be available to general public on July, you can still access the notebooks of the latest version here. 

```
https://github.com/fastai/course-v4
```

The course will be largely based out of the [book](https://www.amazon.com/Deep-Learning-Coders-fastai-PyTorch/dp/1492045527), which is scheduled to release on July 2020 as well.



Though Google Colab gives you a notebook "like" interface, but without support for [ipywidgets](https://ipywidgets.readthedocs.io/en/latest/). JupyterLab supports this, lets see how to enable [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/) in Google Colab with [ngrok](https://ngrok.com/).

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

```
!npm cache clean -f
!npm install -g n 
!n stable
```



### Install JupyterLab and ipywidgets support

```
!pip install -q jupyterlab
!jupyter labextension install @jupyter-widgets/jupyterlab-manager
```



### Create tunnel

```console
!./ngrok authtoken $authtoken & ./ngrok http 8888 --log=stdout & jupyter lab --ip 0.0.0.0
```

Output from the cell will have a link, ending with the format **http://\<random_value>.ngrok.io**

![image-20200325190939174]({{site.baseurl}}/images/2020-03-25-Colab_JuPyterLab/image-20200325190939174.png)

Access the URL to get a JupyterLab Interface, check the 'ipywidget' support with the following code.

```python
from ipywidgets import interact, interactive, fixed, interact_manual
import ipywidgets as widgets

def f(x):
    return x

interact(f, x=10);
```


![image-20200325191036567]({{site.baseurl}}/images/2020-03-25-Colab_JuPyterLab/image-20200325191036567.png)



You can clone this [notebook](https://github.com/sheikmohdimran/Experiments_2020/blob/master/jupyter_lab.ipynb), and try all the steps listed above.