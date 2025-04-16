# JSP Session

This example demonstrates how to use sessions in JSP to store and retrieve user data across multiple pages. We'll create a simple application where a user enters their name, and it is displayed on another page using session management.

## Prerequisites
- Basic understanding of HTML and JSP.
- A web server like Apache Tomcat installed.
- A text editor (e.g., Notepad++, VS Code).

## Step-by-Step Guide

### Step 1: Understand JSP Sessions
- A session is a way to store information (like user data) to be used across multiple pages.
- JSP uses the `HttpSession` object to manage sessions.
- Common methods:
  - `session.setAttribute("key", value)`: Stores data in the session.
  - `session.getAttribute("key")`: Retrieves data from the session.
  - `session.invalidate()`: Ends the session.

### Step 2: Project Structure
Create the following files in your Tomcat web application directory (e.g., `webapps/sessionExample`):
```
sessionExample/
├── index.jsp
├── welcome.jsp
└── WEB-INF/
    └── web.xml
```

### Step 3: Create `index.jsp`
This page contains a form to collect the user's name and store it in a session.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Session Example - Input</title>
</head>
<body>
    <h2>Enter Your Name</h2>
    <form action="welcome.jsp" method="post">
        <label>Name:</label>
        <input type="text" name="username" required>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

**Explanation**:
- A simple HTML form collects the user's name.
- The form submits the data to `welcome.jsp` using the POST method.

### Step 4: Create `welcome.jsp`
This page retrieves the name from the form, stores it in the session, and displays it.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Session Example - Welcome</title>
</head>
<body>
    <%
        // Get the name from the form
        String username = request.getParameter("username");
        
        // Store the name in the session
        if (username != null && !username.trim().isEmpty()) {
            session.setAttribute("user", username);
        } else {
            username = "Guest"; // Default value if no name is provided
            session.setAttribute("user", username);
        }
    %>
    <h2>Welcome, <%= session.getAttribute("user") %>!</h2>
    <p>Your name is stored in the session.</p>
    <p><a href="display.jsp">Go to Display Page</a></p>
    <p><a href="logout.jsp">Logout</a></p>
</body>
</html>
```

**Explanation**:
- `request.getParameter("username")` gets the name from the form.
- `session.setAttribute("user", username)` stores the name in the session.
- `<%= session.getAttribute("user") %>` displays the stored name.
- Links to a display page and a logout page are provided.

### Step 5: Create `display.jsp`
This page demonstrates that the session data is accessible on another page.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Session Example - Display</title>
</head>
<body>
    <h2>Session Data Display</h2>
    <%
        // Retrieve the name from the session
        String username = (String) session.getAttribute("user");
        if (username == null) {
            username = "Guest";
        }
    %>
    <p>Hello, <%= username %>!</p>
    <p>This page retrieves your name from the session.</p>
    <p><a href="welcome.jsp">Back to Welcome Page</a></p>
    <p><a href="logout.jsp">Logout</a></p>
</body>
</html>
```

**Explanation**:
- `session.getAttribute("user")` retrieves the name stored in the session.
- If no name is found (e.g., session expired), it defaults to "Guest".
- The page shows the session data is accessible across pages.

### Step 6: Create `logout.jsp`
This page ends the session and redirects the user back to the input page.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>JSP Session Example - Logout</title>
</head>
<body>
    <%
        // Invalidate the session
        session.invalidate();
    %>
    <h2>Logged Out</h2>
    <p>You have been logged out.</p>
    <p><a href="index.jsp">Go back to Input Page</a></p>
</body>
</html>
```

**Explanation**:
- `session.invalidate()` destroys the session, removing all stored data.
- The user is redirected to `index.jsp`.

### Step 7: Create `web.xml`
This file configures the web application. Place it in the `WEB-INF` folder.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <display-name>Session Example</display-name>
</web-app>
```

**Explanation**:
- A basic `web.xml` file is required for the web application to work in Tomcat.
- It defines the application name.

### Step 8: Deploy and Test
1. **Start Tomcat**: Run the Tomcat server (e.g., via `startup.bat` or `startup.sh` in the Tomcat `bin` folder).
2. **Deploy the Application**:
   - Copy the `sessionExample` folder to Tomcat's `webapps` directory.
   - Alternatively, package the folder into a `.war` file and deploy it.
3. **Access the Application**:
   - Open a browser and go to `http://localhost:8080/sessionExample/index.jsp`.
4. **Test the Flow**:
   - Enter a name in `index.jsp` and submit.
   - See the welcome message in `welcome.jsp`.
   - Click the link to `display.jsp` to verify the session data.
   - Click "Logout" to end the session and return to `index.jsp`.

### Step 9: How It Works
- When you submit the name in `index.jsp`, it is sent to `welcome.jsp`.
- `welcome.jsp` stores the name in the session using `session.setAttribute`.
- `display.jsp` retrieves the name from the session, showing that the data persists across pages.
- `logout.jsp` clears the session, so the data is no longer available.

### Step 10: Tips for Beginners
- **Session Timeout**: By default, sessions expire after 30 minutes of inactivity. You can change this in `web.xml` by adding:
  ```xml
  <session-config>
      <session-timeout>15</session-timeout> <!-- 15 minutes -->
  </session-config>
  ```
- **Debugging**: If the session data isn't showing, check:
  - Is the session being created properly?
  - Are you using the correct attribute name (`user` in this case)?
  - Is the browser accepting cookies? (Sessions typically rely on cookies.)
- **Security**: Avoid storing sensitive data in sessions. Use secure methods for sensitive information.

### Step 11: Example Output
- **index.jsp**: Shows a form to enter a name.
- **welcome.jsp**: Displays "Welcome, [YourName]!" with links to display and logout.
- **display.jsp**: Shows "Hello, [YourName]!" confirming session data.
- **logout.jsp**: Shows "You have been logged out" and links back to `index.jsp`.

