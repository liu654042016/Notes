<!--
 * @Author: liu kang
 * @Date: 2024-10-14 17:11:58
 * @LastEditors: LiuKang
 * @LastEditTime: 2024-10-14 17:16:54
 * @FilePath: \Notes\work\vastai.md
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
# common
## docker 相关
拉镜像
```cpp
docker pull harbor.vastaitech.com/ai-compiler/ai_centos_x86_64_all_in_one_image:0.1.10
```
启动容器
```cpp
docker run -itd --net=host --privileged=true -v /home/vastai/kangliu/:/opt/soft/ -v /aitest:/aitest -v /home/vastai:/home/vastai -v /pipeline:/pipeline -v /external:/external -v /external2:/external2 -v /lib/modules:/lib/modules -v /usr/src:/usr/src  -v /dev:/dev  --entrypoint="/bin/bash" --name KL_TVM harbor.vastaitech.com/ai-compiler/ai_ubuntu18.04_x86_64_llvm9_develop_image:0.2.9
```