## 抽取JDBC工具类 ： JDBCUtils

* 目的：简化书写
* 分析：
	1. 注册驱动也抽取
	* 抽取一个方法获取连接对象
	  * 需求：不想传递参数（麻烦），还得保证工具类的通用性。
	  * 解决：配置文件
	  	jdbc.properties
	  		url=
	  		user=
	  		password=
	* 抽取一个方法释放资源
* * 代码实现：

```java
public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;
    /**
     * 文件的读取，只需要读取一次即可拿到这些值。使用静态代码块
     */
    static{
        //读取资源文件，获取值。

        try {
            //1. 创建Properties集合类。
            Properties pro = new Properties();

            //获取src路径下的文件的方式--->ClassLoader 类加载器
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res  = classLoader.getResource("jdbc.properties");
            String path = res.getPath();
            System.out.println(path);///D:/IdeaProjects/itcast/out/production/day04_jdbc/jdbc.properties
            //2. 加载文件
           // pro.load(new FileReader("D:\\IdeaProjects\\itcast\\day04_jdbc\\src\\jdbc.properties"));
            pro.load(new FileReader(path));

            //3. 获取数据，赋值
            url = pro.getProperty("url");
            user = pro.getProperty("user");
            password = pro.getProperty("password");
            driver = pro.getProperty("driver");
            //4. 注册驱动
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    
     /**
     * 获取连接
     * @return 连接对象
     */
    public static Connection getConnection() throws SQLException {

        return DriverManager.getConnection(url, user, password);
    }

    /**
     * 释放资源
     * @param stmt
     * @param conn
     */
    public static void close(Statement stmt,Connection conn){
        if( stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
     /**
     * 释放资源
     * @param stmt
     * @param conn
     */
    public static void close(ResultSet rs,Statement stmt, Connection conn){
        if( rs != null){
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( stmt != null){
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if( conn != null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

}
```

### 案例：

- 需求：

  1. 通过键盘录入用户名和密码
  2. 判断用户是否登录成功
    * select * from user where username = "" and password = "";
    * 如果这个sql有查询结果，则成功，反之，则失败

  * 步骤：
  		1. 创建数据库表 user
  	
  	

```sql
CREATE TABLE USER(
				id INT PRIMARY KEY AUTO_INCREMENT,
				username VARCHAR(32),
				PASSWORD VARCHAR(32)
);

INSERT INTO USER VALUES(NULL,'zhangsan','123');
INSERT INTO USER VALUES(NULL,'lisi','234');         
```



代码实现：

```java
public class JDBCDemo9 {

    public static void main(String[] args) {
        //1.键盘录入，接受用户名和密码
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入用户名：");
        String username = sc.nextLine();
        System.out.println("请输入密码：");
        String password = sc.nextLine();
        //2.调用方法
        boolean flag = new JDBCDemo9().login(username, password);
        //3.判断结果，输出不同语句
        if(flag){
            //登录成功
            System.out.println("登录成功！");
        }else{
            System.out.println("用户名或密码错误！");
        }
    }
     /**
         * 登录方法
         */
        public boolean login(String username ,String password){
            if(username == null || password == null){
                return false;
            }
            //连接数据库判断是否登录成功
            Connection conn = null;
            Statement stmt =  null;
            ResultSet rs = null;
            //1.获取连接
            try {
                conn =  JDBCUtils.getConnection();
                //2.定义sql
                String sql = "select * from user where username = '"+username+"' and password = '"+password+"' ";
                //3.获取执行sql的对象
                stmt = conn.createStatement();
                //4.执行查询
                rs = stmt.executeQuery(sql);
                //5.判断
               /* if(rs.next()){//如果有下一行，则返回true
                    return true;
                }else{
                    return false;
                }*/
               return rs.next();//如果有下一行，则返回true

            } catch (SQLException e) {
                e.printStackTrace();
            }finally {
                JDBCUtils.close(rs,stmt,conn);
            }
                   return false;
        }
    }
```



