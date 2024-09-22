---
title: 使用 Cloudflare 在 Gmail 上配置域名邮箱
date: 2024-07-31 16:17:34
# updated: 2023-03-26 16:16:34
# description: 希望以后能多写一点博文
tags: 
    - 博文
    - 域名邮箱
    - Cloudflare
categories: 叶子的小知识
comment: true
keywords: cloudflare, gmail, custom domain, email
# sticky: 100

# hide: true/index
# author: 叶子
# email: clover@mskclover.com
hideTime: false
readmore: false
# ermaid: true
---

<img src="/images/custom-email-on-gmail-by-cloudflare/cover.jpg" width="450" alt="cover" />


<!-- more -->

## 前言

在去年初，我在[这篇周记](https://mskclover.com/2023/04/09/2023-04-Week-14/)里讲我配置了「域名邮箱的接收」，但是因为我犯懒，就一直没有配置其发送。最近看到一个关注许久的博客出了问题，无法留言，就趁此机会把**「域名邮箱的发送」**配置啦。本篇文章的所有配置步骤，均参考 **数字游民** 的这篇教程：[如何使用 Cloudflare 配置域名邮箱收发邮件](https://blog.shuziyimin.org/1355#comment-8798/)（备份 [于此](https://web.archive.org/web/20240916150632/https://blog.shuziyimin.org/1355#comment-8798/)），并根据自己的经历作了一点小小的修改。

整个步骤大体上可以分为两个部分：

- 配置域名邮箱接收邮件，并转发到自己的邮箱（如 Gmail）；
- 配置域名邮箱发送邮件，该功能依托其他大型邮件服务商（如 Gmail）。



## 选择 Cloudflare 作为自己的域名托管商

在我之前的文章里，有说过为什么选择 **Cloudflare** 作为我的域名托管商。最初我的域名是托管在 **[Namecheap](https://www.namecheap.com/)** 上的，不过由于首年便宜，续费太贵的缘故，我就将域名转移到了 Cloudflare 上了 —— 这是偷偷摸摸做的，那时忙着做实验室的项目，所以忘记写一篇博文来纪念一下了，好遗憾的。Cloudflare 在[这篇博客](https://blog.cloudflare.com/cloudflare-registrar/)里说过他们**为什么**会开始自己做域名服务的托管，也说了他们为什么**不会**像其他公司一样将**续费价格翻好几倍**，当然也说了他们为什么承诺为每一位用户提供 **免费 SSL** 等服务。我真的好感动，Cloudflare 为大家创造了一个更好的互联网，难怪许多人都愿意尊称他们为**「赛博佛祖」**。

![registrar-pricing](/images/custom-email-on-gmail-by-cloudflare/registrar-pricing.jpg)_Cloudflare 所列出的顶级域名收费明细_

如果自己有域名，需要将其转移到 Cloudflare 上托管。如果没有域名，那直接在 Cloudflare 上注册一个也未尝不可。



## 设置 Cloudflare Email Routing 路由规则

登陆 Cloudflare 的 **dash/控制台** 页面，其左栏应有 **Email/电子邮件** 一项，在下拉列表的 **电子邮件路由 — 路由规则** 处有：

![catch-all](/images/custom-email-on-gmail-by-cloudflare/catch-all.jpg)

可以打开该功能，会使得所有发给你域名的邮件都会应用后续规则，也就是都会转发到你指定的目标邮箱。该功能是可选项，非必须。我参考的教程上，有这样写道：

> 比较在乎隐私保护的朋友可以使用这个服务，比如在注册各种网站服务时用服务名称临时编个前缀，注册 craft 时就用 craft@example.com；注册 disney+ 时就用 disney@example.com。 这样收到垃圾邮件时，可以知道是哪家服务商把你的信息泄露了，也可以根据收件人来拒收邮件。

而在页面下方有：

![creat-email](/images/custom-email-on-gmail-by-cloudflare/creat-email.jpg)

可以点击右侧的 **创建地址** 会出现：

<img src="/images/custom-email-on-gmail-by-cloudflare/set-action.jpg" width="600" alt="DNS" />

在「自定义地址」处填入自己想要的邮箱地址，在「操作」处填入 发送到电子邮件（即转发功能），在「目标位置」填入自己的邮箱以后，我们便设置好了。接下来 Cloudflare 会给你的邮箱发送一封确认邮件，点击其中的链接即可验证身份，到此配置完毕。



## 添加 Cloudflare 的 MX 记录

在配置好以上邮箱与路由规则以后，在 **Email/电子邮件** 处点击「添加记录」，就可以将 MX 记录一键导入到 **DNS — 记录** 中，参考类型如下：

![DNS](/images/custom-email-on-gmail-by-cloudflare/DNS.jpg)



## 配置 Gmail 以域名邮箱的名义发送邮件

我认为在网络世界中，邮箱名字链接了太多的个人信息，这也是个人隐私的一部份。所以，我想要在某些时候，以域名邮箱的名义发送邮件，比如域名邮箱接收到他人来信的时候 —— 他人发邮件给你的域名邮箱，总不好用另外一个邮箱回复别人吧（

下面，我继续参考上述教程，以 Gmail 为基础，配置代理域名邮箱的发送功能：



### 获取谷歌账户的专属应用密码

在浏览器中打开链接：https://myaccount.google.com/apppasswords：

![apppasswords](/images/custom-email-on-gmail-by-cloudflare/apppasswords.jpg)_谷歌的中文排版为什么总是喜欢逼死强迫症？_

填入一个「自定义信息」后，就会弹出窗口「系統產生的應用程式密碼」，并显示一串密码。注意：**一定要复制并妥善保存这个密码**，因为在你点击「确认」以后，你就再也看不到这个密码了。

我填入的自定义信息是：Gmail-Cloudflare-mail，以供参考。



### 配置 Gmail 发送邮件地址

登入 Gmail，在其右上角找到齿轮 **设置 — 查看所有設定 — 帳戶和匯入**，在「選擇寄件地址」一栏找到「新增另一個電子郵件地址」：

![add-another-email](/images/custom-email-on-gmail-by-cloudflare/add-another-email.jpg)

点击「新增另一個電子郵件地址」后，会出现弹窗：

![enter-email](/images/custom-email-on-gmail-by-cloudflare/enter-email.jpg)

请注意：

> - 邮箱名字会用于之后发邮件的默认名，会对外展示，请慎重填写；
> - 域名邮箱地址，请事先在 Cloudflare 中配置此前缀域名邮箱，确认可以接收邮件。

完成填写后，点击「下一步」：

![add-account](/images/custom-email-on-gmail-by-cloudflare/add-account.jpg)

> - SMTP 需要填写 smtp.gmail.com；
> - port 端口保持默认即可，如果需要变更协议，端口需要做相应变更；
> - username 填写原本 Gmail 的用户名，即邮箱地址中除去 @gmail.com 之外的信息；
> - password 需要使用在步骤 4.1 中获取的专属应用密码；

如果上述步骤没有问题，成功进入下一个页面会看到：

![confirm](/images/custom-email-on-gmail-by-cloudflare/confirm.jpg)_因为我已经建好了域名邮箱，所以截取的是教程中的图片_

此时，Gmail 会收到一封邮件，在邮件中点击链接，或者在此输入邮件中的验证码，即可配置完成。未来在使用 Gmail 发送邮件的时候，可以在发件人处选择发送该封邮件的地址。

以上，希望你一切顺利 ：)













