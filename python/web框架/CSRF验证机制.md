# **CSRF（跨站请求伪造）**

- CSRF 英文全称为 Cross SIte Request Forgery
- CSRF 通常指恶意攻击者盗用用户的名义，发送恶意请求，严重泄露个人信息危害财产的安全

**解决CSRF攻击**：使用csrf_token校验

1. 客户端和浏览器向后端发送请求时，后端往往会在响应中的 cookie 设置 csrf_token 的值，可以使用请求钩子实现，在cookie中设置csrf_token

![img](https://img-blog.csdn.net/20180811180021106?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpcmVsZXNzOTEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2. flask_wtf 中为我们提供了CSRF保护，可以直接调用开启对app的保护

![img](https://img-blog.csdn.net/20180811180057168?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpcmVsZXNzOTEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

一旦开启CSRF保护，就要设置秘钥：SECRET_KEY

![img](https://img-blog.csdn.net/20180811180128227?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpcmVsZXNzOTEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## csrf验证

### 1. 表单提交方式

- .服务器通过请求钩子在cookie中设置了csrf_token,实际上是在session中存储了未加密的csrf_token，并且将生成的sessionID编号存储在cookie中
- 在表单中我们添加了csrf的隐藏字段，在浏览器再次访问服务器时：

1.获取到表单中的csrf_token(加密的)，使用secret_key进行解密，得到解密后的csrf_token

2.通过cookie中的sessionID，取到服务器内部存储的session中的csrf_token(未加密的)

3.将两者的值进行比较



### 2. ajax提交请求方式

- 在js里面，获取到cookie中的csrf_token,将其添加到ajax的请求头中

![img](https://img-blog.csdn.net/2018081118020712?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpcmVsZXNzOTEx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



服务器后端验证过程：

1.获取请求头中的csrf_token（加密的），然后使用secret_key解密，得到解密后csrf_token。

2.通过cookie中的sessionID，取到服务器内部存储的session中的csrf_token(未加密的)。

3.将两者的值进行比较