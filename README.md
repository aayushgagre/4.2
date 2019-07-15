package com.cg.bean;

public class Login {

	String username;
	String password;
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	
	@Override
	public String toString() {
		return "Login [username=" + username + ", password=" + password + "]";
	}
	public Login(String username, String password) {
		super();
		this.username = username;
		this.password = password;
	}
	
	
}










package com.cg.controller;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.cg.bean.Login;
import com.cg.service.LoginService;
import com.cg.service.LoginServiceImpl;

/**
 * Servlet implementation class LoginServlet
 */
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginServlet() {
        super();
        // TODO Auto-generated constructor stub
    }
    
    
    LoginService service=new LoginServiceImpl();

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String qStr=request.getParameter("action");
		if("index".equals(qStr))
		{
			RequestDispatcher dispatch=request.getRequestDispatcher("login.html");
			dispatch.forward(request, response);
		}
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		String qStr=request.getParameter("action");
		if("login".equals(qStr))
		{
			String user=request.getParameter("username");
			String password=request.getParameter("password");
			
			Login login=new Login(user,password);
			Login obj=service.loginUser(login);
			if(obj!=null){
			
			PrintWriter out=response.getWriter();
			out.println("Welcome");
			out.println("You are logged in with username " + user + " & password "+password);
			}
		}
	}

}

















package com.cg.dao;

import com.cg.bean.Login;

public interface LoginDao {


	public Login loginUser(Login user);
}







package com.cg.dao;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;




import com.cg.bean.Login;
import com.cg.util.DBUtil;

public class LoginDaoImpl implements LoginDao{

	Connection con=DBUtil.getConnect();
	Login obj=null;
	@Override
	public Login loginUser(Login user) {
		// TODO Auto-generated method stub
		String qry="select * from UserDetails";
		try{
			Statement stmt=con.createStatement();
			ResultSet rs=stmt.executeQuery(qry);
			
			while(rs.next()){
				String uName=rs.getString(1);
				String pWd=rs.getString(2);
				if(uName.equals(user.getUsername()) && pWd.equals(user.getPassword()))
				{
					obj=user;
					break;
				}
			}
		}
		catch(Exception e)
		{
			e.printStackTrace();
		}
		
		
		
		return obj;
		
	}
	
}















package com.cg.service;

import com.cg.bean.Login;

public interface LoginService {
	
	public Login loginUser(Login user);

}











package com.cg.service;

import com.cg.bean.Login;
import com.cg.dao.LoginDao;
import com.cg.dao.LoginDaoImpl;

public class LoginServiceImpl implements LoginService{

	LoginDao dao;
	public LoginServiceImpl(){
		dao=new LoginDaoImpl();
	}
	
	
	@Override
	public Login loginUser(Login user) {
		// TODO Auto-generated method stub
		
		
		return dao.loginUser(user);
	}

}
















package com.cg.util;

import java.sql.Connection;
import java.sql.DriverManager;

public class DBUtil {
	
	static Connection con;
	static
	{
		try
		{
			Class.forName("oracle.jdbc.driver.OracleDriver");
			String user="hr";
			String pass="hr";
			String url="jdbc:oracle:thin:@localhost:1521:XE";
			con=DriverManager.getConnection(url,user,pass);
		}
		catch(Exception e)
		{
			e.printStackTrace();
		}
	}
	
	public static Connection getConnect()
	{
		return con;
	}

}














<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>

<a href="LoginServlet?action=index">Login</a>

</body>
</html>













<!DOCTYPE html>
<html>
<head>
<meta charset="ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
<form action="LoginServlet?action=login" method="post">
<table>
<tr>
	<td>Username</td>
	<td><input type="text" name="username"/>
</tr>
<tr>
	<td>password</td>
	<td><input type="password" name="password"/>
</tr>
<tr>
	<td><input type="submit" value="submit"/></td>
	<td><input type="reset" value="reset"/></td>
</tr>
</table>

</form>
</body>
</html>
