---
title: 外网ip
date: 2024-12-31
info: 位置信息
tags:
  - 位置
---

# js获取外网ip

在JavaScript中获取外网IP地址，通常需要通过调用外部服务提供的API来实现。

## 实现

```js
export const getIp = () => {
  return new Promise((resolve, reject) => {
    fetch('https://ipinfo.io/json')
      .then((response) => {
        if (!response.ok) {
          throw new Error('Network response was not ok')
        }
        return response.json()
      })
      .then((data) => {
        resolve(data.ip)
      })
      .catch((error) => {
        reject(error.message)
      })
  })
}
```

## 使用

```js
getIp()
  .then((ip) => {
    console.log('ip', ip)
  })
  .catch((error) => {
    console.error('Error getting IP networks outside:', error)
  })
```

## 注意

流量收费问题
