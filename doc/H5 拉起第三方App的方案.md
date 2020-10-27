# H5 拉起第三方 App 的方案

参考：
https://suanmei.github.io/2018/08/23/h5_call_app/

http://szuwest.github.io/tan-tao-cong-h5ye-mian-la-qi-appde-ji-zhu-fang-an-he-wen-ti.html


**需求背景**：

我们会经常遇到这样的需求，在 App 的 H5 页面中通过某种方式唤端第三方 App，如果用户手机没有安装相应的 App，通常会跳转至 App 下载页面。下面分析 H5 拉起第三方 App 的方案。


**两种技术方案**：

首先必须明确，技术方案不是绝对完美的。这两个技术适用不同的场景，也有不同的局限性。

一种通过**HTTP 链接**（`iOS`叫 `Universal Link`，安卓叫`APP Links`技术）拉起技术；

一种是通过**自定义`scheme`拉起技术**（也有叫深度链接`deeplink`的）。


**HTTP 链接方案**：

由于该方案安卓手机需要通过**谷歌服务器**验证（国内访问不了谷歌），所以这里不做介绍。
参考：http://szuwest.github.io/tan-tao-cong-h5ye-mian-la-qi-appde-ji-zhu-fang-an-he-wen-ti.html


**自定义`scheme`拉起技术**：

先看一下 **URL 格式**

```
[scheme:][//host][path][?query][#fragment]
```

`host`是可选的，`path`是必须的。一般都是通过`path`来定义指定打开`APP`特定的页面，同时通过`query`来携带参数。`HTTP`链接其实就是一种特殊的`scheme`

这种方式的做法是每个`APP`都自定义一个唯一的`scheme`，通过这个`scheme`来拉起`APP`。由于每个`APP`都会定义自己独有的`scheme`，所以一般还通过`scheme`来判断某个应用是否已安装在手机上。

H5 中的做法可以是 **a 标签跳转**，**window.location.href**, **iframe** ，后续介绍。
APP 端则需要相应的配置。


**自定义`scheme`拉起技术的问题**：

如果是自己开发的不同`App`相互跳转问题好解决，而如果像微信、微博等自带浏览器的超级`App`，会对`deeplink`技术做出限制，这种情况通常的做法是引导用户通过第三方浏览器跳转至第三方`App`，各式各样的**浏览器也有着不同的限制**，例如需要用户手动同意打开第三方`App`的操作等，所以也不是绝对完美的方案。


**浏览器限制问题举例**：

**Intent**
安卓的原生谷歌浏览器自从 **chrome25** 版本开始对于唤端功能做了一些变化，**URL Scheme** 无法再启动 Android 应用。 例如，通过 iframe 指向 weixin://，即使用户安装了微信也无法打开。所以，APP 需要实现谷歌官方提供的 **intent**: 语法，或者实现让用户通过自定义手势来打开 APP，当然这就是题外话了。


**三种唤端媒介**：

无论是 **URL Scheme** 还是 **Intent** ，他们都算是 URL ，只是 URL Scheme 和 Intent 算是特殊的 URL。所以我们可以拿使用 URL 的方法来使用它们。

- iframe

```html
<iframe src="sinaweibo://qrcode"></iframe>
```

在只有 URL Scheme 的日子里，iframe 是使用最多的了。因为在未安装 app 的情况下，不会去跳转错误页面。但是 iframe 在各个系统以及各个应用中的兼容问题还是挺多的，不能全部使用 URL Scheme。

- a 标签

```html
<a href="intent://scan/#Intent;scheme=zxing;package=com.google.zxing.client.android;end"">扫一扫</a>
```

- window.location

```js
window.location.href = "sinaweibo://qrcode";
```

以上三个媒介在不同设备都有可能唤端失败。


**开箱即用的 callapp-lib**

`callapp-lib` 是一个 `H5 `唤起 `APP `的解决方案，能够满足大部分唤起客户端的场景，也预留了扩展口，帮你实现一些定制化的功能。
地址：https://github.com/suanmei/callapp-lib
