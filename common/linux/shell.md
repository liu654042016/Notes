<!--
 * @Author: liu kang
 * @Date: 2023-04-21 12:23:59
 * @LastEditors: LiuKang
 * @LastEditTime: 2023-04-21 12:24:05
 * @FilePath: \Notes\common\linux\shell.md
 * @Description: 
 * 
 * Copyright (c) 2023 by ${git_name_email}, All Rights Reserved. 
-->

# Common 

* ## [tar](https://blog.csdn.net/example440982/article/details/51712973)

  tar -cvf test.tar test  #仅打包，不压缩
  tar -zcvf test.tar.gz test  #打包后，以gzip压缩 在参数f后面的压缩文件名是自己取的，习惯上用tar来做，如果加z参数，则以tar.gz 或tgz来代表gzip压缩过的tar file文件
  tar -zcvf dist.tar.gz -C dist .  #注意最后有一点，通过 -C 参数指定目录，可以去掉打包内容包含相对目录，打包内容只包含 dist/* 下的文件内容，不包含 dist 目录
