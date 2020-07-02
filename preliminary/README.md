# Preliminary

## Contest questions

<https://kcode-git.kuaishou.com/kcode/KcodeRpcMonitor/-/blob/master/README.md>

## Local score

- prepare : 34s
- checkPair : 210ms (100w)
- checkResponder : 88ms (100w)

> 配置：Inter i5-6200U 2.30GHz, Plextor 256GB SSD

## Tricks

1. service 无法做到像热身赛那样直接映射为一个小范围内的 int 值，可以使用一个 `int[][]` 做映射。观察发现每个服务名都是由一个 `service+数字` 组成，并且每个 service 的首字母各不相同，因此可将第一维设为首字母，第二维设为结尾的数字，并且结尾数字可以仅使用从倒数第五位开始的两位数字表示，故映射数组为：int[26][100] -> [1,80] (实际只需要 `int[21][100] -> [1,80]`)

2. ip 共有 450 个，观察发现每个 ip 均为 `10.` 开头的内网地址，并且与服务有着一对多的关系，每个 ip 只对应唯一一个服务，每个服务最多对应 9 个 ip，因此可以先将 ip 映射到 [1, 450] (`int [255][255][255] -> [1, 450]`)，再将 ipIdx 映射到 [1, 9]

3. `checkPair` 与 `checkResponder` 输入均包括被调服务名，因此可以在处理时首先按被调服务名进行处理，例如 `pairData[][][][][]` 数组中的第一维就是被调服务名，第二维是主调服务名，这样就可以同时处理二三阶段数据

4. 方法耗时范围均在 [0, 298] 之间，适合 Bucket sort，寻找 p99 时只需从结尾依次寻找即可

5. 调用成功率可以事先保存为一个 `String[10001]` 的数组，下标为 `成功率 * 10000 的 int 值`

6. `checkResponder` 阶段需要返回一段时间范围内的被调成功率，因此可以事先穷举所有情况，避免在查询时计算

7. 服务名的干扰项都是以 `Err` 结尾，可以根据倒数第三位是否 `E` 直接判断
