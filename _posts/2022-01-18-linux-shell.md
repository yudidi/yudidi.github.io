---
layout: post
title:
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
// 附录3
批量替换sh文件中的内容
```bash
#对发布的文件/做一些处理，版本号替换，修改权限等
sed -i -e "s/__BUILD_VERSION__/$VERSION/" bin/*.sh
```

4.1 有的sed -i -e的参数末尾有个/g
// 附录4 页面搜索/g
```bash
sed -i -e 's/123/456/g' sedtest.txt
```

4.2 sed使用示例
// 附录4

# 参考
1.[Linux--shell中$()命令](https://blog.csdn.net/sayhello_world/article/details/73496500)
2.[每天一个Linux命令（20）--find命令之exec](https://www.cnblogs.com/aaronax/p/5618024.html)
3.[使用sed -i --和sed -i -e 搜尋與取代文字](https://balian-ear.medium.com/%E4%BD%BF%E7%94%A8sed-i-%E5%92%8Csed-i-e-%E6%90%9C%E5%B0%8B%E8%88%87%E5%8F%96%E4%BB%A3%E6%96%87%E5%AD%97-74808dc91bb2)
4.[Linux sed 命令--和使用示例](https://www.runoob.com/linux/linux-comm-sed.html)
5.[`find <path> -name "*.sh" -exec chmod +x \{\} \;` 是如何工作的？](https://stackoverflow.com/questions/64401299/how-does-find-path-name-sh-exec-chmod-x-work)