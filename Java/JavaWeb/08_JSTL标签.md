## JSTL

1. 概念：JavaServer Pages Tag Library  JSP标准标签库
	
* 是由Apache组织提供的开源的免费的jsp标签		<标签>
	
2. 作用：用于简化和替换jsp页面上的java代码		

3. 使用步骤：
	1. 导入jstl相关jar包
	2. 引入标签库：taglib指令：  `<%@ taglib %>`
	3. 使用标签

4. ### 常用的JSTL标签

  1. **if**:相当于java代码的if语句
  	
     1. 属性：
           * test 必须属性，接受boolean表达式
               * 如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容
  	          * 一般情况下，test属性值会结合el表达式一起使用
     	
     	 2. 注意：
     
     ​	 * c:if标签没有else情况，想要else情况，则可以在定义一个c:if标签
     
  2. **choose**:相当于java代码的switch语句
  	
     1. 使用choose标签声明         			相当于switch声明
       2. 使用when标签做判断         			相当于case
    3. 使用otherwise标签做其他情况的声明    	相当于default
  	
  3. **foreach**:相当于java代码的for语句

     1. 完成重复的操作
                    for(int i = 0; i < 10; i ++){

     ​        }
             * 属性：
                 begin：开始值
                 end：结束值
                 var：临时变量
                 step：步长
                 varStatus:循环状态对象
                     index:容器中元素的索引，从0开始
                     count:循环次数，从1开始
                 
                 ```jsp
                 <c:forEach begin="1" end="10" var="i" step="2" varStatus="s">
                     ${i} <h3>${s.index}<h3> <h4> ${s.count} </h4><br>
                 
                 </c:forEach>
                 ```
                 
                 
         2. 遍历容器
             List<User> list;
             for(User user : list){

             }

             * 属性：
                 items:容器对象
                 var:容器中元素的临时变量
                 varStatus:循环状态对象
                     index:容器中元素的索引，从0开始
                     count:循环次数，从1开始
                 
                 ```jsp
                 
                 <%
                     List list = new ArrayList();
                     list.add("aaa");
                     list.add("bbb");
                     list.add("ccc");
                 
                     request.setAttribute("list",list);
                 
                 
                 %>
                 
                 <c:forEach items="${list}" var="str" varStatus="s">
                 
                         ${s.index} ${s.count} ${str}<br>
                 
                 </c:forEach>
                 ```
                 
                 

5. 练习：
	
	* 需求：在request域中有一个存有User对象的List集合。需要使用jstl+el将list集合数据展示到jsp页面的表格table中