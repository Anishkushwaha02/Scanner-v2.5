# 🔍 Scanner-v2.5  

Tool for penetration testing — finds open ports, tests for vulnerabilities.  

---

## ✨ Author  
**Anish Kushwaha**  
📧 [Anish_Kushwaha@proton.me](mailto:Anish_Kushwaha@proton.me)

---

## ⚡ Features  
- Fast multithreaded scanning  
- Banner grabbing (service detection)  
- Custom port range support  
- User-friendly CLI output  

---

## 🛠️ Installation  

```bash
git clone https://github.com/your-repo/Scanner-v2.5.git
cd Scanner-v2.5
pip install -r requirements.txt

import socket
import tkinter as tk
from tkinter import scrolledtext
from datetime import datetime
import threading

try:
    import ipapi
except ImportError:
    ipapi = None  # Optional

def grab_banner(ip, port):
    try:
        s = socket.socket()
        s.settimeout(2)
        s.connect((ip, port))
        s.send(b"\r\n")
        banner = s.recv(1024).decode(errors="ignore").strip()
        s.close()
        return banner if banner else None
    except:
        return None

def scan_ports(domain, max_ports, output_text):
    start_time = datetime.now()
    output_text.delete(1.0, tk.END)

    output_text.insert(tk.END, "\nBrutal Port Scanner GUI\n", 'creator')
    output_text.insert(tk.END, "Creator: Anish Kushwaha\n", 'creator')
    output_text.insert(tk.END, "Email: Anish_Kushwaha@proton.me\n", 'creator')
    output_text.insert(tk.END, "Version: 2.5\n\n", 'creator')

    try:
        ip = socket.gethostbyname(domain)
    except socket.gaierror:
        output_text.insert(tk.END, "Error: Invalid domain or host unreachable.\n", 'error')
        return

    try:
        country = ipapi.location(ip)['country_name'] if ipapi else "Unknown"
    except:
        country = "Unknown"

    open_ports = []
    for port in range(1, int(max_ports) + 1):
        s = socket.socket()
        s.settimeout(0.8)
        try:
            s.connect((ip, port))
            banner = grab_banner(ip, port)
            if banner:
                open_ports.append((port, banner))
                output_text.insert(tk.END, f"[OPEN] Port {port} - {banner}\n", 'open')
        except:
            pass
        finally:
            s.close()

    end_time = datetime.now()
    elapsed = (end_time - start_time).total_seconds()

    output_text.insert(tk.END, "\nScan Summary:\n", 'info')
    output_text.insert(tk.END, f"Domain: {domain}\n", 'info')
    output_text.insert(tk.END, f"IP Address: {ip}\n", 'info')
    output_text.insert(tk.END, f"Country: {country}\n", 'info')
    output_text.insert(tk.END, f"Open Ports with Banners: {len(open_ports)}\n", 'info')
    output_text.insert(tk.END, f"Time Taken: {elapsed:.2f} seconds\n", 'info')
    output_text.insert(tk.END, "Scan Complete.\n", 'success')

def start_scan_thread():
    domain = domain_entry.get().strip()
    max_ports = ports_entry.get().strip()
    if not domain or not max_ports.isdigit():
        output_text.insert(tk.END, "Please enter a valid domain and number of ports.\n", 'error')
        return
    threading.Thread(target=scan_ports, args=(domain, int(max_ports), output_text)).start()

root = tk.Tk()
root.title("Brutal Port Scanner by Anish Kushwaha")
root.configure(bg="#000000")  # Pure black background

font_cfg = ("Consolas", 9)

tk.Label(root, text="Enter Domain:", bg="#000000", fg="#ffffff", font=font_cfg).pack()
domain_entry = tk.Entry(root, font=font_cfg, bg="#121212", fg="#ffffff", width=45, insertbackground="white")
domain_entry.pack(pady=5)

tk.Label(root, text="Number of Ports to Scan (e.g., 100, 500):", bg="#000000", fg="#ffffff", font=font_cfg).pack()
ports_entry = tk.Entry(root, font=font_cfg, bg="#121212", fg="#ffffff", width=10, insertbackground="white")
ports_entry.pack(pady=5)

tk.Button(root, text="Start Scan", command=start_scan_thread, bg="#003366", fg="white", font=font_cfg).pack(pady=10)

output_text = scrolledtext.ScrolledText(root, height=25, width=90, font=font_cfg, bg="#121212", fg="#ffffff", insertbackground="white")
output_text.pack(padx=10, pady=10)

output_text.tag_config('open', foreground='lightgreen')
output_text.tag_config('error', foreground='red')
output_text.tag_config('info', foreground='cyan')
output_text.tag_config('creator', foreground='deepskyblue', font=("Consolas", 9, "bold"))
output_text.tag_config('success', foreground='lime')

root.mainloop()
