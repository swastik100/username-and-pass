# username-and-pass

Below is my Login form:

<form id="loginform" class="form-horizontal" name="myForm" method="POST" action="ValidateLoginServlet2.do" onSubmit="return validateLogin()">
    <input type="text" class="form-control" name="uname" placeholder="username">                                        
    <input id="login-password" type="password" class="form-control" name="pwd" placeholder="password">
    <input type="submit" value="Login" href="#" class="btn btn-success" />
</form>
And my Validate login servlet:

protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
//        processRequest(request, response);
        PrintWriter out = response.getWriter();

        String username = request.getParameter("uname");
        String password = request.getParameter("pwd");
        System.out.println(username);
        System.out.println(password);

        try
        {
            Connection con = OracleDBConnection.getConnection();
            PreparedStatement statement = con.prepareStatement("select firstname, password from registration where firstname =? and password=?");
            statement.setString(1, username);
            statement.setString(2, password);
            ResultSet result = statement.executeQuery();
            if(result.next()){
                response.sendRedirect("LoginSuccessful.jsp");
            }else{
                out.println("username and password are incorrect");
            }
        }catch(Exception e){
            System.out.println("DB related Error");
            e.printStackTrace();
        }   
    }
java javascript jsp servlets
shareimprove this question
edited Jun 10 '15 at 23:56

chŝdk
19.4k42247
asked Jun 10 '15 at 15:12

kittu
1,43862965
1
I really hope you're encrypting those passwords before they get stored in the database or compared against DB values... – Shotgun Ninja Jun 10 '15 at 16:16
Do they have to be encrypted? If so, then how?. Could you please give me a hint or a link about it. Thank you – kittu Jun 10 '15 at 17:25
add a comment
2 Answers
active oldest votes
up vote
1
down vote
accepted
You can use a <span> element where you show your error message that you get from your servlet request, here's the JSP page:

<form id="loginform" class="form-horizontal" name="myForm" method="POST" action="ValidateLoginServlet2.do" onSubmit="return validateLogin()">
    <input type="text" class="form-control" name="uname" placeholder="username">                                        
    <input id="login-password" type="password" class="form-control" name="pwd" placeholder="password">
    <input type="submit" value="Login" href="#" class="btn btn-success" />
    <span style="color:red;">${errMsg}</span>
</form>
And in your servlet you set an error message in your else statment like this:

if(result.next()) {
    response.sendRedirect("LoginSuccessful.jsp");
}else{
    request.setAttribute("errMsg", "username and password are incorrect");
    // The following will keep you in the login page
    RequestDispatcher rd = request.getRequestDispatcher("/login.jsp");
    rd.forward(request, response); 
}
And to prevent showing the same error the next login in your if block where the login is successful you can reset the ErrMsg like this:

request.setAttribute("errMsg", "");
shareimprove this answer
edited Jun 11 '15 at 6:58
answered Jun 10 '15 at 16:08

chŝdk
19.4k42247
The else part is getting re-directed to login page as you have mentioned, however <c:out value="${errMsg}"/> error message is not being displayed on the view page – kittu Jun 10 '15 at 17:43
1
@kittu Well , simply try ${errMsg} without <c:out> like this: <span style="color:red;">${errMsg}</span> – chŝdk Jun 10 '15 at 20:15 
add a comment
up vote
0
down vote
in your else part of you Validate login servlet put this code :

if(result.next()){
    response.sendRedirect("LoginSuccessful.jsp");
}
else{
  HttpSession session = request.getSession();
  session.setAttribute("wrong_uname_pass", "1");
  response.sendRedirect("index.jsp");
}
And put below code at very first part of your index.jsp(or where is your login form)

<%
if(session.getAttribute("wrong_uname_pass") != null){
%>
<script>

alert("wrong user name or password");
</script>

<%
session.removeAttribute("wrong_uname_pass");
}

%>
