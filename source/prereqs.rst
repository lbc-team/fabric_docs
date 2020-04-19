先决条件
=============

在我们开始之前，您应该检查一下您已在平台上安装了以下所有必备的组件
因为您将在这个平台上搭建Hyperledger Fabric并开发区块链应用程序。

安装 Git
-----------
如果之前没有安装过git命令或者它运行起来有错，请从下面的地址下载最新版本的 `git
<https://git-scm.com/downloads>`_。

安装 cURL
------------

如果之前没有安装过cURL命令或者它运行起来有错，请从下面的地址下载最新版本的 `cURL
<https://curl.haxx.se/download.html>`__ 

.. note:: 如果您是运行再windows平台下，请参考下面的 `Windows
   extras`_ .

Docker and Docker Compose
-------------------------

您需要在您的Hyperledger Fabric系统上安装以下的组件:

  - MacOSX, \*nix, 和 Windows 10: `Docker <https://www.docker.com/get-docker>`__
    Docker 的版本需要在 17.06.2-ce 以上.
  - 比Windows 10低版本的Windows还需要安装: `Docker
    Toolbox <https://docs.docker.com/toolbox/toolbox_install_windows/>`__ -

您可以在终端的命令行中通过运行以下命令来检查已经安装的docker版本

.. code:: bash

  docker --version

.. note:: 下面的应用运行在有systemd的linux系统上。

启动docker的守候进程.

.. code:: bash

  sudo systemctl start docker

Optional: 如果您希望docker的售后进程跟随系统一起启动，请执行:

.. code:: bash

  sudo systemctl enable docker

增加用户到docker组中.

.. code:: bash

  sudo usermod -a -G docker <username>

.. note:: 在Mac或者Windows系统上安装Docker或者Docker Toolbox会同时安装Docker Compose
          所以，应该在已经安装过Docker的系统上，检查Docker Compose的版本应该在1.14.0以上
	  如果不是，请安装较新的版本。
	  
您可以在您的命令行终端中运行下面的命令来检查Docker Compose的版本:

.. code:: bash

  docker-compose --version

.. _Go:

Go 编程语言
-----------------------

Hyperledger Fabric 使用Go语言编写

  - `Go <https://golang.org/dl/>`__ 请安装1.13.x以上的Go语言。

考虑到我们将使用Go来编写链码程序，有两个您需要设置环境变量；您可以
通过将他们放在适当的启动文件中永久生效，例如
作为您的个人`~/.bashrc``文件，如果您使用的是``bash`` shell在Linux下。

首先, 您需要设置环境变量 ``GOPATH`` 来指定链码的工作路径:

.. code:: bash

  export GOPATH=$HOME/go

.. note:: 您 **必须** 设置GOPATH环境变量

  尽管在Linux中，Go的“GOPATH”变量可以是冒号分隔的列表，如果未设置，则将使用默认值`$HOME/go`，
  当前的Fabric构建框架仍然要求您设置并声明该变量，且只能包含**一个**目录。
  （此限制可能会在以后的版本中删除。）

其次，您应该在（或者在启动文件中）命令的搜索路径中包含Go的``bin``目录，如下所示Linux下的“bash”示例：

.. code:: bash

  export PATH=$PATH:$GOPATH/bin

如果上面的路径不存在，这条命令本身不会报错，但是当我们build我们的Fabric系统时，就会弹出错误。
所以请确保它存在，并且设置正确。

Node.js Runtime and NPM
-----------------------

如果您将使用Node.js版本的Hyperledger Fabric SDK开发，请使用
8.9.4及更高版本的Node.js 8，10.15.3及更高版本支持Node.js 10。

  - `Node.js <https://nodejs.org/en/download/>`__ 下载

.. note:: 安装Node.js也将安装NPM，但是请您确认已经安装的NPM版本。你可以
	  使用以下命令升级 “npm”：

.. code:: bash

  npm install npm@5.6.0 -g

Python
^^^^^^

.. note:: 以下仅适用于Ubuntu16.04用户。

默认情况下，Ubuntu 16.04系统会安装python3.5.1。
Fabric Node.js SDK的“npm install”``需要Python 2.7。使用请使用以下命令查看python2的版本：

.. code:: bash

  sudo apt-get install python

Check your version(s):

.. code:: bash

  python --version

.. _windows-extras:

Windows extras
--------------

如果您是在Windows7上开发，并且使用Docker的启动终端。那么它默认会调用一个老版本的“Git”
Bash<https://git scm.com/downloads>``经验表明它是一个功能不全的环境。它是适合运行基于Docker的场景，
例如:doc:`getting_started`, 但在使用时会遇到问题，比如运行：“make”和“docker”命令。 

因此，我们建议在MSYS2的shell环境中运行make和docker命令。 所以, `安装
MSYS2 <https://github.com/msys2/msys2/wiki/MSYS2-installation>`__
(用pacman来安装开发工具链和gcc包) 并且用下面命令来启动Docker Toolbox :

::

   /c/Program\ Files/Docker\ Toolbox/start.sh

或者, 您也可以更改Docker的启动终端设置，让他使用MSYS2 bash:

::

   "C:\Program Files\Git\bin\bash.exe" --login -i "C:\Program Files\Docker Toolbox\start.sh"

to:

::

   "C:\msys64\usr\bin\bash.exe" --login -i "C:\Program Files\Docker Toolbox\start.sh"

通过上面的更改，您现在可以容易地启动Docker Quickstart然后得到一个可用的环境。

在Windows 10上，您应该使用本机Docker发行版可以使用Windows PowerShell。但是，对于 ``binaries`` 
命令要成功，您仍然需要使用 ``uname`` 命令可用。你可以把它作为Git的一部分，但是要注意只有
支持64位版本。

在运行 ``git clone`` 命令之前, 请先运行下面命令:

::

    git config --global core.autocrlf false
    git config --global core.longpaths true

您可以通过下面命令检查git的设置:

::

    git config --get core.autocrlf
    git config --get core.longpaths

These need to be ``false`` and ``true`` respectively.

Docker Toolbox命令自带的 ``curl`` 命令版本有点旧，不能正确应用在 :doc:`getting_started`
中。所以请从 `cURL
<https://curl.haxx.se/download.html>`__页面中安装新的版本。

对于 Node.js 您需要 Visual Studio C++ Build 工具
他们可以用下面命令免费获得:

.. code:: bash

	  npm install --global windows-build-tools

参照 `NPM windows-build-tools 页面
<https://www.npmjs.com/package/windows-build-tools>`__ 获得更多细节。

一旦上面命令运行成功，您可以用下面命令获得NPM GRPC模块:

.. code:: bash

	  npm install --global grpc

到这一步，您的环境应该可以运行
:doc:`getting_started` 中的示例和教程了。

.. note:: 如果您有本文档未解决的问题，或遇到
	  有关任何教程的问题，请访问：doc:`questions`有关在何处查找其他帮助的一些提示。

.. Licensed under Creative Commons Attribution 4.0 International License
   https://creativecommons.org/licenses/by/4.0/

Fabric 中文文档由登链社区发起翻译，未经授权请勿转载。
译者：[Alex](https://learnblockchain.cn/people/377)， 校对： [李大狗](https://learnblockchain.cn/people/127)