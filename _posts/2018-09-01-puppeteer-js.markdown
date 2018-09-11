---
layout: post
title:  "Puppeteer 简单教程"
date:   2018-09-01 20:48:57 +0800
categories: javascript
---

Github地址：[Puppeteer]

使用Puppeteer可以做任何你能在Chrome里做的操作，点击按钮、输入指定的用户名、截屏、将页面导出为PDF。并且我们还能用最常用的类似document.querySelector的方法直接查询获取页面元素！
这些都可以在Nodejs环境下完成，也就是说爬虫、自动化测试都是它非常理想的应用场景。

{% highlight javascript %}
    // 获取submit按钮，并点击提交
    page.$eval('button[type=submit]', btn => btn.click());
{% endhighlight %}

### 第一步：启动

{% highlight javascript %}
    browser = await puppeteer.launch();
    
    // 启动已安装的Chrome
    browser = await puppeteer.launch({
      headless: false,
      executablePath: '/Applications/Google Chrome.app/Contents/MacOS/Google Chrome'
    });
{% endhighlight %}

Puppeteer 默认会安装最新版本的chrome，默认的启动选项为headless模式。headless就是没有GUI的Chrome，会在后台使用Blink引擎渲染页面的所有内容，包括执行js、加在css动画等所有（具体看这里[Chrome headless]。
Puppeteer 也可以选择启动已经安装了的Chrome版本，并且取消headless模式，这样就可以一边跑自动脚本一边观察页面上的变化了。

### 第二步：抓取页面元素

{% highlight javascript %}
    const inputUsername = await page.$('input[name=username]');
    await inputUsername.type(account.username);
    
    const inputPassword = await page.$('input[name=password]');
    await inputPassword.type(account.password);
    
    await page.$eval('button[type=submit]', el => el.click());
{% endhighlight %}

以上这些代码用于登录手机版Stage1st论坛。因为我想要抓取外野论坛的帖子，所以必须先要登录。用Puppeteer每次启动时都会清空本地的所有状态，所以这个登录操作是必须要做的。

{% highlight javascript %}
    await page.goto('https://bbs.saraba1st.com/2b/forum.php?mod=forumdisplay&fid=75');
    
    const threads = await page.$$eval('.threadlist a', els => els.map(el=> {
        return {title: el.textContent.trim(), link: el.href};
    }));
    
    threads.forEach((th,index) => {
      console.log(`${index + 1}  ${th.title.trim()}`);
    });
{% endhighlight %}

等待页面提交、跳转等登录过程完成之后。跳转到外野的论坛地址，然后从页面上抓取当前页面的所有帖子主题并且打印到Console。



[Puppeteer]: https://github.com/GoogleChrome/puppeteer
[Chrome headless]: https://developers.google.com/web/updates/2017/04/headless-chrome 
