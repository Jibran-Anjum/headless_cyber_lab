# Personal Cyber Lab & Private AI Server

An enterprise-grade, zero-trust headless server built on a Lenovo ThinkPad L540 running Debian. This project transforms aging hardware into a secure penetration testing sandbox and local AI workstation without exposing administrative services to the public internet.

## 🛠️ Hardware & Storage Architecture
* **Host Machine:** Lenovo ThinkPad L540
* **OS:** Headless Debian (consumes <5GB storage and minimal baseline RAM).
* **Storage Strategy:** The optical CD/DVD drive was physically replaced with a secondary hard disk caddy. This isolates 110GB of SSD storage exclusively for LLM model weights, security tool logs, and project environments, mounted persistently via `/etc/fstab`.

## 🔒 Security Posture & Networking
The server implements a two-layer defense system to prevent Man-in-the-Middle (MitM) attacks and automated botnet scanning.
* **Network Cloaking (Tailscale WireGuard):** The server is not exposed to the public internet. All remote administrative traffic routes exclusively through a private Tailscale mesh network (`100.x.x.x`). 
* **Firewall (UFW):** Strict rules drop all incoming public traffic. SSH (Port 22) and Ollama API (Port 11434) traffic are only permitted if they originate from the secure `tailscale0` interface.
* **Authentication Layer:** Password authentication and Root login are strictly disabled in `sshd_config`. Access requires physical possession of an `ed25519` cryptographic keypair.
* **Intrusion Prevention:** `Fail2ban` runs silently in the background to automatically block malicious IP anomalies.

## 🧠 Local AI & Penetration Testing Stack
The system operates entirely offline using Ollama, ensuring sensitive environment data, Nmap scans, and vulnerability logs never leave the local hardware.
* **Recon & Exploit Phase:** Uses `dolphin-phi` (~3B parameters) for rapid, uncensored terminal analysis and payload formatting directly within the hardware's RAM limits.
* **Methodology & Guidance:** Uses `xploiter/pentester` to provide specialized web application penetration testing guidance (OWASP Top 10, SQLmap, Burp Suite).
* **Automated Reporting:** Utilizes a custom Modelfile built on `CognitiveComputations/dolphin-llama3.1` (8B) to ingest raw scanner outputs (like `dnsrecon` and `whatweb`) and compile them into structured executive summaries and technical exploitation vectors.

## 📁 Remote File Management
* **Protocol:** SFTP via SSH.
* **Client Integration:** The server's file system is securely mounted in the local Linux Dolphin file manager (`sftp://username@100.x.x.x`). It utilizes the pre-established SSH key for zero-friction drag-and-drop file transfers without requiring a server-side graphical interface.

## 🌐 Public Web Exposure (Zero-Cost Hosting Stack)
The architecture supports securely hosting public-facing static applications (e.g., portfolios or HTML/JS projects) without exposing the underlying administrative ports.
* **Dynamic DNS:** Maps the home network IP using DuckDNS.
* **Reverse Proxy:** Nginx serves static web files from `/var/www/html`.
* **SSL/TLS:** Automated HTTPS certificates are generated and renewed via Let's Encrypt (`certbot --nginx`).
* **Port Forwarding:** The local router selectively forwards external ports 80/443 to the internal Debian server, allowing public web traffic while UFW continues to block SSH and AI inference ports.
