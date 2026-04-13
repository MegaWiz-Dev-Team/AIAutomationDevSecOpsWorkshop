# OWASP WebGoat

**Learn the hack - Stop the attack**

[WebGoat](https://owasp.org/www-project-webgoat/) is a deliberately insecure application that allows interested developers to test vulnerabilities commonly found in Java-based applications that use common and popular open source components.

## Description

Web application security is difficult to learn and practice. Not many people have full blown web applications like online book stores or online banks that can be used to scan for vulnerabilities. In addition, security professionals frequently need to test tools against a platform known to be vulnerable to ensure that they perform as advertised. All of this needs to happen in a safe and legal environment.

Even if your intentions are good, we believe you should never attempt to find vulnerabilities without permission. The primary goal of the WebGoat project is simple: create a de-facto interactive teaching environment for web application security.

> **WARNING 1:** While running this program your machine will be extremely vulnerable to attack. You should disconnect from the Internet while using this program. WebGoat’s default configuration binds to localhost to minimize the exposure.

> **WARNING 2:** This program is for educational purposes only. If you attempt these techniques without authorization, you are very likely to get caught.

## Goals

### 1. Explain the vulnerability
Teaching is now a first class citizen of WebGoat. We focus on explaining from the beginning what, for example, a SQL injection is.

### 2. Learn by doing
During the explanation of a vulnerability we build assignments which will help you understand how it works.

### 3. Explain mitigation
At the end of each lesson you will receive an overview of possible mitigations which will help you during your development work.

## How to Run WebGoat

### 1. Run using Docker
If you already have a browser and ZAP and/or Burp installed, you can run the WebGoat image directly using Docker:

```bash
docker run -it -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 webgoat/webgoat
```

If you want to reuse the container, give it a name:
```bash
docker run --name webgoat -it -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 webgoat/webgoat
```

You can then stop and start it using:
```bash
docker start webgoat
```

### 2. Run using Docker with complete Linux Desktop
Instead of installing tools locally we have a complete Docker image based on running a desktop in your browser.
```bash
docker run -p 127.0.0.1:3000:3000 webgoat/webgoat-desktop
```

### 3. Standalone Application
Download the latest WebGoat release from [GitHub Releases](https://github.com/WebGoat/WebGoat/releases).
```bash
java -Dfile.encoding=UTF-8 -Dwebgoat.port=8080 -Dwebwolf.port=9090 -jar webgoat-2023.5.jar
```

---

## WebWolf

WebWolf is a separate web application which simulates an attacker's machine. It makes a clear distinction between what takes place on the attacked website and the actions you need to do as an "attacker". 

Features include:
- **Host a file:** Upload a file needed to be downloaded during an assignment.
- **E-mail client:** Simulate sending and receiving an e-mail.
- **Landing page for incoming requests:** Serve as a landing page to make a call from inside an assignment, acting like a simple form of netcat.

### Running WebWolf
- **Docker:** If you started the Docker image, WebWolf is already running at `http://localhost:9090/WebWolf`.
- **Standalone:** Start via jar file:
  ```bash
  java -jar webwolf-<<version>>.jar [--server.port=9090] [--server.address=localhost]
  ```

---

*For more information, please visit the [OWASP WebGoat official page](https://owasp.org/www-project-webgoat/).*
