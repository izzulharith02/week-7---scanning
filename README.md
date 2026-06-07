# 📦 File Analysis – Scanning Result
**Subject:** Vulnerability Analysis  
**Code:** IKB21004 & IKB21403  
**Task:** File Analysis – Scanning Result (Week 6 & 7)

---

## Question 1 – packet1.pcap Flag

### Overview
`packet1.pcap` contains **41 ICMP packets** (ping requests and replies) between two hosts:
- `172.24.20.31`
- `171.64.20.62`

### Step 1 – Open in Wireshark
```bash
wireshark packet1.pcap
```

### Step 2 – Identify Anomalous Packet
All packets are standard ICMP Echo (ping) with **Length 98**, except **Packet 37** which has **Length 70** — indicating hidden data.

<img width="948" height="936" alt="1" src="https://github.com/user-attachments/assets/b8319448-9b57-4de8-b82c-3b3acaa016b7" />


> **Packet 37** stands out with a different length and contains a non-standard ICMP payload.

### Step 3 – Extract the Hidden Data
Clicking on **Packet 37** and expanding `Internet Control Message Protocol → Data` reveals:

```
Data: 5531564456455979 4d44497a65324670 58326c7a58324e76 623278 39
```

In the hex pane (bottom right of Wireshark), the highlighted bytes are:
```
55 31 56 44 56 45 59 79 4d 44 49 7a 65 32 46 70
58 32 6c 7a 58 32 4e 76 62 32 78 39
```

### Step 4 – Decode Base64
The raw bytes spell out ASCII text: `U1VDVEYyMDIze2FpX2lzX2Nvb2x9`  
This is a **Base64 encoded string**. Decoding it:

```bash
echo "U1VDVEYyMDIze2FpX2lzX2Nvb2x9" | base64 -d
```

<img width="752" height="272" alt="2" src="https://github.com/user-attachments/assets/20552f15-1ede-4592-9a1f-2ad5c9278b36" />


### ✅ Flag 1
```
SUCTF2023{ai_is_cool}
```

---

## Question 2 – packet2.pcap Flag

### Overview
`packet2.pcap` contains **794 packets** with mixed traffic:
- **668 ICMP packets** (normal ping traffic)
- **126 TCP packets** (including an FTP session and chat messages)

### Step 1 – Open in Wireshark
```bash
wireshark packet2.pcap
```

### Step 2 – Filter for FTP Traffic
In the Wireshark filter bar, type:
```
ftp
```

<img width="957" height="937" alt="3" src="https://github.com/user-attachments/assets/4566a9d2-b750-4ff9-a3b0-bd657fa1566f" />


The FTP session reveals:
| Command | Details |
|---------|---------|
| `USER anonymous` | Anonymous FTP login |
| `PASS` | Empty password |
| `230 Login OK` | Login successful |
| `RETR global_thermonuclear_war.gamerules.txt` | File downloaded |
| `227 Entering Passive Mode (171,64,20,62,147,65)` | PASV mode |

### Step 3 – Filter for FTP Data
In the filter bar, type:
```
ftp-data
```

One packet appears — **Packet 216** — containing the transferred file.

<img width="953" height="933" alt="4" src="https://github.com/user-attachments/assets/33f6ff29-e09e-4a55-a635-0d0c1063f2e4" />


### Step 4 – Follow TCP Stream
Right-click on **Packet 216** → **Follow** → **TCP Stream**

<img width="952" height="937" alt="5" src="https://github.com/user-attachments/assets/f8ae1cdc-bad9-4ef3-8f9b-8d9a369be8d0" />


The file content is revealed:
```
https://tinyurl.com/yr5zprz4
```

### Step 5 – Open the URL
Navigating to `https://tinyurl.com/yr5zprz4` redirects to a **Google Doc** titled **"Club Tux"** containing text encoded in **Wingdings font**.

<img width="847" height="775" alt="6" src="https://github.com/user-attachments/assets/2d80c3a2-82e6-4de8-b91f-9e0132271a49" />


### Step 6 – Decode Wingdings
The Wingdings symbols were decoded using **dCode Wingdings Decoder** (`dcode.fr/wingdings-font`):

<img width="423" height="532" alt="7" src="https://github.com/user-attachments/assets/1944e9fc-cc3a-4b3a-aceb-8022aae2b22c" />


### ✅ Flag 2
```
EX MACHINA AVA
```

---

## 🔍 Summary

| Question | File | Method | Flag |
|----------|------|--------|------|
| Q1 | `packet1.pcap` | ICMP steganography → Base64 decode | `SUCTF2023{ai_is_cool}` |
| Q2 | `packet2.pcap` | FTP data extraction → URL → Wingdings decode | `EX MACHINA AVA` |

---

## 🛠 Tools Used

| Tool | Purpose |
|------|---------|
| Wireshark | Packet capture analysis |
| Terminal / Bash | Base64 decoding |
| dCode.fr | Wingdings font decoder |
| tinyurl.com | URL redirect |


---

*Report prepared for IKB21403 Vulnerability Analysis — UniKL*
