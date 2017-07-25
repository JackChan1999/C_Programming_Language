## C动态库升级成框架案例

函数指针反向调用，通过函数指针实现C语言的面向对象编程

回调函数：利用函数指针做函数参数，实现的一种调用机制，具体任务的实现者，可以不知道什么时候被调用。

回调机制原理：

- 当具体事件发生时，调用者通过函数指针调用具体函数
- 回调机制的将调用者和被调函数分开，两者互不依赖
- 任务的实现和任务的调用可以耦合（提前进行接口的封装和设计）

### socketclient.c

```c
#define  _CRT_SECURE_NO_WARNINGS 
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "itcast_comm.h"
#include "itcastlog.h"

typedef int (*EncData)(unsigned char *inData,
                       int inDataLen,
                       unsigned char *outData,
                       int *outDataLen,
                       void *Ref, int RefLen);
typedef int (*DecData)(unsigned char *inData,
                       int inDataLen,
                       unsigned char *outData,
                       int *outDataLen,
                       void *Ref, 
                       int RefLen);

typedef struct _SCK_HANDLE {
	char		version[16];
	char		serverip[16];
	int		    serverport;
	unsigned char *	buf ;
	int				buflen;

	//加密函数入口地址
	EncData		encDataFunc;
	void		*enc_ref;
	int			enc_refLen;

	//加密函数入口地址
	DecData		decDataFunc;
	void		*dec_ref;
	int			dec_refLen;
}SCK_HANDLE;

//客户端初始化
ITCAST_FUNC_EXPORT(int)
cltSocketInit(void **handle) //5day after
{
	SCK_HANDLE		*sh = NULL;
	int				rv = 0;

	if (handle == NULL)
	{
		rv = -1;
		return rv;
	}
	//分配内存并初始化
	sh = (SCK_HANDLE *)malloc(sizeof(SCK_HANDLE));
	if (sh == NULL)
	{
		rv = -2;
		return rv;
	}
	memset(sh, 0, sizeof(SCK_HANDLE));

	//域赋值
	strcpy(sh->version, "0.0.1");
	strcpy(sh->serverip, "192.168.0.211");
	sh->serverport = 8888;

	//传出
	*handle = sh;
	return rv;
}

//客户端发报文
ITCAST_FUNC_EXPORT(int)
cltSocketSend(void *handle, unsigned char *buf,  int buflen)
{
	int				rv = 0;
	SCK_HANDLE		*sh = NULL;
	unsigned char	bufdata[4096];
	int				bufdatalen = 4096;

	if (handle == NULL)
	{
		rv = -4;
		return rv;
	}
	if (buf == NULL || buflen<=0)
	{
		rv = -5;
		return rv;
	}

	sh = (SCK_HANDLE *)handle;

	//数据加密 有回调函数
	if (sh->encDataFunc != NULL)
	{
		rv = sh->encDataFunc(buf, buflen, 
                             bufdata, 
                             &bufdatalen, 
                             sh->enc_ref, 
                             sh->enc_refLen);
		if (rv != 0)
		{
			return rv;
		}
		//把加密的密文 存储 handle 上下文之中
		sh->buf = (unsigned char *)malloc(sizeof(unsigned char)*bufdatalen);
		memcpy(sh->buf, bufdata, bufdatalen);
		sh->buflen = bufdatalen;
	}
	else
	{
		sh->buf = (unsigned char *)malloc(sizeof(unsigned char)*buflen);
		if (sh->buf == NULL)
		{
			rv = -6;
			return rv;
		}

		//把发送的报文数据，存储 handle 上下文之中
		memcpy(sh->buf, buf, buflen);
		sh->buflen = buflen;
	}
	return rv;
}

//客户端发报文
ITCAST_FUNC_EXPORT(int)
cltSocket_EncAndSend(void *handle, 
                     unsigned char *buf,  
                     int buflen, 
                     EncData encDataCallback, 
                     void *Ref, 
                     int RefLen)
{
	int				rv = 0;
	SCK_HANDLE		*sh = NULL;
	unsigned char	bufdata[4096];
	int				bufdatalen = 4096;

	if (handle == NULL)
	{
		rv = -4;
		return rv;
	}
	if (buf == NULL || buflen<=0)
	{
		rv = -5;
		return rv;
	}

	//先加密
	if (encDataCallback != NULL)
	{
		rv = encDataCallback(buf, 
                             buflen, 
                             bufdata, 
                             &bufdatalen, 
                             Ref, 
                             RefLen); //回调函数调用
		if (rv != 0)
		{
			return rv;
		}
	}

	sh = (SCK_HANDLE *)handle;
	sh->buf = (unsigned char *)malloc(sizeof(char)*bufdatalen);
	if (sh->buf == NULL)
	{
		rv = -6;
		return rv;
	}

	//把发送的报文数据，存储 handle 上下文之中
	memcpy(sh->buf, bufdata, bufdatalen);
	sh->buflen = bufdatalen;

	return rv;
}

//客户端收报文
ITCAST_FUNC_EXPORT(int)
cltSocketRev(void *handle, unsigned char *buf, int *buflen)
{
	int			rv = 0;
	SCK_HANDLE	*sh = NULL;

	if (handle == NULL)
	{
		rv = -4;
		return rv;
	}
	if (buflen == NULL)
	{
		rv = -5;
		return rv;
	}

	sh = (SCK_HANDLE *)handle;

	//赋值 把上下文中的数据，copy到buf空间中
	//支持二次调用，第一次调用求长度 第二次调用可以把数据copy buf中
	if (buf != NULL)
	{
		memcpy(buf, sh->buf, sh->buflen);
		//buf[ci->buflen] = '\0';
	}
	*buflen = sh->buflen;
	return rv;
}
//客户端释放资源
ITCAST_FUNC_EXPORT(int)
cltSocketDestory(void *handle)
{
	SCK_HANDLE *sh = NULL;
	sh = handle; 
	if (sh != NULL)
	{
		free(sh->buf);
		free(sh);
	}
	return 0;
}

/////////////////第二套api实现/////////////////////

//客户端初始化
ITCAST_FUNC_EXPORT(int)
cltSocketInit2(void **handle) //5day after
{
	SCK_HANDLE		*sh = NULL;
	int				rv = 0;


	if (handle == NULL)
	{
		rv = -1;
		return rv;
	}
	//分配内存并初始化
	sh = (SCK_HANDLE *)malloc(sizeof(SCK_HANDLE));
	if (sh == NULL)
	{
		rv = -2;
		return rv;
	}
	memset(sh, 0, sizeof(SCK_HANDLE));

	//域赋值
	strcpy(sh->version, "0.0.1");
	strcpy(sh->serverip, "192.168.0.211");
	sh->serverport = 8888;

	//传出
	*handle = sh;
	return rv;
}

//客户端发报文
ITCAST_FUNC_EXPORT(int)
cltSocketSend2(void *handle, unsigned char *buf,  int buflen)
{
	int				rv = 0;
	SCK_HANDLE		*sh = NULL;

	if (handle == NULL)
	{
		rv = -4;
		return rv;
	}
	if (buf == NULL || buflen<=0)
	{
		rv = -5;
		return rv;
	}

	sh = (SCK_HANDLE *)handle;
	sh->buf = (unsigned char *)malloc(sizeof(char)*buflen);
	if (sh->buf == NULL)
	{
		rv = -6;
		return rv;
	}

	//把发送的报文数据，存储 handle 上下文之中
	memcpy(sh->buf, buf, buflen);
	sh->buflen = buflen;

	return rv;
}
//客户端收报文
ITCAST_FUNC_EXPORT(int)
cltSocketRev2(void *handle, unsigned char **buf, int *buflen)
{
	int			rv = 0;
	SCK_HANDLE	*sh = NULL;

	if (handle == NULL)
	{
		rv = -4;
		return rv;
	}
	if (buflen == NULL)
	{
		rv = -5;
		return rv;
	}
	sh = (SCK_HANDLE *)handle;


	//分配内存数据传出
	*buf = (char *)malloc(sh->buflen);
	if (*buf == NULL)
	{
		rv = -6;
		return rv;
	}
	memcpy(*buf, sh->buf, sh->buflen);

	*buflen = sh->buflen;

	return rv;
}

ITCAST_FUNC_EXPORT(int)
cltSocketRev2_Free(unsigned char **buf)
{
	int rv = 0;
	unsigned char * tmp = *buf;
	if (buf == NULL)
	{
		rv = -7;
		return rv;
	}
	if (tmp != NULL)
	{
		free(tmp);
	}
	*buf = NULL;
}

//客户端释放资源
ITCAST_FUNC_EXPORT(int)
cltSocketDestory2(void **handle)
{
	SCK_HANDLE *sh = NULL;
	sh = *handle; 
	if (sh != NULL)
	{
		free(sh->buf);
		free(sh);
	}
	*handle = NULL;
	return 0;
}

ITCAST_FUNC_EXPORT(int)
cltSocketSetEncFunc(void *handle, 
                    EncData encDataFunc,
                    void  *enc_ref, 
                    int enc_refLen)
{
	SCK_HANDLE *sh = NULL;

	if (handle == NULL)
	{
		return  -1;
	}

	sh = (SCK_HANDLE *)handle;

	sh->encDataFunc = encDataFunc;
	if (enc_refLen > 0)
	{
		sh->enc_refLen = enc_refLen;
		sh->enc_ref = malloc(enc_refLen);
		memcpy(sh->enc_ref, enc_ref, enc_refLen);
	}

	return 0;
}
```

### socketclientdll.h

```c
#ifndef _INC_Demo01_H
#define _INC_Demo01_H
#ifdef  __cplusplus
extern "C" {
#endif
	//定义一套协议
	//实现 动态库 加密解密业务模型抽象
	typedef int (*EncData)(unsigned char *inData,
                           int inDataLen,
                           unsigned char *outData,
                           int *outDataLen,
                           void *Ref, 
                           int RefLen);
	typedef int (*DecData)(unsigned char *inData,
                           int inDataLen,
                           unsigned char *outData,
                           int *outDataLen,
                           void *Ref, 
                           int RefLen);

	//------------第一套api接口---Begin-------------//
	//客户端初始化
	int cltSocketInit(void **handle /*out*/); 
	
	//客户端发报文
	int cltSocketSend(void *handle /*in*/, 
                      unsigned char *buf /*in*/,  
                      int buflen /*in*/);
		
	int cltSocket_EncAndSend(void *handle,  
                             EncData encDataCallBack, 
                             unsigned char *buf,  
                             int buflen);
	
	//客户端收报文
	int cltSocketRev(void *handle /*in*/, 
                     unsigned char *buf /*in*/, 
                     int *buflen /*in out*/);
	
	//客户端释放资源
	int cltSocketDestory(void *handle/*in*/);
	//-----------第一套api接口---End-------------//
	
	//-------------第二套api接口---Begin----------//
	int cltSocketInit2(void **handle); 

	//客户端发报文
	int cltSocketSend2(void *handle, 
                       unsigned char *buf,  
                       int buflen);
	//客户端收报文
	int cltSocketRev2(void *handle, 
                      unsigned char **buf, 
                      int *buflen);
	int cltSocketRev2_Free(unsigned char **buf);
	//客户端释放资源

	int cltSocketDestory2(void **handle);
	//-----------第二套api接口---End---------------//

	//实现了 把上层应用加密接口入口地址 塞入到动态库 里面
	int cltSocketSetEncFunc(void *handle, 
                            EncData encDataFunc,
                            void  *enc_ref, 
                            int enc_refLen);

#ifdef  __cplusplus
}
#endif

#endif  /* _INC_Demo01_H */
```

### test

```c
#include "stdio.h"
#include "stdlib.h"
#include "string.h"
#include "socketclientdll.h"

//typedef int (*EncData)(unsigned char *inData,int inDataLen,unsigned char *outData,int *outDataLen,void *Ref, int RefLen);

//是厂商的加密函数
int myEncData(unsigned char *inData,
              int inDataLen,
              unsigned char *outData,
              int *outDataLen,
              void *Ref, int RefLen)
{
	memcpy(outData, "12345", 10);
	*outDataLen = 10;
	return 0;
}

int main()
{
	int				rv = 0;
	void			*handle = NULL;
	unsigned char	buf[2048];
	int				buflen = 0;

	unsigned char	buf2[2048] = {0};
	int				buflen2 = 0;

	strcpy(buf, "hello world");


	buflen = 10;
	rv = cltSocketInit(&handle);
	if (rv != 0)
	{
		printf("func cltSocketInit():%d", rv);
		return rv;
	}
	
	// cvtres.exe
	///////--方法二///////////
	rv = cltSocketSetEncFunc(handle, myEncData, NULL, 0);
	if (rv != 0)
	{
		printf("func cltSocketInit():%d", rv);
		goto End;
	}
	
	rv = cltSocketSend(handle, buf,  buflen);
	if (rv != 0)
	{
		printf("func cltSocketSend():%d", rv);
		goto End;
	}
	///////--方法二///////////////////////
	
	/*方法1 
	rv = cltSocket_EncAndSend(handle, buf,  buflen, myEncData, NULL, 0);
	if (rv != 0)
	{
		printf("func cltSocketSend_enc():%d", rv);
		goto End;
	}
	*/
	
	rv = cltSocketRev(handle, buf2 , &buflen2);
	if (rv != 0)
	{
		printf("func cltSocketRev():%d", rv);
		goto End;
	}
	printf("\n%s", buf2);

End:

	rv = cltSocketDestory(handle);
	if (rv != 0)
	{
		printf("func cltSocketDestory():%d", rv);
		return rv;
	}

	printf("hello....\n");

	//system("pause");
	return 0;
}
```