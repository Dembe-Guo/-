html 被设计是用来显示数据的，XMl 是为了传输和存储数据的
<? xml version="1.0" encoding="utf-8"?>
<! element 元素名称 元素内容>
get 把参数显示在地址栏，安全级别低，不支持大数据
post 把参数封装在请求中 安全级别高，支持大数据

200 表示服务器成功处理了客户的请求
302 表示请求的资源临时从不同的URL响应请求
304 表示客户端缓存的版本是最新的，客户端应继续使用它
404 服务器找不到请求资源
500 服务器发生错误

Servlet 技术具有如下特点
方便 跨平台 灵活性和可扩展性 
Servlet 在xml 中的配置
<servlet>
<servlet-name></>
<servlet-class>绝对路径</>
</servlet>
<servlet-mapping>
<servlet-name></>
<url-pattern></url-pattern>
</>

PrintWriter out response.getWriter();
InputStream in = context.getResourceAsSteram("文件路径)
properties pros = new Properties();
pros.load(in);
out.println(pros.getProperty("name"))

get OutputSteram 字节流
get Writer 字符流

解决中文输出乱码
response.setContentType("text/html;charset=utf-8");

请求重定向
response.sendRedirect("");

response.setHeader("refresh","3;.....")
refresh 刷新页面

referer 防止盗链

重定向=二次请求；二次响应
转发 一次请求，一次响应

request.getRequestDispatcher().forward(request,response);
请求参数中文乱码
request.setCharacterEncoding("utf-8)

四个域的作用范围 servletContext>session>request>pagecontext

获取当前时间
String time = new SimpleDataFormat("yyyy-MM-dd hh:mm:ss").format(new Date());

JSP 
跨平台；业务代码相分离；组件重用；预编译；
JSP运行原理 JSP文件转换为servlet(.java)编译为class(.class)执行servlet实例

JSP隐式对象 out request response config session  application page pagecontext exception 

<error-page>
    <error-code>404</error-code>
    <location>/404.jsp</location>
</error-page>
<error-page>
<error-code>404</>
<loaction>路径</>
</>
JavaBean 必须具有一个公共的，无参的构造方法
提供公共的setter getter 方法

创建bean 文件
private String name ;
public String getNAme(){
    return name
}
public Void setNAme(String name){
    this.name= name;

}

El 隐式对象
pagecontext pagescope requestscope sessionscope applicationscope param paramValues header headervalues  cookie initparam 

监听器设计几个重要组成部分
事件 事件源 事件监听器 事件处理器 
事件监听器几个步骤 
将监听器绑定到事件源 注册监听器 
事件监听是会触发监听器的成员方法 及事件处理器 
事件处理器通过事件对象获得事件源 并对事件源进行处理、
<listener>
<listener-class></>
</>

 Driver 接口 connection 接口 statement 接口 PreparedStatement接口 Resultset接口 DriverManager类

 servletContext 作用 
 * 获取Web应用程序的初始化参数
 * 实现多个Servlet对象共享数据
 * 读取web应用下的资源文件

 