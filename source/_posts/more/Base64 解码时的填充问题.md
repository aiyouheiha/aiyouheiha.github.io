---
title: Base64 解码时的填充问题
date: 2018-05-25 10:15:39
categories: "更多"
tags:
    - Base64
keywords: Base64
---

> 代码内容 From [io.jsonwebtoken.impl.Base64UrlCodec](https://github.com/jwtk/jjwt)

```java
protected char[] ensurePadding(char[] chars) {

    char[] result = chars; //assume argument in case no padding is necessary

    int paddingCount = 0;

    //fix for https://github.com/jwtk/jjwt/issues/31
    int remainder = chars.length % 4;
    if (remainder == 2 || remainder == 3) {
        paddingCount = 4 - remainder;
    }

    if (paddingCount > 0) {
        result = new char[chars.length + paddingCount];
        System.arraycopy(chars, 0, result, 0, chars.length);
        for (int i = 0; i < paddingCount; i++) {
            result[chars.length + i] = '=';
        }
    }

    return result;
}
```

## 疑惑

为什么是否需要填充的 if 语句判定条件是 `remainder == 2 || remainder == 3`，
那么 remainder 为 0 或者 1 的时候为什么不需要填充呢？

- 首先需要了解 Base64 的原理，可参考以下链接
  - https://segmentfault.com/a/1190000004533485
    - **PS** 评论提到了“为什么一定要补位到 6 和 8 的公倍数”的问题
    - 给出的答案是为了应对 **Base64 拼接** 的问题

## 不需要填充

假设原字符串长度为 `n`，则对应的二进制位长度 `8 * n`，当该长度是 `6` 的倍数时，不需要进行填充。

此时，编码后的二进制位长度应该是 `24` 的倍数：`24, 48, 72 ...`

对应字节数则应该是 `24 / 6 = 4` 的倍数：`4, 8, 12 ...`

**因此，在解码时，如果字节数是 `4` 的倍数（`remainder == 0`），不需要进行填充**

## 需要填充

对于需要填充的情况，除去完全填充位（不包含任何原二进制位的填充位），其长度是怎样的一种情况呢？

首先，二进制位总数必然是 `8` 的倍数，那么，当其除以 `6` 时，

- 要么整除，是 `24` 的倍数；
- 要么会余不超过 6 的二进制位

又根据余数可乘性

```
a 与 b 的乘积除以 c 的余数，等于 a, b 分别除以 c 的余数之积（或这个积除以 c 的余数）
```

余数应该是 `(2 * [0-5]) % 6` 也即 `0, 2, 4`，也即非整除仅有 `2, 4` 两种情况。

需要再填充一定的二进制位，使得其能够整除 `24`，
标准便是新增的位数需要能够整除 8 同时可以补足进行 Base64 划分的组（6位一组）

- **2** 添加 `4 + 6 + 6` 新增 16 位便可
- **4** 添加 `2 + 6` 新增 8 位即可

也就是说，完全填充仅有填充 1 或者填充 2 两种情况；

这也是为什么判定条件为 `remainder == 2 || remainder == 3`;

如果出现 `remainder == 1` （需要填充 3）这根本不可能是一个合法的 Base64 字符串

## 有点绕？

假设 `remainder == 1` 也即字节数为 `4 * n + 1`，对应的二进制位数为 `4 * n * 6 + 6`；

由于这是从 8 位字节转化而来的，出现这种形式的待完全填充字节序列，必然要符合如下命题：

- `4 * n * 6 + [1-6]` 能够整除 `8`

这显然是一个伪命题，得证。







