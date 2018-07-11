---
title: Fault Record
date: 2018-07-11 15:20:05
categories: "更多"
tags:
    - fault
keywords: fault
---

## Memcached 缓存时间过长

- `6 * 30 * 24 * 60 * 60 = 15552000`
- `30 * 24 * 60 * 60 = 2592000`

```
set test 0 15552000 3
123
STORED
get test
END
set test 0 2592000 3
123
STORED
get test
VALUE test 0 3
123
END
```

- **Note** 保存时的提示为 `STORED` 成功，程序同样的也没有报错，可实际数据已经丢失
- **Fault Reason** 测试，测试，测试
- **More** 关于具体细节，有缘再做补充


