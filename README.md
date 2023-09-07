# Online-Book-Recommendation-System
Home Page 
/* 
* To change this template, choose Tools | Templates 
* and open the template in the editor. 
*/ 
package Dbcon; 
import java.sql.Connection; 
import java.sql.DriverManager; 
/** 
* 
* @author Java4 
*/
22 
public class DbConnection { 
 public static Connection getConnection() 
 { 
 Connection con = null; 
 try{ 
 Class.forName("com.mysql.jdbc.Driver"); 
 con = DriverManager.getConnection("jdbc:mysql://localhost:3306/rating", "root",  "root"); 
 } 
 catch(Exception e) 
 { 
 e.printStackTrace(); 
 } 
 return con; 
 } 
} 
File open  
/* 
* To change this template, choose Tools | Templates 
* and open the template in the editor. 
*/ 
package Upload; 
/** 
* 
* @author Java4 
*/ 
import java.io.IOException; 
import java.io.InputStream; 
import java.sql.Connection; 
import java.sql.DriverManager;
23 
import java.sql.PreparedStatement; 
import java.sql.SQLException; 
import java.util.logging.Level; 
import java.util.logging.Logger; 
import javax.servlet.ServletException; 
import javax.servlet.annotation.MultipartConfig; 
import javax.servlet.annotation.WebServlet; 
import javax.servlet.http.HttpServlet; 
import javax.servlet.http.HttpServletRequest; 
import javax.servlet.http.HttpServletResponse; 
import javax.servlet.http.Part; 
@WebServlet("/uploadServlet") 
@MultipartConfig(maxFileSize = 16177215) // upload file's size up to 16MB public class FileUploadDBServlet extends HttpServlet { 
 // database connection settings 
 private String dbURL = "jdbc:mysql://localhost:3306/rating";  private String dbUser = "root"; 
 private String dbPass = "root"; 
 protected void doPost(HttpServletRequest request, 
 HttpServletResponse response) throws ServletException, IOException {  // gets values of text fields 
 String Name = request.getParameter("uname"); 
 String password = request.getParameter("pass"); 
 String email = request.getParameter("email"); 
 String dob = request.getParameter("dob"); 
 String state = request.getParameter("state"); 
 String country = request.getParameter("country"); 
 String gen = request.getParameter("gen");
24 
 System.out.println(Name + password + email + dob + state + country);  InputStream inputStream = null; // input stream of the upload file 
 // obtains the upload file part in this multipart request 
 Part filePart = request.getPart("photo"); 
 if (filePart != null) { 
 // prints out some information for debugging 
 System.out.println(filePart.getName()); 
 System.out.println(filePart.getSize()); 
 System.out.println(filePart.getContentType()); 
 // obtains input stream of the upload file 
 inputStream = filePart.getInputStream(); 
 } 
 Connection conn = null; // connection to the database 
 String message = null; // message will be sent back to client 
 try { 
 // connects to the database 
 DriverManager.registerDriver(new com.mysql.jdbc.Driver());  conn = DriverManager.getConnection(dbURL, dbUser, dbPass); 
 // constructs SQL statement 
 String sql = "insert into reg (uname, pass, email, dob, gen, state, country, allows,  photo) values (?, ?, ?, ?, ?, ?, ?, ?, ?)"; 
 PreparedStatement statement = conn.prepareStatement(sql);  statement.setString(1, Name); 
 statement.setString(2, password); 
 statement.setString(3, email); 
 statement.setString(4, dob); 
 statement.setString(5, gen);
25 
 statement.setString(6, state); 
 statement.setString(7, country); 
 statement.setString(8, "No"); 
 if (inputStream != null) { 
 // fetches input stream of the upload file for the blob column  statement.setBlob(9, inputStream); 
 } 
 // sends the statement to the database server 
 int row = statement.executeUpdate(); 
 if (row > 0) { 
 System.out.println("success---->>>>>>>>>>>>"); 
 message = "Registration Success"; 
 getServletContext().getRequestDispatcher("/reg.jsp").forward(request,  response); 
  
 } 
 } catch (SQLException ex) { 
 ex.printStackTrace(); 
 String sqls = "insert into bx_users (location) values (?)"; 
 PreparedStatement statements = null; 
 try { 
 statements = conn.prepareStatement(sqls); 
 } catch (SQLException ex1) { 
 Logger.getLogger(FileUploadDBServlet.class.getName()).log(Level.SEVERE,  null, ex1); 
 } 
 try { 
 statements.setString(1, state); 
 } catch (SQLException ex1) {
26 
 Logger.getLogger(FileUploadDBServlet.class.getName()).log(Level.SEVERE,  null, ex1); 
 } 
 // forwards to the message page 
 getServletContext().getRequestDispatcher("/reg.jsp").forward(request, response);  } 
 } 
}
