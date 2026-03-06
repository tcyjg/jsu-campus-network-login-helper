# 吉首大学校园网自动登录脚本

> 基于 Tampermonkey 油猴脚本，实现校园宿舍网登录页自动填写账号密码并提交登录。
> 当前版本：v1.3

---

## 环境要求

- 浏览器：Chrome / Edge / Firefox
- 插件：[Tampermonkey](https://www.tampermonkey.net/)

---

## 安装步骤

### 第一步：安装 Tampermonkey

前往 [https://www.tampermonkey.net/](https://www.tampermonkey.net/) 根据你的浏览器下载并安装对应版本的扩展。

### 第二步：新建脚本

1. 点击浏览器右上角的 Tampermonkey 图标
2. 选择 **「添加新脚本」**
3. 清空编辑器中的默认内容，粘贴以下代码：

```javascript
// ==UserScript==
// @name         吉首大学校园网自动登录
// @namespace    http://tampermonkey.net/
// @version      1.3
// @description  校园网自动登录
// @match        http://192.168.254.17/*
// @grant        none
// ==/UserScript==

(function () {
    'use strict';

    const USERNAME = '你的学号';          // ← 改成你的学号
    const PASSWORD = '你的校园ID密码';    // ← 改成你的密码
    const NETWORK  = '@cmcc';             // ← 按运营商修改，见下方说明

    // 注销页/已登录页不执行自动登录，避免注销后立即重新登录
    const title = document.title;
    if (title.includes('注销') || title.includes('成功')) {
        console.log('[自动登录] 当前是注销页，跳过自动登录');
        return;
    }

    function autoLogin() {
        if (!window.f1 || !window.ee || !window.f1.DDDDD || !window.f1.upass) {
            setTimeout(autoLogin, 500);
            return;
        }

        window.f1.DDDDD.value = USERNAME;
        window.f1.upass.value = PASSWORD;

        if (NETWORK) {
            const radio = document.querySelector(`input[name="network"][value="${NETWORK}"]`);
            if (radio) radio.click();
        }

        console.log('[自动登录] 提交登录...');
        window.ee(1);
    }

    window.addEventListener('load', () => setTimeout(autoLogin, 1500));
})();
```

### 第三步：修改账号信息

找到脚本中以下三行，替换为你自己的信息：

| 变量 | 说明 | 示例 |
|------|------|------|
| `USERNAME` | 校园 ID（即学号） | `'2021XXXXXXXX'` |
| `PASSWORD` | 已激活的校园 ID 密码 | `'yourPassword'` |
| `NETWORK` | 运营商后缀，见下表 | `'@cmcc'` |

**运营商对照表：**

| 值 | 运营商 |
|----|--------|
| `''` | 不选（默认校园内网） |
| `'@cmcc'` | 中国移动 |
| `'@unicom'` | 中国联通 |
| `'@telecom'` | 中国电信 |

### 第四步：保存并启用

按 `Ctrl + S` 保存脚本，确认 Tampermonkey 面板中该脚本状态为 **已启用**。

---

## 使用方法

连接校园宿舍网 Wi-Fi 后，**直接打开浏览器访问任意网页**，系统会自动跳转至登录页并完成登录，无需任何手动操作。

---

## 常见问题

**Q：脚本保存后没有自动登录？**

检查 `@match` 地址是否与实际登录页 URL 一致（默认为 `http://192.168.254.17/*`），以及脚本是否处于启用状态。

**Q：提示密码错误但密码正确？**

校园 ID 密码中若含有特殊字符，请修改为以下英文字符后再试：
```
! @ # $ % ^ & * ( ) _ + = - ~ [ ] { } | ; : " , . < > / ?
```

**Q：修改密码后登录失败？**

校园 ID 密码修改或重置后需等待约 **10 分钟**生效，请稍后重试并同步更新脚本中的 `PASSWORD`。

**Q：如何注销/切换账号登录？**
1. 首先关闭脚本
2. 访问校园网后，页面会显示注销按钮。点击后若弹出**"确定注销并解绑Mac?"**，说明系统检测到 Mac 绑定，直接点确定即可完成注销。

如需跳过弹窗直接注销，在 F12 控制台执行：
```javascript
logout.init();
```


## 相关链接

- [校园宿舍网使用指南](https://nic.jsu.edu.cn/fwzn/wljr/xsssrw/index.htm)
- [激活校园 ID](https://authserver.jsu.edu.cn/retrieve-password/accountActivation/index.html#/)
- [忘记校园 ID 密码](https://authserver.jsu.edu.cn/retrieve-password/retrievePassword/index.html#/)
- [自助服务系统](http://selfservice.jsu.edu.cn/Self/)

---

## 免责声明

本脚本仅供个人学习与便利使用，账号密码明文存储于脚本中，请勿在公共设备上使用，并注意保护个人信息安全。