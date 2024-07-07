
Copy the JAR file to the `lib` directory of your project.
    <!-- Login Servlet -->
    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.example.controller.LoginServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/login</url-pattern>
    </servlet-mapping>

    <!-- User List Servlet -->
    <servlet>
        <servlet-name>UserListServlet</servlet-name>
        <servlet-class>com.example.controller.UserListServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>UserListServlet</servlet-name>
        <url-pattern>/listUsers</url-pattern>
    </servlet-mapping>

    <!-- Add User Servlet -->
    <servlet>
        <servlet-name>AddUserServlet</servlet-name>
        <servlet-class>com.example.controller.AddUserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>AddUserServlet</servlet-name>
        <url-pattern>/addUser</url-pattern>
    </servlet-mapping>

    <!-- Edit User Servlet -->
    <servlet>
        <servlet-name>EditUserServlet</servlet-name>
        <servlet-class>com.example.controller.EditUserServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>EditUserServlet</servlet-name>
        <url-pattern>/editUser</url-pattern>
    </servlet-mapping>

    <!-- Welcome File -->
    <welcome-file-list>
        <welcome-file>login.jsp</welcome-file>
    </welcome-file-list>
</web-app>

  ```

`login.jsp`
- **Login Page:**
  - Create a new JSP file named `login.jsp` in the `WebContent` directory.
  - Add the following HTML form for user login:

  ```jsp
  <html>
  <head>
      <title>Login</title>
  </head>
  <body>
      <form action="login" method="post">
          <label for="username">Username:</label>
          <input type="text" id="username" name="username"><br>
          <label for="password">Password:</label>
          <input type="password" id="password" name="password"><br>
          <input type="submit" value="Login">
      </form>
  </body>
  </html>
  ```

#### 2. Create `home.jsp`
- **Home Page:**
  - Create a new JSP file named `home.jsp` in the `WebContent` directory.
  - Add content to welcome the user and provide options for listing, adding, and editing users.

  ```jsp
  <html>
  <head>
      <title>Home</title>
  </head>
  <body>
      <h1>Welcome, ${username}</h1>
      <a href="listUsers">List All Users</a>
      <a href="addUser.jsp">Add User</a>
      <a href="editUsers">Edit User</a>
      <a href="logout">Logout</a>
  </body>
  </html>
  ```

#### 3. Create `userlist.jsp`
- **User List Page:**
  - Create a new JSP file named `userlist.jsp` in the `WebContent` directory.
  - Add content to display the list of users.

  ```jsp
  <html>
  <head>
      <title>User List</title>
  </head>
  <body>
      <h1>List of Users</h1>
      <table border="1">
          <tr>
              <th>ID</th>
              <th>Username</th>
              <th>Email</th>
          </tr>
          <c:forEach var="user" items="${users}">
              <tr>
                  <td>${user.id}</td>
                  <td>${user.username}</td>
                  <td>${user.email}</td>
              </tr>
          </c:forEach>
      </table>
  </body>
  </html>
  ```

#### 4. Create `adduser.jsp`
- **Add User Page:**
  - Create a new JSP file named `adduser.jsp` in the `WebContent` directory.
  - Add a form for adding a new user.

  ```jsp
  <html>
  <head>
      <title>Add User</title>
  </head>
  <body>
      <form action="addUser" method="post">
          <label for="username">Username:</label>
          <input type="text" id="username" name="username"><br>
          <label for="password">Password:</label>
          <input type="password" id="password" name="password"><br>
          <label for="email">Email:</label>
          <input type="email" id="email" name="email"><br>
          <input type="submit" value="Add User">
      </form>
  </body>
  </html>
  ```

#### 5. Create `edituser.jsp`
- **Edit User Page:**
  - Create a new JSP file named `edituser.jsp` in the `WebContent` directory.
  - Add content to edit user details.

  ```jsp
  <html>
  <head>
      <title>Edit User</title>
  </head>
  <body>
      <form action="editUser" method="post">
          <input type="hidden" name="id" value="${user.id}">
          <label for="username">Username:</label>
          <input type="text" id="username" name="username" value="${user.username}"><br>
          <label for="password">Password:</label>
          <input type="password" id="password" name="password" value="${user.password}"><br>
          <label for="email">Email:</label>
          <input type="email" id="email" name="email" value="${user.email}"><br>
          <input type="submit" value="Update User">
      </form>
  </body>
  </html>
  ```

### Step 5: Implement Servlets

#### 1. Create `LoginServlet`
- **LoginServlet.java:**
  - Create a new

 Java class named `LoginServlet` in the `com.example.controller` package.
  - Implement the servlet to handle user login.

  ```java
  package com.example.controller;

  import com.example.dao.UserDao;
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import javax.servlet.http.HttpSession;
  import java.io.IOException;

  @WebServlet("/login")
  public class LoginServlet extends HttpServlet {
      private UserDao userDao;

      @Override
      public void init() {
          userDao = new UserDao();
      }

      @Override
      protected void doPost(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
          String username = request.getParameter("username");
          String password = request.getParameter("password");

          if (userDao.validate(username, password)) {
              HttpSession session = request.getSession();
              session.setAttribute("username", username);
              response.sendRedirect("home.jsp");
          } else {
              response.sendRedirect("login.jsp");
          }
      }
  }
  ```

#### 2. Create `UserListServlet`
- **UserListServlet.java:**
  - Create a new Java class named `UserListServlet` in the `com.example.controller` package.
  - Implement the servlet to retrieve and display all users.

  ```java
  package com.example.controller;

  import com.example.dao.UserDao;
  import com.example.model.User;
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  import java.util.List;

  @WebServlet("/listUsers")
  public class UserListServlet extends HttpServlet {
      private UserDao userDao;

      @Override
      public void init() {
          userDao = new UserDao();
      }

      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
          List<User> userList = userDao.getAllUsers();
          request.setAttribute("users", userList);
          request.getRequestDispatcher("userlist.jsp").forward(request, response);
      }
  }
  ```

#### 3. Create `AddUserServlet`
- **AddUserServlet.java:**
  - Create a new Java class named `AddUserServlet` in the `com.example.controller` package.
  - Implement the servlet to handle adding a new user.

  ```java
  package com.example.controller;

  import com.example.dao.UserDao;
  import com.example.model.User;
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;

  @WebServlet("/addUser")
  public class AddUserServlet extends HttpServlet {
      private UserDao userDao;

      @Override
      public void init() {
          userDao = new UserDao();
      }

      @Override
      protected void doPost(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
          String username = request.getParameter("username");
          String password = request.getParameter("password");
          String email = request.getParameter("email");

          User newUser = new User(username, password, email);
          userDao.addUser(newUser);

          response.sendRedirect("listUsers");
      }
  }
  ```

#### 4. Create `EditUserServlet`
- **EditUserServlet.java:**
  - Create a new Java class named `EditUserServlet` in the `com.example.controller` package.
  - Implement the servlet to handle retrieving and updating user details.

  ```java
  package com.example.controller;

  import com.example.dao.UserDao;
  import com.example.model.User;
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;

  @WebServlet("/editUser")
  public class EditUserServlet extends HttpServlet {
      private UserDao userDao;

      @Override
      public void init() {
          userDao = new UserDao();
      }

      @Override
      protected void doPost(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
          int id = Integer.parseInt(request.getParameter("id"));
          String username = request.getParameter("username");
          String password = request.getParameter("password");
          String email = request.getParameter("email");

          User user = new User(id, username, password, email);
          userDao.updateUser(user);

          response.sendRedirect("listUsers");
      }
  }
  ```

### Step 6: Implement Data Access Object (DAO)

#### 1. Create `UserDao`
- **UserDao.java:**
  - Create a new Java class named `UserDao` in the `com.example.dao` package.
  - Implement methods for database operations.

  ```java
  package com.example.dao;

  import com.example.model.User;
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.ArrayList;
  import java.util.List;

  public class UserDao {
      private String jdbcURL = "jdbc:mysql://localhost:3306/user_management";
      private String jdbcUsername = "root";
      private String jdbcPassword = "password";

      private static final String INSERT_USERS_SQL = "INSERT INTO users (username, password, email) VALUES (?, ?, ?);";
      private static final String SELECT_USER_BY_ID = "SELECT id, username, password, email FROM users WHERE id = ?;";
      private static final String SELECT_ALL_USERS = "SELECT * FROM users;";
      private static final String DELETE_USERS_SQL = "DELETE FROM users WHERE id = ?;";
      private static final String UPDATE_USERS_SQL = "UPDATE users SET username = ?, password = ?, email = ? WHERE id = ?;";

      protected Connection getConnection() {
          Connection connection = null;
          try {
              Class.forName("com.mysql.cj.jdbc.Driver");
              connection = DriverManager.getConnection(jdbcURL, jdbcUsername, jdbcPassword);
          } catch (SQLException e) {
              e.printStackTrace();
          } catch (ClassNotFoundException e) {
              e.printStackTrace();
          }
          return connection;
      }

      public void addUser(User user) {
          try (Connection connection = getConnection();
               PreparedStatement preparedStatement = connection.prepareStatement(INSERT_USERS_SQL)) {
              preparedStatement.setString(1, user.getUsername());
              preparedStatement.setString(2, user.getPassword());
              preparedStatement.setString(3, user.getEmail());
              preparedStatement.executeUpdate();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }

      public List<User> getAllUsers() {
          List<User> users = new ArrayList<>();
          try (Connection connection = getConnection();
               PreparedStatement preparedStatement = connection.prepareStatement(SELECT_ALL_USERS)) {
              ResultSet rs = preparedStatement.executeQuery();
              while (rs.next()) {
                  int id = rs.getInt("id");
                  String username = rs.getString("username");
                  String password = rs.getString("password");
                  String email = rs.getString("email");
                  users.add(new User(id, username, password, email));
              }
          } catch (SQLException e) {
              e.printStackTrace();
          }
          return users;
      }

      public boolean validate(String username, String password) {
          boolean status = false;
          try (Connection connection = getConnection();
               PreparedStatement preparedStatement = connection.prepareStatement("SELECT * FROM users WHERE username = ? AND password = ?")) {
              preparedStatement.setString(1, username);
              preparedStatement.setString(2, password);
              ResultSet rs = preparedStatement.executeQuery();
              status = rs.next();
          } catch (SQLException e) {
              e.printStackTrace();
          }
          return status;
      }

      public void updateUser(User user) {
          try (Connection connection = getConnection();
               PreparedStatement preparedStatement = connection.prepareStatement(UPDATE_USERS_SQL)) {
              preparedStatement.setString(1, user.getUsername());
              preparedStatement.setString(2, user.getPassword());
              preparedStatement.setString(3, user.getEmail());
              preparedStatement.setInt(4, user.getId());
              preparedStatement.executeUpdate();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
  }
  ```

### Step 7: Configure Database Connection

1. **Database Configuration:**
   - Update the JDBC URL, username, and password in the `UserDao` class to match your MySQL configuration.

### Step 8: Deploy and Test

#### 1. Deploy to Tomcat
- **Deploy Application:**
  - In Eclipse, right-click your project and select `Run As > Run on Server`.
  - Choose your Tomcat server and click `Finish`.
- **Test the Application:**
  - Open a web browser and go to `http://localhost:8080/user-management/login.jsp`.
  - Test the login functionality and ensure you can navigate to the home page.
  - Test the list, add, and edit user functionalities.

By following these steps, you will have a complete web application using JSP, Servlets, JDBC, and MySQL. This detailed explanation should help you understand each part of the process, even if you are new to Java development.
