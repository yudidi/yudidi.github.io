---
layout: post
title: linux shell或命令行
categories: [shell]
tags: [shell]
---

# 背景
最近看同事写的linux-shell脚本,发现好多语法看不懂.这里补习一下.

1. shell函数调用

2. 拼接cmdmingl 

```cmd
funHasString() {
  result=$(echo "$1" | grep "$2")
  if [[ "$result" != "" ]]; then
    return 0
  else
    return 1
  fi
}

heathCheck() {
  serverFlag=0
  canalFlag=0
  for ((i = 0; i < 25; i++)); do
    sleep 1

    st1=$("$CMD" -c "$CONF_FILE" status "$NAME_SERVER")
    echo "$st1"

    funHasString "$st1" "RUNNING"

     if [[ $? -eq 0 ]]; then
          echo 'server 启动成功'
          serverFlag=1
    fi
```

3.批量给某个目录下后缀sh的文件执行命令.
// 附录2
// 附录5
```bash
# 批量给sh的文件赋权限
find . -name '*.sh' -exec chmod +x {} \;
```

4. 使用 sed -i --和sed -i -e 搜尋與取代文字
// 附录3,6,7
批量替换sh文件中的内容
```bash
#对发布的文件/做一些处理，版本号替换，修改权限等
sed -i -e "s/__BUILD_VERSION__/$VERSION/" bin/*.sh

# 当我想替换文件中的路径时，使用转义符号（\）会比较麻烦且可读性很差。
# 比如要将abc.sh中的/a/b/c替换成/d/e/f，命令如下：
# 分隔符由/换成#（其他字符也可以，只要跟在s命令后面即可）：
sed -i 's#/a/b/c#/d/e/f#g'
```

4.1 有的sed -i -e的参数末尾有个/g
// 附录4 页面搜索/g
```bash
sed -i -e 's/123/456/g' sedtest.txt
```

4.2 sed使用示例
// 附录4


# 5.将条件判断（if语句）写在bash命令行中
// 附录8
```bash
ps -ef | grep eureka |grep -v grep ;if [ $? == 0 ]; then ps -ef | grep eureka |grep -v grep |awk ‘{print $2}’|xargs kill -9; fi
```

# Grep在匹配行之前显示文件名和行号
```bash
grep -n --with-filename 'pattern' file
```

# 参考
1.[Linux--shell中$()命令](https://blog.csdn.net/sayhello_world/article/details/73496500)
2.[每天一个Linux命令（20）--find命令之exec](https://www.cnblogs.com/aaronax/p/5618024.html)
3.[使用sed -i --和sed -i -e 搜尋與取代文字](https://balian-ear.medium.com/%E4%BD%BF%E7%94%A8sed-i-%E5%92%8Csed-i-e-%E6%90%9C%E5%B0%8B%E8%88%87%E5%8F%96%E4%BB%A3%E6%96%87%E5%AD%97-74808dc91bb2)
4.[Linux sed 命令--和使用示例](https://www.runoob.com/linux/linux-comm-sed.html)
5.[`find <path> -name "*.sh" -exec chmod +x \{\} \;` 是如何工作的？](https://stackoverflow.com/questions/64401299/how-does-find-path-name-sh-exec-chmod-x-work)
6.[sed替换内容中有斜杠该怎么处理](https://blog.csdn.net/xingjingb/article/details/118075915)
7.[mac 执行sed -i指令时，总是出现extra characters at the end of command](https://blog.csdn.net/lgh1117/article/details/50094595)
8.[将条件判断（if语句）写在bash命令行中](https://blog.csdn.net/qq_31977125/article/details/88545675)
  [Linux(二)：巧用分号在命令行执行逻辑判断语句](https://blog.csdn.net/yiifaa/article/details/77844387?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-7.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-7.pc_relevant_default&utm_relevant_index=10)
9.[Grep在匹配行之前显示文件名？](https://cloud.tencent.com/developer/ask/28017)