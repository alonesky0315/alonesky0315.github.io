---
layout: post
title: 利用python的pywin32 库将 main.py 作为系统服务运行
category: Python
keywords: Common Python
tags: Common Python
description: 
---

#### 利用python的pywin32 库将 main.py 作为系统服务运行
>  在 Windows 系统中，不借助第三方工具，你可以使用 Python 的 win32service、win32serviceutil 和 win32event 等模块（这些模块来自 pywin32 库）将 main.py 作为系统服务运行。

1. 安装 pywin32 库
```bash
pip install pywin32
```

2. 创建服务脚本  
```python
import win32serviceutil
import win32service
import win32event
import servicemanager
import subprocess
import os
import sys
class PythonScriptService(win32serviceutil.ServiceFramework):
        _svc_name_ = "PythonMainScriptService"
        _svc_display_name_ = "Python Main Script Service"
        _svc_description_ = "Runs the main.py Python script as a Windows service."

        def __init__(self, args):
                win32serviceutil.ServiceFramework.__init__(self, args)
                self.hWaitStop = win32event.CreateEvent(None, 0, 0, None)
                self.process = None

        def SvcStop(self):
                self.ReportServiceStatus(win32service.SERVICE_STOP_PENDING)
                if self.process:
                        try:
                                self.process.terminate()
                        except Exception as e:
                                servicemanager.LogMsg(servicemanager.EVENTLOG_ERROR_TYPE,
                                                                            servicemanager.PYS_SERVICE_FAILED,
                                                                            (self._svc_name_, str(e)))
                win32event.SetEvent(self.hWaitStop)

        def SvcDoRun(self):
                servicemanager.LogMsg(servicemanager.EVENTLOG_INFORMATION_TYPE,
                                                            servicemanager.PYS_SERVICE_STARTED,
                                                            (self._svc_name_, ''))
                script_dir = os.path.dirname(os.path.abspath(__file__))
                main_script_path = os.path.join(script_dir, 'main.py')
                try:
                        self.process = subprocess.Popen(['python', main_script_path],
                                                                                        cwd=script_dir)
                        win32event.WaitForSingleObject(self.hWaitStop, win32event.INFINITE)
                except Exception as e:
                        servicemanager.LogMsg(servicemanager.EVENTLOG_ERROR_TYPE,
                                                                    servicemanager.PYS_SERVICE_FAILED,
                                                                    (self._svc_name_, str(e)))
if __name__ == '__main__':
        if len(sys.argv) == 1:
                servicemanager.Initialize()
                servicemanager.PrepareToHostSingle(PythonScriptService)
                servicemanager.StartServiceCtrlDispatcher()
        else:
                win32serviceutil.HandleCommandLine(PythonScriptService)
```
3. 代码解释
	* 类定义：PythonScriptService 类继承自 win32serviceutil.ServiceFramework，用于定义 Windows 服务的行为。
	* __init__ 方法：初始化服务，并创建一个事件对象 hWaitStop 用于停止服务。
	* SvcStop 方法：当服务停止时，该方法会被调用。它会尝试终止 main.py 脚本的进程，并设置事件对象 hWaitStop。
	* SvcDoRun 方法：当服务启动时，该方法会被调用。它会使用 subprocess.Popen 启动 main.py 脚本，并等待事件对象 hWaitStop 被设置。
	* 主程序：根据命令行参数的数量，决定是启动服务控制分发器还是处理命令行命令（如安装、卸载、启动、停止服务等）。
4. 安装和管理服务 

	打开命令提示符（**以管理员身份运行**），进入 service_wrapper.py 所在的目录
	- 安装服务
	```bash
	python service_wrapper.py install
	```
	- 启动服务
	```bash
	python service_wrapper.py start
	```
	- 停止服务
	```bash
	python service_wrapper.py stop
	```
	- 卸载服务
	```bash
	python service_wrapper.py remove
	```

通过以上步骤，你就可以将 main.py 作为 Windows 系统服务运行，并且可以方便地进行安装、启动、停止和卸载操作。