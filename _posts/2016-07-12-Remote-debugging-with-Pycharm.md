---
layout: post
title: "Remote debugging with Pycharm"
category: Python
tags: [心得,Pycharm,debug]
date: 2016-07-12
---

一、在远程计算机上，需要：

1. pydevd模块（在本地开发环境的PyCharm安装路径中找到pycharm-debug.egg文件（若远程计算机运行的是Python3，则需要pycharm-debug-py3k.egg），rename as pydevd for brevity.）

2. default.py文件（文件名不重要），内容如下：

   ```python
   #!/usr/bin/env python
   # coding=utf-8

   """ Sets the packages path and optionally starts the Python remote debugging client.

   The Python remote debugging client depends on the settings of the variables defined in debug_conf.py.
   Set these variables in debug_conf.py to enable/disable debugging using either the JetBrains PyCharm or Eclipse PyDev
   remote debugging packages which must be copied to packages/pydebug.
   """

   import os
   import sys

   module_dir = os.path.dirname(os.path.realpath(__file__))
   # packages = os.path.join(module_dir, u'packages')
   # sys.path.insert(0, module_dir)
   sys.path.append(os.path.join(module_dir, 'pydebug'))

   remote_debugging = {
       'client_package_location': os.path.join(module_dir, 'pydebug'),
       'is_enabled': False,
       'host': None,
       'stderr_to_server': False,
       'stdout_to_server': False,
       'port': 15679,
       'suspend': True,
       'trace_only_current_thread': False,
       'overwrite_prev_trace': False,
       'patch_multiprocessing': False}

   def configure_remote_debugging():

       configuration_file = os.path.join(module_dir, 'debug_conf.py')

       if not os.path.exists(configuration_file):
           return

       execfile(configuration_file, remote_debugging)

       if remote_debugging['is_enabled'] and os.path.exists(remote_debugging['client_package_location']):
           import pydevd
           try:
               pydevd.settrace(
                   remote_debugging['host'],
                   remote_debugging['stderr_to_server'],
                   remote_debugging['stdout_to_server'],
                   remote_debugging['port'],
                   remote_debugging['suspend'],
                   remote_debugging['trace_only_current_thread'],
                   remote_debugging['overwrite_prev_trace'],
                   remote_debugging['patch_multiprocessing'])
           except SystemExit as e:
               pass  # don't stop just because we couldn't connect to the debugger

   configure_remote_debugging()
   ```

3. debug_conf.py文件（所有可能需要修改的东西放在了这个文件里面~），内容如下：

   ```python
   #!/usr/bin/env python

   host = 'localhost'
   port = 15679
   suspend = False
   is_enabled = True
   ```


<!--break-->

二、把上述三个文件放在远程准备运行的文件的同一目录下，然后在准备运行的文件头部添加上`import default`（其实就是让remote debug的client运行起来）。

三、在本地需要对pycharm设置，添加remote debug，并修改其中的`Local host name`为本地机器的hostname或ip，`Port`修改为一个未被占用的端口号（比如12345），`Path mappings`中需要添加本地project的目录和remote机器上的目录的映射关系。

四、修改远程机器上的那个debug_conf.py文件，主要是修改其中的`host`和`port`，使之与步骤三中的一致。

五、在本地在选取了添加的remote debug后，打上必要的断点并点击debug按钮（其实就是让remote debug的server端先跑起来）。

六、在远程机器上运行准备debug的脚本。顺利的话本地pycharm应该会停在断点处，就和debug本地程序一样！