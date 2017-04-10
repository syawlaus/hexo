title: Windows 批处理命令
date: 2016-08-12 22:00
categories: Batch
---

本文意在常用的 Windows 批处理命令。

<!-- more -->

#打开 Windows 资源管理器窗口

    start \YOUR_PATH

#删除文件/目录

    del FILE_PATH   删除文件
        /f             强制删除只读文件
        /p            删除每个文件前确认
        /q            删除文件，并无需确认
    
    del DIR_PATH    删除目录里的所有文件，不包括子目录
    
    rd  DIR_PATH
        /s             删除目录，包括所有子文件、子目录
        /q            删除，并无需确认

#复制文件/目录

    xcopy  SRC_DIR_PATH  DES_DIR_PATH   复制目录，只包含子文件
        /e                                复制目录和子目录，包括空目录
        /i                                 如果目标不存在，又在复制一个以上的文件，则假定目标一定是一个目录
        /y                                覆盖已有文件，且没有提示
    
    xcopy  SRC_FILE_PATH  DES_DIR_PATH
    
    xcopy  SRC_FILE_PATH  DES_FILE_PATH

#获取路径

    %cd%        当前盘符和路径
    %~d0        当前盘符
    %~dp0       当前盘符和路径（同 %cd%）
    %~f0        当前批处理文件全路径

#创建目录

    md

To be continued.
