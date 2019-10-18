return
===

从函数中退出并返回数值。

## 概要

```shell
return [n]
```

## 主要用途

- 使得shell函数退出并返回数值，如果没有指定n的值，则默认为函数最后一条命令执行的返回状态。

## 参数

n（可选）：整数。

## 返回值

返回值为你指定的参数n的值，如果你指定的参数大于255或小于0，那么会通过加或减256的方式使得返回值总是处于0到255之间。

在函数外执行return语句会返回失败。

## 例子

```shell
#!/usr/bin/env bash
# 定义一个返回值大于255的函数
example() {
  return 259
}
# 执行函数
example
# 显示3
echo $?
```

### 注意

1. 该命令是bash内建命令，相关的帮助信息请查看`help`命令。


<!-- Linux命令行搜索引擎：https://github.com/wsdo/linux-complete-guide.git -->