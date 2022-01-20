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


# 参考
1.[Linux--shell中$()命令](https://blog.csdn.net/sayhello_world/article/details/73496500)