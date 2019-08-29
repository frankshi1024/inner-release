# 极视角人工智能算法callback技术原理说明

[TOC]

> Powered by 极视角

## 一、技术原理
> callback信息是算法服务将分析结果通过http post方式传输给指定的url，如需对callback的结果进行二次开发，只需要开发接收程序即可。

![image](http://markdown-1024.oss-cn-shenzhen.aliyuncs.com/%E9%85%8D%E7%BD%AE%E5%B7%A5%E5%85%B7/%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/callback%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86.png)

## 二、示例程序
> web端示例工具访问地址： http://134.175.110.225/<br>
> web端callback地址：http://134.175.110.225/api/callback/handle

### C#接收Demo1

```
public void ProcessRequest()
        {
            string AppPath = "";
            AppPath = Server.MapPath("~");
            fliebll.WriteLog(AppPath, "进入调用方法!");
            //创建流的对象
            StreamReader sr = new StreamReader(Request.InputStream);
            //读取request的流：Json字符
            var stream = sr.ReadToEnd().ToString();
            fliebll.WriteLog(AppPath, "接收到Post Json文件!");
            //讲读取到的字符用字典存储
            Dictionary<string, object> str = (Dictionary<string, object>)new JavaScriptSerializer().DeserializeObject(stream);

            JObject jo = new JObject();
            if (str != null)
            {
                foreach (var item in str)
                {
                    //把字典转换成Json对象
                    jo.Add(item.Key, item.Value.ToString());

                }
            }
            fliebll.WriteLog(AppPath, "文件内容:" + jo.ToString());
            //return jo;
        }
```
### C#接收Demo2

```

public bool Start(int port) {
	string url = "http://+:" + port + "/";
	this.httpListener = new HttpListener();
	this.httpListener.Prefixes.Add(url); //要监听的url范围  

	//添加默认的监听范围
	this.httpListener.Start();
	this.httpListener.BeginGetContext(new AsyncCallback(WebRequestCallback), this.httpListener); // 异步方式监听
}


private void WebRequestCallback(IAsyncResult ar) {
    if (this.httpListener == null) {
        return;
    }
    //取得Context  
    HttpListenerContext Context = this.httpListener.EndGetContext(ar);
    this.httpListener.BeginGetContext(new AsyncCallback(WebRequestCallback), this.httpListener);
    //处理请求
    this.ProcessRequest(Context);
}

```


## 三、参考：完整产品架构
![image](http://markdown-1024.oss-cn-shenzhen.aliyuncs.com/%E9%85%8D%E7%BD%AE%E5%B7%A5%E5%85%B7/%E6%8A%80%E6%9C%AF%E5%8E%9F%E7%90%86/VAS%E5%8D%95%E8%B7%AF%E6%8A%80%E6%9C%AF%E6%9E%B6%E6%9E%84%EF%BC%88%E5%85%AC%E5%BC%80%EF%BC%89.png)
