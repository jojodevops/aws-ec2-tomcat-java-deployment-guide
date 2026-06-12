# 🚀 Deploying a Java Web App on AWS EC2 with Tomcat 11

### ✨ A friendly, visual, beginner‑friendly guide for non‑technical users

**👤 Author:** Jojo Novoa

---

## 📖 1. Overview

Welcome! 👋 We are going to deploy a real Java web application to the cloud — step by step, in plain language. No prior cloud experience needed!

Here are the key ideas, explained simply:

- 📦 **What is a WAR file?** A WAR (Web Application Archive) is a single packaged file that contains your entire Java website — code, pages, and configuration — all zipped into one neat bundle ending in `.war`.
- ☁️ **What is EC2?** Amazon EC2 (Elastic Compute Cloud) is a virtual computer that lives in Amazon's data centers. Think of it as renting a powerful PC in the cloud that runs 24/7.
- 🐱 **What is Tomcat?** Apache Tomcat is a "web server for Java." It takes your WAR file and turns it into a live website that people can visit in their browser.
- ☕ **Why is Java 17 required?** Tomcat 11 is built on modern Java and needs **Java 17** (or newer) to run. Older Java versions simply won't work.
- 🎯 **What will you achieve?** By the end, your Java app will be live on the internet, reachable from any browser using a public web address. 🌍

---

## 🏗️ 2. Architecture Diagram

Here's the big picture of what we are building 👇

```
                🌍 Internet Users
                       |
                       v
              ┌──────────────────┐
              │  Public IP (EC2) │
              └──────────────────┘
                       |
                       v
        ┌────────────────────────────────┐
        │      AWS EC2 Instance          │
        │  (Amazon Linux 2023 Server)    │
        └────────────────────────────────┘
                       |
                       v
        ┌────────────────────────────────┐
        │        Apache Tomcat 11        │
        │  Runs Java Web Applications    │
        └────────────────────────────────┘
                       |
                       v
        ┌────────────────────────────────┐
        │        webdemo.war App         │
        │   Your Java Website/Service    │
        └────────────────────────────────┘
```

---

## 📋 3. Requirements

Before we begin, make sure you have these ready ✅

| 🔑 Item | 📝 Description |
|---------|----------------|
| 🔐 `test.pem` | Your private key file to securely connect to the server |
| 📦 `webdemo.war` | The Java web app you want to deploy |
| 💻 EC2 instance | A running virtual server on AWS |
| 🔓 Port `8080` open | So the world can reach your Tomcat app |
| 🌐 Internet connection | To download and upload everything |

---

## 🛠️ 4. Step‑by‑Step Deployment Guide

Let's do this together! Follow each step in order. 🎬

---

### 1️⃣ Step 1 — Launch EC2 🖥️

We are going to create our virtual server in AWS.

| ⚙️ Setting | ✅ Value |
|-----------|----------|
| Instance type | `t2.micro` |
| Operating System | Amazon Linux 2023 |
| Security group rule 1 | **SSH** — port `22` |
| Security group rule 2 | **HTTP** — port `80` |
| Security group rule 3 | **Custom TCP** — port `8080` |

> 💡 Port `8080` is the most important one — that's the door your Tomcat app uses!

---

### 2️⃣ Step 2 — Connect via SSH 🔌

Now let's log into our new server from your computer.

```bash
ssh -i test.pem ec2-user@<PUBLIC_IP>
```

> 🔁 Replace `<PUBLIC_IP>` with your EC2 instance's **public** IP address.

---

### 3️⃣ Step 3 — Install Java 17 ☕

Tomcat needs Java to run, so let's install it first.

```bash
sudo dnf install java-17-amazon-corretto -y
java -version
```

> ✅ If `java -version` shows version 17, you're good to go!

---

### 4️⃣ Step 4 — Install Tomcat 11.0.22 🐱

Time to install the engine that runs our Java app.

```bash
cd /tmp
wget https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.22/bin/apache-tomcat-11.0.22.tar.gz
sudo mkdir -p /opt/tomcat
sudo tar -xzf apache-tomcat-11.0.22.tar.gz -C /opt/tomcat --strip-components=1
sudo chmod +x /opt/tomcat/bin/*.sh
sudo /opt/tomcat/bin/startup.sh
ps aux | grep tomcat
```

> 🔍 The last command (`ps aux | grep tomcat`) confirms Tomcat is actually running.

---

### 5️⃣ Step 5 — Upload the WAR File 📤

Let's send our app from your computer to the server.

```bash
scp -i test.pem webdemo.war ec2-user@<PUBLIC_IP>:/tmp
```

> 🚚 This securely copies `webdemo.war` into the `/tmp` folder on the server.

---

### 6️⃣ Step 6 — Deploy the WAR 🚀

Now we hand the app over to Tomcat.

```bash
sudo mv /tmp/webdemo.war /opt/tomcat/webapps/
ls /opt/tomcat/webapps/
```

> 🪄 Tomcat automatically detects the WAR and unpacks it. Magic!

---

### 7️⃣ Step 7 — Access the App 🌐

The moment of truth — open your app in any browser! 🎉

```
http://<PUBLIC_IP>:8080/webdemo
```

> 🥳 If you see your web app, congratulations — it's live!

---

## 🚑 5. Troubleshooting

Something not working? Don't panic. Here are the most common issues and fixes 👇

| ❗ Problem | 🤔 Cause | 🛠️ Fix |
|-----------|----------|---------|
| Tomcat won't start | Java missing | Install Java 17 |
| 404 Not Found | Wrong URL | Check WAR name |
| 403 Forbidden | Port blocked | Open port 8080 |
| Permission denied | Using ec2-user | Run `sudo su` |
| `javax.*` errors | Tomcat 11 uses Jakarta | Replace with `jakarta.*` |

### 📜 Check the logs anytime

```bash
sudo tail -f /opt/tomcat/logs/catalina.out
```

> 👀 The logs are your best friend — they tell you exactly what's happening.

---

## 🧠 6. Real‑World Notes (from team debugging)

These are lessons learned the hard way during real deployments 💪

- 🔑 **Use `sudo su`** to avoid permission issues.
- ☕ **Tomcat didn't install** because Java was missing — always install Java first!
- 📥 **Use `wget` + `tar` + `chmod` + `startup.sh`** in that exact order.
- 🔍 **`ps aux | grep tomcat`** confirms Tomcat is running.
- 🌐 **Use the public IP**, not the private one, for both SCP and the browser.

---

## ✅ 7. Final Checklist

Tick everything off before you celebrate! 🎯

| 📌 Step | Status |
|---------|--------|
| Java installed | ✔ |
| Tomcat installed | ✔ |
| Tomcat running | ✔ |
| WAR uploaded | ✔ |
| WAR deployed | ✔ |
| App accessible | ✔ |
| Logs clean | ✔ |
| Ports open | ✔ |

---

## 🎉 8. Congratulations!

🏆 **Your Java application is now running on AWS EC2 using Tomcat 11. Great job!** 🏆

> 🚀 You went from zero to a live cloud deployment. That's a real achievement — be proud! 🌟

---
