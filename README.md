# 随意的一点作业

## 核心规则描述

我们有这样一段类似的 yaml

```yaml
route:
  routes:
  - name: abc
    match:
      alertname: DeadMansSwitch
    continue: true
  - name: abc2
    match_re:
      alertname: DeadMansSwitch.*?
    continue:
  - name: abc3
    match:
      ultraman: DeadMansSwitch
    routes:
    - name: abc4
      match:
        height: 100
    continue: false
```

我们有一个 root field 为 route，其子字段为 routes

routes 是一个数组，数组中的每个元素都是一个 route，其字段如下

1. name 为 string，unique
2. match 是一个 kv 对，key 为 string，value 为 string
3. match_re 是一个 kv 对，key 为 string，value 为 string，value 为一个正则表达式
4. routes 是一个数组，数组中的每个元素都是一个 route
5. continue 为 bool，如果为 true，则继续匹配，如果为 false，则不再匹配

我们匹配的输入源为一个 kv 对，key 为 string，value 为 string

```json
{
  "alertname": "DeadMansSwitch",
  "height": "100"
}
```

我们的匹配规则为

1. 如果 routes 中的某个 route 的 match kv 对中的所有 key 都在输入源中，且对应的 value 都相等，则匹配成功
2. 如果 routes 中的某个 route 的 match_re kv 对中的所有 key 都在输入源中，且对应的 value 都符合正则表达式，则匹配成功
3. 如果当前 route 匹配成功，则继续匹配子 routes 规则，规则同上
4. 如果当前 route 匹配成功，且 continue 为 false，则不再匹配后续的 route
5. 如果当前 route 匹配成功，且 continue 为 true，则继续匹配后续的 route

## 作业要求

### 作业1

实现一个命令行工具叫作 bot，接收一个 yaml 文件路径，和一个 json 文件路径，输出匹配成功的 route name

其调用规则如下

```bash

./bot valite --config-file=example.yaml --input-file=example.json

```

同时支持一个命令，树形输出 yaml 文件的内容

```bash
./bot print --tree --config-file=example.yaml
```

### 作业2

基于作业2，实现并行解析 json 文件，输出匹配成功的 route name

```bash
./bot valite --config-file=example.yaml --input-file=example.json --parallel=10
```

### 作业3

基于作业3，实现一个简易的 HTTP Server，其 Server 配置如下

```yaml
port: 10086
file-path: /validate/upload
json-path: /validate/json
```

其启动命令如下

```bash
./bot server --server-config-file=server.yaml --config-file=example.yaml
```

该服务支持用户通过 **application/json** 的方式上传验证单挑 JSON，也支持上传一个包含多条待验证 JSON 的文件


### 作业4

基于作业3，实现可以通过 CLI 上传文件的功能，命令格式自拟，通过 BOT_HTTP_ENDPOINT 环境变量来实现 HTTP Server 的地址配置
