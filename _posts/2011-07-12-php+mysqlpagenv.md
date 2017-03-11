---
layout: post
title:  "php+mysql分页显示内容"
date:   2011-07-12 21:06:05
categories: Web
tags: php 分页
excerpt: php+mysql内容分页代码。
---

最近在写应用，期间要用到分页查询，一下是一些代码，记下备用。
在文件开始设置变量：

```php
$num_of_page=30; //每页显示多少个
if (isset($_GET['pg']))   //获取浏览器变量，判断当前页数
{
    $pages=($_GET['pg']-1)*$num_of_page;
    $nowpage=$_GET['pg'];
}else{
    $pages=0;
    $nowpage=1;
}
```

然后就是通过MySQL连接到数据库获取一页数据，并且分页显示出来，代码如下:

```php
$dbc=mysqli_connect($hostname,$username,$password,$database) or die('数据库连接失败，请联系应用管理员，感谢！');
$query="SELECT SQL_CALC_FOUND_ROWS * FROM $tablename WHERE ownname='$myname'  ORDER BY id DESC LIMIT $pages,$num_of_page";  //查询出符合条件的数据  SQL_CALC_FOUND_ROWS 配合下个查询用的，用于计算符合条件的数据行数
$query2="SELECT found_rows() AS rowcount"; //这个用于获取一共有多少行数据，便于计算有多少页
$result=mysqli_query($dbc,$query) or die('查询数据库失败，请联系应用管理员，感谢！');
$result2=mysqli_query($dbc,$query2) or die('查询数据库失败，请联系应用管理员，感谢！');
$total=mysqli_fetch_array($result2); //获取一共有多少数据
$total_num=(int)$total['rowcount'];//将获取的结果赋值到到简单的变量里

if($total)
{
    echo "一共有$total个数据<br>";
    while($getrow=mysqli_fetch_array($result))
    {
         //.....用$getrow数组将数据根据需要显示出来 

           $i=$total/$num_of_page;
               if (!is_int($i))   //判断一共有几页
               {
                  $i=(int)$i+1;
               }
               echo "<div class=\"classname\">当前第".$nowpage."页 共".$i."页:"; //显示开始提示,设置分页CSS
                   for ($j=1;$j<=$i;$j++)
     {    
        if($j==$nowpage)
        {
        echo "<span class=\"current\">".$j."</span>";// 当前页的页码,不显示链接
        }else{
        echo " <a href=\"".$_SERVER['PHP_SELF']."?pg=".$j."\">".$j."</a> ";//非当前页页码显示为链接
        }    
      } 
               echo "</div>";
    }
}else{
     echo "没有数据";
}

mysqli_close($dbc);//关闭mysql连接
```

分页可以应用CSS效果，网上有很多分页效果的CSS文件下载，自己做一个也不是很复杂。

大致意思就是酱紫。
