#Go Web session和数据存储    

Web里面经典的解决方案是cookie和session,cookie机制是一种客户端机制,把用户数据保存在客户端,而s ession机制是一种服务器端的机制,服务器使用一种类似于散列表的结构来保存信息,每一个网站访客都会被分 配给一个唯一的标志符,即sessionID,它的存放形式无非两种:要么经过url传递,要么保存在客户端的cookies 里.当然,你也可以将Session保存到数据库里,这样会更安全,但效率方面会有所下降。
##本章解决的问题：   
1. session 和cookie的区别     
2. Go 实现session  
3. 如何防止session被劫持，保护session    
4. 实现session是存储在内存中的，实现应用session共享  


--------------            
cookie： 在本地计算机白村一些用户操作的历史信息。并在用户再次访问该 站点时浏览器通过HTTP协议将本地cookie内容发送给服务器,从而完成验证     

* 会话cookie   
	不设置过期时间， 关闭浏览器就消失  

* 持久cookie    
	设置过期时间， 浏览器保存期到硬盘上

####设置cookie   
`http.SetCookie(w ResponseWriter, cookie *Cookie)   `


~~~  
type Cookie struct {
~~~    

设置cookie    
~~~  
expiration := time.Now()
~~~

####读取cookie    

~~~  
cookie, _ := r.Cookie("username")

for _, cookie := range r.Cookies() {

~~~   
session：简而言之就是在服务器上保存用户操作的历史信息。服务器使用session id来标识session,session i d由服务器负责产生,保证随机性与唯一性,相当于一个随机密钥,避免在握手或传输中暴露用户真实密码。但该 方式下,仍然需要将发送请求的客户端与session进行对应,所以可以借助cookie机制来获取客户端的标识(即se ssion id),也可以通过GET方式将id提交给服务器。


session机制是一种服务器端的机制,服务器使用一种类似于散列表的结构(也可能就是使用散列表)来保存信息。     

程序需要为某个客户端的请求创建一个session， 服务器先检查这个客户端的请求中是否包含了一个sessionID， 如果已经包含，说明已经存在session， 服务器就按照sessionID，查找到session， 如果不包含则



##session     
go标准包没有做对session的支持     
session的基本原理是由服务器为每个会话维护一份信息数据,客户端和服务端依靠一个全局唯一的标识来访问这 份数据,以达到交互的目的     
创建session   ：  

* 生成全局唯一标识符  
* 开辟数据存储空间,也可以进行存储   
* 将session 的去哪聚唯一标识符发送给客户端   

  
HTTP协议一般讲数据存储在3个地方：   

1. 请求行  
2. 头域    
3. body   

一般有两种方式实现： cookie， URL重写   

cookie 通过set-Cookie 将session标识符传送给客户端 ，客户端每次请求带回sessionID   
URL 重写：返回给用户的页面里的所有URL后面追加session标识符(如果客户端禁用cookie， 使用此种方式最好)     

###go 实现session管理    

- 全局session管理器  
- 保证sessionID的全局唯一性  
- 为每个客户关联一个session   
- session 的存储   
- session过期处理   

定义全局session管理器   

~~~   
 type Manager struct {
在main 包中创建一个全局的session管理器  

~~~  
var globalSessions *session.Manager //然后在init函数中初始化
~~~

实现一个接口用来管理session   

~~~  
type Provider interface {
~~~   

• SessionInit函数实现Session的初始化,操作成功则返回此新的Session变量

~~~  
 type Session interface {
~~~

~~~  
var provides = make(map[string]Provider)
~~~   

~~~  
func (manager *Manager) sessionId() string {
~~~


session创建  

~~~   
 func (manager *Manager) SessionStart(w http.ResponseWriter, r *http.Request) (session Session) {
~~~   

session使用：  

~~~  
func login(w http.ResponseWriter, r *http.Request) {
          t.Execute(w, sess.Get("username"))

~~~    

设置、读取、删除     

~~~    
 func count(w http.ResponseWriter, r *http.Request) {

~~~

session 有过期改变，所以需要定义GC操作，当符合条件的时候引发GC     

~~~  
//Destroy sessionid
~~~    

~~~
func init() {
~~~   



###基于内存的session存储   

~~~   
package memory
第 6 章 6 session和数据存储 | 216
第 6 章 6 session和数据存储 | 217
~~~    

初始化一个session管理器   

~~~   
import (
  go globalSessions.GC()
 }
~~~    
 
















