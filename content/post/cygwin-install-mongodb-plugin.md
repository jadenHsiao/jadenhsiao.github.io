+++
title = "Cygwin为php安装mongodb拓展"
categories = ["Cygwin"]
tags = ["Cygwin"]
date = "2021-08-01T11:33:28+08:00"
draft = false
header_images="static/images/cygwin-install-mongodb-plugin.webp"
+++
>The purpose of this driver is to provide exceptionally thin glue between MongoDB and PHP, implementing only fundamental and performance-critical components necessary to build a fully-functional MongoDB driver.

上面的引用是摘抄自mongodb拓展在[pecl](http://pecl.php.net/package/mongodb)站点的描述，大致的意思就是该拓展为php和mongodb提供粘合剂功能，实现php链接和操作mongodb。下面就简单分享下Cygwin安装php的mongodb拓展的方法。

## 一、下载mongodb拓展源码包

![pecl的搜索页](/images/1626878736547.png "pecl的搜索页")
进入[pecl](http://pecl.php.net/)，搜索*mongodb*，因为我使用的环境是Cygwin提供的php7.3.7版本，所以选择搜索结果的*mongodb*，这里看到两个mongodb相关的拓展，第二个的mongo是php5.x所使用的，这里不做深入探讨。

![mongodb项目页面](/images/1626886675885.png "mongodb项目页面")
进入到*mongodb*拓展的项目页，选择最新的的稳定版1.10.0，复制下载链接，到Cygwin内用wget
下载拓展源码包。

![下载、解压、生成编译文件](/images/1626881168072.png "下载、解压、生成编译文件")
1.用wget下载mongodb拓展源码包
`wget http://pecl.php.net/get/mongodb-1.10.0.tgz`
2.解压源码包
`tar xzf mongodb-1.10.0.tgz`
3.进入源码目录并且执行生成编译文件
`cd mongodb-1.10.0`
`phpize`

## 二、执行配置
这里需要注意的是，执行配置时需要指定到Cygwin下php-config文件，指令如下。
`./configure --with-php-config=/bin/php-config`
![执行配置](/images/1626881635352.png "执行配置")

## 三、执行编译和安装
跑完执行配置后，就是最关键的编译和安装了，指令如下。
`make && make install`
![执行编译和安装](/images/1626882108514.png "执行编译和安装")
当你在等待他编译安装的时候，突然出现了编译出错，找不到函数。
![编译错误提示](/images/1626882187357.png "编译错误提示")

## 四、处理编译错误
从报错内容分析，在 "src/libmongoc/src/libmongoc/src/mongoc/mongoc-client.c" 这个文件下，存在有未被定义的函数 "__ns_initparse" 和 "__ns_parserr"，那还好办，找到对应的类库不就行了，结果在百度上翻了近10页搜索结果都没有解决方法，遇事不决，谷歌找，就在搜索结果的第二页，看到了*libspf2*的静态库，里面也是包含了 "__ns_initparse" 的相关函数，同时还提供了源码。
![libspf2](/images/1626882755980.png "libspf2")

![函数说明](/images/1626883015162.png "函数说明")

![源码](/images/1626883065373.png "源码")

新建一个c文件，为*ns.c*，文件内容就是*libspf2*提供的"__ns_initparse"源码，下面贴出源码内容：
```c
#ifndef lint
static const char rcsid[] = "$Id: ns_parse.c,v 1.3.2.1 2 6/27 03:51:42 marka Exp $";
#endif

/* Import. */

/* #include "port_before.h" */
#include "config.h"

#include <sys/types.h>

#include <netinet/in.h>

#include <errno.h>
/* #include <resolv.h> */
#ifdef HAVE_STRING_H
#include <string.h>             /* strstr / strdup */
#else
#ifdef HAVE_STRINGS_H
#include <strings.h>            /* strstr / strdup */
#endif
#endif


/* #include "port_after.h" */

/* Forward. */

static void setsection( ns_msg *msg, ns_sect sect );


/* Macros. */

#define RETERR( err ) do { errno = (err); return (-1); } while ( 0 )

/* Public. */

/* These need to be in the same order as the nres.h:ns_flag enum. */
struct _ns_flagdata _ns_flagdata[16] = {
	{ 0x8000, 15 },         /* qr. */
	{ 0x7800, 11 },         /* opcode. */
	{ 0x0400, 10 },         /* aa. */
	{ 0x0200, 9  },         /* tc. */
	{ 0x0100, 8  },         /* rd. */
	{ 0x0080, 7  },         /* ra. */
	{ 0x0040, 6  },         /* z. */
	{ 0x0020, 5  },         /* ad. */
	{ 0x0010, 4  },         /* cd. */
	{ 0x000f, 0  },         /* rcode. */
	{ 0x0000, 0  },         /* expansion (1/6). */
	{ 0x0000, 0  },         /* expansion (2/6). */
	{ 0x0000, 0  },         /* expansion (3/6). */
	{ 0x0000, 0  },         /* expansion (4/6). */
	{ 0x0000, 0  },         /* expansion (5/6). */
	{ 0x0000, 0  },         /* expansion (6/6). */
};

int
ns_skiprr( const u_char *ptr, const u_char *eom, ns_sect section, int count )
{
	const u_char *optr = ptr;

	for ( (void) NULL; count > 0; count-- )
	{
		int b, rdlength;

		b = dn_skipname( ptr, eom );
		if ( b < 0 )
			RETERR( EMSGSIZE );
		ptr += b /*Name*/ + NS_INT16SZ /*Type*/ + NS_INT16SZ /*Class*/;
		if ( section != ns_s_qd )
		{
			if ( ptr + NS_INT32SZ + NS_INT16SZ > eom )
				RETERR( EMSGSIZE );
			ptr += NS_INT32SZ /*TTL*/;
			NS_GET16( rdlength, ptr );
			ptr += rdlength /*RData*/;
		}
	}
	if ( ptr > eom )
		RETERR( EMSGSIZE );
	return(ptr - optr);
}


int
ns_initparse( const u_char *msg, int msglen, ns_msg *handle )
{
	const u_char	*eom = msg + msglen;
	int		i;

	memset( handle, 0x5e, sizeof *handle );
	handle->_msg	= msg;
	handle->_eom	= eom;
	if ( msg + NS_INT16SZ > eom )
		RETERR( EMSGSIZE );
	NS_GET16( handle->_id, msg );
	if ( msg + NS_INT16SZ > eom )
		RETERR( EMSGSIZE );
	NS_GET16( handle->_flags, msg );
	for ( i = 0; i < ns_s_max; i++ )
	{
		if ( msg + NS_INT16SZ > eom )
			RETERR( EMSGSIZE );
		NS_GET16( handle->_counts[i], msg );
	}
	for ( i = 0; i < ns_s_max; i++ )
		if ( handle->_counts[i] == 0 )
			handle->_sections[i] = NULL;
		else {
			int b = ns_skiprr( msg, eom, (ns_sect) i,
					   handle->_counts[i] );

			if ( b < 0 )
				return(-1);
			handle->_sections[i]	= msg;
			msg			+= b;
		}
	if ( msg != eom )
		RETERR( EMSGSIZE );
	setsection( handle, ns_s_max );
	return(0);
}


int
ns_parserr( ns_msg *handle, ns_sect section, int rrnum, ns_rr *rr )
{
	int	b;
	int	tmp;

	/* Make section right. */
	if ( (tmp = section) < 0 || section >= ns_s_max )
		RETERR( ENODEV );
	if ( section != handle->_sect )
		setsection( handle, section );

	/* Make rrnum right. */
	if ( rrnum == -1 )
		rrnum = handle->_rrnum;
	if ( rrnum < 0 || rrnum >= handle->_counts[(int) section] )
		RETERR( ENODEV );
	if ( rrnum < handle->_rrnum )
		setsection( handle, section );
	if ( rrnum > handle->_rrnum )
	{
		b = ns_skiprr( handle->_msg_ptr, handle->_eom, section,
			       rrnum - handle->_rrnum );

		if ( b < 0 )
			return(-1);
		handle->_msg_ptr	+= b;
		handle->_rrnum		= rrnum;
	}

	/* Do the parse. */
	b = dn_expand( handle->_msg, handle->_eom,
		       handle->_msg_ptr, rr->name, NS_MAXDNAME );
	if ( b < 0 )
		return(-1);
	handle->_msg_ptr += b;
	if ( handle->_msg_ptr + NS_INT16SZ + NS_INT16SZ > handle->_eom )
		RETERR( EMSGSIZE );
	NS_GET16( rr->type, handle->_msg_ptr );
	NS_GET16( rr->rr_class, handle->_msg_ptr );
	if ( section == ns_s_qd )
	{
		rr->ttl		= 0;
		rr->rdlength	= 0;
		rr->rdata	= NULL;
	} else {
		if ( handle->_msg_ptr + NS_INT32SZ + NS_INT16SZ > handle->_eom )
			RETERR( EMSGSIZE );
		NS_GET32( rr->ttl, handle->_msg_ptr );
		NS_GET16( rr->rdlength, handle->_msg_ptr );
		if ( handle->_msg_ptr + rr->rdlength > handle->_eom )
			RETERR( EMSGSIZE );
		rr->rdata		= handle->_msg_ptr;
		handle->_msg_ptr	+= rr->rdlength;
	}
	if ( ++handle->_rrnum > handle->_counts[(int) section] )
		setsection( handle, (ns_sect) ( (int) section + 1) );

	/* All done. */
	return(0);
}


/* Private. */

static void
setsection( ns_msg *msg, ns_sect sect )
{
	msg->_sect = sect;
	if ( sect == ns_s_max )
	{
		msg->_rrnum	= -1;
		msg->_msg_ptr	= NULL;
	} else {
		msg->_rrnum	= 0;
		msg->_msg_ptr	= msg->_sections[(int) sect];
	}
}
```
将其移动到"/src/libmongoc/src/libmongoc/src/mongoc"目录下，然后再打开同目录下的*mongoc-client.c*，在该文件的第一个条件编译结束下面添加文件引用，如下图。
![引入函数文件](/images/1626883539223.png "引入函数文件")

完成以上操作后，回到Cygwin，再次执行`make && make install`。
![编译安装完成](/images/1626883663846.png "编译安装完成")

## 五、添加配置到php.ini
打开Cygwin的php.ini，目录在 */etc* 下，加入*extension=mongodb*；最后再执行`php -m`查看列表是否存在mongodb，如果存在就安装完成了。
![执行php -m查看拓展](/images/1626886709530.png "执行php -m查看拓展")

![查看拓展具体信息](/images/1626884253615.png)


