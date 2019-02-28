
# Nginx 📖学习笔记

> 作者: maxwell 日期: 2019-01-24

![nginx][nginx_logo]

- [Nginx 📖学习笔记](#nginx-📖学习笔记)
  - [1. Rrewirte](#1-rewrite)
  - [2. Location](#2-location)
    - [2.1. 💎顺序优先级：](#21-💎顺序优先级)

## 1. Rewrite

```nginx
Syntax: rewrite regex replacement [flag];
Default: —
Context: server, location, if
```

如果请求的 URI 与正则匹配，URI 将会根据 `replacement` 规则进行替换 `rewrite` 指令也会按照出现顺序依次执行，如果以 `"http://"`、`"https://"`、`"$scheme"` 开头，会立即重定向到目标地址。

可选参数 `flag` 解析:

- `last`: 停止处理当前指令，并搜寻与之匹配的  `location`。
- `break`：停止处理当前指令。
- `redirect`： 暂时重定向并返回302编码。
- `permanent`：永久重定向并返回301编码。

## 2. Location

```nginx
Syntax: location [ = | ~ | ~* | ^~ ] uri { ... }
        location @name { ... }
Default: —
Context: server, location
```

- =  表示精确匹配
- ^~ 表示非正则表达匹配
- ~* 表示正则表达匹配（大小写不敏感）
- ~  表示正则表达匹配（大小写敏感）

为了匹配最佳配置：会优先匹配带有前缀的 `location`，其中，匹配前缀中最长 `location` 配置会被标记位置，接下来会按照正则排列顺序依次检查，如果与之匹配则使用，否则使用先记住的规则。如果 `location` 拥有 "^~" ，则不继续往下进行正则检查，使用 "=" 修饰符会精确匹配，因此不能嵌套 `location` 。

### 2.1. 💎顺序优先级：

1. `location` =
2. `location` 完整路径 不是只匹配 URI 的头部
3. `location` ^~ 路径
4. `location` ~,~* 正则顺序
5. `location` 部分起始路径 pattern 匹配 URI 的头部
6. `location` /

[nginx_logo]: http://ww1.sinaimg.cn/large/ac1cf9b4gy1fzhogfhsd7j20he040wep.jpg