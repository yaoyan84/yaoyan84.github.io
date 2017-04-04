---
layout: post
title:  "printf格式化输出"
date:   2015-07-25 22:16:25
categories: 
tags: shell
excerpt: 使用printf格式化输出
---

最近需要脚本统计每个目录中有多少文件，并按照一定格式显示出来。

使用printf可以格式化输出。 [详细用法](http://man.linuxde.net/printf)



```
#!/bin/sh
taget_dir="/Users/yaoyan/shell"

for dir in $(find ${taget_dir} -type d)
do
    dir_name=$(echo "${dir}" | awk -F '/' '{print $5}')
    file_num=$(find ${dir} -type f | wc -l)
    #echo "${dir_name}:${file_num}"
    printf "%-25s:%6s\n" ${dir_name} ${file_num}
done
```


![](/images/posts/2017/printf.png)

```
%-25s  :宽度25字符，左对齐
%6s:     宽度6字符，右对齐
```



