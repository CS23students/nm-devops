# 📄 Jenkins CI/CD Pipeline Report (Calculator Project)

# Jenkins CI/CD Pipeline Implementation

## AIM

To create a complete CI/CD pipeline using Jenkins for a Java web application (Calculator), including build, test, artifact storage, and deployment to Apache Tomcat with a visual pipeline view.

---

## REQUIREMENTS

- Java JDK 17
- Apache Maven
- Apache Tomcat 9
- Jenkins
- JFrog Artifactory
- Git (optional)

---

## PROJECT STRUCTURE

calculator/
├── src/
│ ├── main/
│ │ ├── java/com/calculator/App.java
│ │ └── webapp/
│ │ └── index.jsp
│ └── test/
├── pom.xml
└── target/

---

## STEP 1: CREATE MAVEN PROJECT

- Create Maven project
- Packaging type: `war`
- Java version: 17

### pom.xml (important parts)

```xml
<packaging>war</packaging>

<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>
</dependencies>

<build>
    <finalName>calculator</finalName>
</build>
```

# STEP 2: CREATE APPLICATION

Servlet (App.java)
@WebServlet("/calculate")
public class App extends HttpServlet {
protected void doPost(HttpServletRequest request, HttpServletResponse response)
throws ServletException, IOException {
double num1 = Double.parseDouble(request.getParameter("num1"));
double num2 = Double.parseDouble(request.getParameter("num2"));
String op = request.getParameter("operation");

        double result = 0;

        switch (op) {
            case "add": result = num1 + num2; break;
            case "sub": result = num1 - num2; break;
            case "mul": result = num1 * num2; break;
            case "div": result = num2 != 0 ? num1 / num2 : 0; break;
        }

        request.setAttribute("result", result);
        request.getRequestDispatcher("index.jsp").forward(request, response);
    }

}

JSP (index.jsp)

<form action="<%= request.getContextPath() %>/calculate" method="post">

# STEP 3: BUILD WAR FILE

mvn clean package

Output:

target/calculator.war

👉 Small size (~4KB) is NORMAL for small apps

# STEP 4: DEPLOY TO TOMCAT

target/calculator.war → apache-tomcat/webapps/
Start Tomcat
startup.bat
Access
http://localhost:8080/calculator

# STEP 5: ENABLE TOMCAT MANAGER

Edit file:
conf/tomcat-users.xml
<role rolename="manager-gui"/>
<role rolename="manager-script"/>

<user username="admin" password="admin123"
      roles="manager-gui,manager-script"/>

Remove restriction

Edit:

webapps/manager/META-INF/context.xml

REMOVE:

<Valve className="org.apache.catalina.valves.RemoteCIDRValve"/>

Test
http://localhost:8080/manager/text/

Output:

OK - Listed applications

# STEP 6: JENKINS SETUP

Create Jobs
Build Job → Maven compile
Test Job → mvn test
Package Job → mvn package
Deploy Job → deploy to Tomcat
STEP 7: ARTIFACTORY SETUP
Create repositories:
libs-release-local
libs-snapshot-local

# STEP 7: ARTIFACTORY SETUP

Create repositories:
libs-release-local
libs-snapshot-local
pom.xml
<distributionManagement>
<repository>
<id>artifactory</id>
<url>http://localhost:8081/artifactory/libs-release-local</url>
</repository>

    <snapshotRepository>
        <id>artifactory</id>
        <url>http://localhost:8081/artifactory/libs-snapshot-local</url>
    </snapshotRepository>

</distributionManagement>

</distributionManagement>
settings.xml (IMPORTANT)

Location:

C:\Users\<user>\.m2\settings.xml
<servers>
<server>
<id>artifactory</id>
<username>admin</username>
<password>password</password>
</server>
</servers>

Deploy
mvn clean deploy

# STEP 8: JENKINS PIPELINE VIEW

Install plugin
Build Pipeline Plugin

Create View
Click "+"
Name: pipeline-view
Select: Build Pipeline View
Click Create

Configure
Select initial job
Save
STEP 9: EXECUTE PIPELINE

Run first job

# RESULT

WAR built successfully
Application deployed in Tomcat
Artifacts stored in Artifactory
Jenkins pipeline executed
Visual pipeline view created
CONCLUSION

A complete CI/CD pipeline was successfully implemented using:

Maven for build
Jenkins for automation
Artifactory for storage
Tomcat for deployment
NOTES
WAR size small → normal
403 error → context.xml issue
401 error → credentials issue
405 error → wrong Artifactory repo type

# OUTPUT

http://localhost:8080/calculator
