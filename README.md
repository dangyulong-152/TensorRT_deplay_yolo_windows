# TensorRT_deplay_yolo_windows
windows下TensorRT零基础部署yolo  

参考仓库：https://github.com/Guanbin-Huang/tensorRT_Pro_co-comments  

代码：https://github.com/Guanbin-Huang/tensorRT_Pro_co-comments/tree/main/simple_yolo/src  

只需要这几个代码文件
该仓库，以及，其提供的教程不是很完整，且有很多坑，本教程用来将onnx模型部署到win系统,win下运行成功  

一、环境配置
vs2017 或以上  

opencv 4.6 https://opencv.org/releases/  

tensorRT,cuda,cudnn配套即可 没有必要与onnx模型导出时的环境搭配  

cuda: https://developer.nvidia.cn/cuda-toolkit-archive  

cudnn: https://developer.nvidia.cn/rdp/cudnn-download 

tensorRT:https://developer.nvidia.cn/nvidia-tensorrt-download  

坑1：必须先安装vs,在安装cuda,这样可以在vs中集成cuda开发环境  

protobuf3.11.4:https://github.com/protocolbuffers/protobuf/releases?page=6  

注意：protobuf的版本是参考仓库指定的  

具体操作：  

1、安装cuda  

2、安装vs,勾选C+++环境必要的组件，勾选windowsSDk8.1  

3、解压缩或安装opencv,cudnn,tensorRT库  

4、为了方便配置vs属性表整理这几个库的目录  

创建环境目录：env  

目录结构：env下包括四个目录-----bin(dll文件)，include(包含文件),lib(lib文件),source(自己的代码目录)  

将 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v11.6下的内容复制到env  

   D:\library\opencv460\opencv\build下的内容复制到env  
   
   D:\library\opencv460\opencv\build\x64\vc15下的内容复制到env  
   
   D:\library\protoc-3.11.4-win64下的内容复制到env  
   
   D:\library\cudnn-windows-x86_64-8.4.1.50_cuda11.6-archive下的内容复制到env  
   
   D:\library\TensorRT-8.4.3.1.Windows10.x86_64.cuda-11.6.cudnn8.4\TensorRT-8.4.3.1下的内容复制到env  
   
   将env/lib下的dll文件复制到env/bin中  
   
   或者使用本人配好的环境(有可能不可以用，本人环境为cuda116,cudnn8.4,tensorRT8.4可能与客官电脑不适配，故建议客观自己配置环境)  
   
   本人环境链接：  
   
   注意：cuda一定要自己安装在电脑上以后再复制到该环境中(坑）  
   
二、用自己的vs创建环境：  

大坑：参考仓库没说这点，导致本人在这里挣扎了好久，  

不要用参考仓库的sln文件！不要用参考仓库的sln文件！不要用参考仓库的sln文件！额，事不过三，不说了。  

v编译下，会找不到stdio.h  

添加 包含目录：C:\Program Files (x86)\Windows Kits\10\Include\10.0.17763.0\ucrt  

项目->属性->配置属性->vc++目录->包含目录   添加：C:\Program Files (x86)\Windows Kits\10\Include\10.0.17763.0\ucrt  

项目->属性->配置属性->vc++目录->库目录   添加：C:\Program Files (x86)\Windows Kits\10\Lib\10.0.17763.0\ucrt\x64  

删除这个cu文件，应该是叫：kernal.cu  

把https://github.com/Guanbin-Huang/tensorRT_Pro_co-comments/tree/main/simple_yolo/src下的几个文件复制到下项目目录，也就是sln文件所在目录  

更改simpl_yolo.cu最上面的包含代码如下s在创建项目时选择visualc++->cuda***runtime 项目模板，这时候，项目中会产生个cu文件  


#************************************************#    

#include "simple_yolo.hpp"    
#include <NvInfer.h>    
#include <NvOnnxParser.h>    
#include <cuda_runtime.h>    
#include "cuda_runtime.h"    
#include "device_launch_parameters.h"    
#include "sm_20_atomic_functions.h"   
#include <stdio.h>  
#include <algorithm>     
#include <fstream>       
#include <memory>    
#include <string>      
#include <future>   
#include <condition_variable>    
#include <mutex>  
#include <thread>  
#include <queue>  

#if defined(_WIN32)  
#	include <Windows.h>  
#   include <wingdi.h>  
#	include <Shlwapi.h>  
#	pragma comment(lib, "shlwapi.lib")  
#	undef min  
#	undef max  
#else  
#	include <dirent.h>  
#	include <sys/types.h>  
#	include <sys/stat.h>  
#	include <unistd.h>  
#   include <stdarg.h>  
#endif  
#************************************************#  

  
二、导出onnx模型  
yolov5:https://github.com/ultralytics/yolov5  
yoloX :https://github.com/Megvii-BaseDetection/YOLOX  
yolov7:https://github.com/WongKinYiu/yolov7  
在导出onnx模型时需要修改一些代码，具体修改及导出onnx的方法参考视频：  
https://www.bilibili.com/video/BV1Xw411f7FW?p=7&spm_id_from=333.1007.top_right_bar_window_history.content.click  
注意：导出参数设置device=0  
  具体命令为：export --include=onnx device=0可能是这个，具体参考内yolo几个官方仓库  
三、编译运行  
1、将导出的onnx模型改名为：yolo5s_dynamic.onnx并复制到bin中，  
将https://github.com/Guanbin-Huang/tensorRT_Pro_co-comments/tree/main/simple_yolo/workspace中的文件复制到env/bin下编译运行即可  

