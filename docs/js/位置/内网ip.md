---
title: 内网ip
date: 2023-11-12
info: 位置信息
tags:
  - 位置
---

# js获取内网ip

在JavaScript中，可以使用WebRTC的API来获取内网IP地址。

## 实现

```js
export const getLocalIPv4 = () => {
  return new Promise((resolve, reject) => {
    // 获取RTCPeerConnection构造函数
    const RTCPeerConnection =
      window.RTCPeerConnection || window.mozRTCPeerConnection || window.webkitRTCPeerConnection

    // 创建一个RTCPeerConnection对象，不配置ICE服务器
    const pc = new RTCPeerConnection({ iceServers: [] })

    // 创建一个空的数据通道，这是必需的，但不会实际使用
    pc.createDataChannel('')

    // 监听onicecandidate事件
    pc.onicecandidate = function (event) {
      // 如果候选者不存在或不是有效的候选者，则忽略
      if (!event || !event.candidate || !event.candidate.candidate) {
        return
      }

      // 使用正则表达式匹配IPv4地址
      const ipRegex = /([0-9]{1,3}(\.[0-9]{1,3}){3})/
      const match = event.candidate.candidate.match(ipRegex)

      // 如果找到了匹配的IP地址，则解析并返回
      if (match) {
        // 过滤掉非内网IP地址（例如，以192.168.、10.或172.16-31开头的IP地址是常见的内网IP地址）
        const ip = match[1]
        if (
          ip.startsWith('192.168.') ||
          ip.startsWith('10.') ||
          (ip.startsWith('172.') &&
            (ip.startsWith('172.16.') ||
              ip.startsWith('172.17.') ||
              ip.startsWith('172.18.') ||
              ip.startsWith('172.19.') ||
              ip.startsWith('172.20.') ||
              ip.startsWith('172.21.') ||
              ip.startsWith('172.22.') ||
              ip.startsWith('172.23.') ||
              ip.startsWith('172.24.') ||
              ip.startsWith('172.25.') ||
              ip.startsWith('172.26.') ||
              ip.startsWith('172.27.') ||
              ip.startsWith('172.28.') ||
              ip.startsWith('172.29.') ||
              ip.startsWith('172.30.') ||
              ip.startsWith('172.31.')))
        ) {
          resolve(ip)
        }
      }
    }

    // 创建一个Offer并设置本地描述，这是触发onicecandidate事件的必要步骤
    pc.createOffer()
      .then((offer) => {
        pc.setLocalDescription(offer)
      })
      .catch((error) => {
        reject(error)
      })
  })
}
```

## 使用

```js
getLocalIPv4()
  .then((ip) => {
    console.log('ip', ip)
  })
  .catch((error) => {
    console.error('Error getting local IPv4 address:', error)
  })
```

## 注意

可以获取到内网ip，但是需要`关闭浏览器的mDNS功能`才可以。  
**对于Google Chrome‌：**  
在浏览器地址栏中输入 chrome://flags/。  
搜索 #enable-webrtc-hide-local-ips-with-mdns。  
将该配置的属性改为 Disabled。  
点击页面下方的 Relaunch，重启浏览器。  
**‌对于Microsoft Edge‌：**  
在浏览器地址栏中输入 edge://flags/。  
搜索 #enable-webrtc-hide-local-ips-with-mdns。  
执行与Chrome相同的步骤。
