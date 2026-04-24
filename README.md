# DNS Spoofing Detection System

Monitors DNS traffic in real time, validates query-response mappings against trusted resolvers, and fires SMS alerts the moment something looks wrong.

---

## What It Does

DNS spoofing (cache poisoning) tricks your system into resolving a legitimate domain to an attacker-controlled IP — silently redirecting your traffic. This tool catches that.

It compares your local DNS responses against known-good results and flags any mismatch. When spoofing is detected, it logs the incident and sends an immediate SMS alert via the Textbelt API.

---

## How It Works

```
Domain queried → Local DNS resolves IP → Compare against expected IP
                                                    │
                              ┌─────────────────────┴──────────────────────┐
                              │ Match                                       │ Mismatch
                              ✅ Clean                             ❌ Flagged as suspicious
                                                                            │
                                                              Log to dns_spoof_log.txt
                                                              Send SMS alert (Textbelt)
```

---

## Project Structure

```
dns_spoof_detection/
├── dns_spoof_checker.py   # Core detection script
├── dns_spoof_log.txt      # Incident log (auto-generated)
├── requirements.txt       # Dependencies
└── README.md
```

---

## Quick Start

**1. Clone**
```bash
git clone https://github.com/aditya-srinivas-ganti/dns-spoof-detection.git
cd dns-spoof-detection
```

**2. Install dependencies**
```bash
pip install -r requirements.txt
```

**3. Configure**

Open `dns_spoof_checker.py` and set your phone number:
```python
PHONE_NUMBER = "+91XXXXXXXXXX"   # Your number
TEXTBELT_API_KEY = "textbelt"    # Free tier: 1 SMS/day
```

**4. Run**
```bash
python dns_spoof_checker.py
```

---

## Output

**Clean resolution**
```
✅  google.com → 142.250.180.206  [Expected]
    No spoofing detected.
```

**Spoofing detected**
```
❌  google.com → 185.220.101.47  [UNEXPECTED]
    Logging incident...
    SMS alert sent to +91XXXXXXXXXX
```

**Log entry (`dns_spoof_log.txt`)**
```
2025-02-17 15:45:12 | ALERT | google.com resolved to unexpected IP: 185.220.101.47
```

---

## Detection Logic

The script flags a response as suspicious when:
- The domain fails to resolve entirely
- The returned IP doesn't match the expected/trusted IP for that domain
- The response deviates from what trusted resolvers (Google `8.8.8.8`, Cloudflare `1.1.1.1`) return

> **Note:** The free Textbelt key allows 1 SMS per day. For production use, swap in a paid API key or configure email alerts instead.

---

## Roadmap

- [ ] Multi-resolver cross-validation (Google, Cloudflare, OpenDNS simultaneously)
- [ ] Email alerts as fallback notification channel
- [ ] Continuous monitoring mode with configurable polling interval
- [ ] Web dashboard to visualize spoofing events over time
- [ ] PCAP export for forensic analysis

---

## Built With

`Python` · `dnspython` · `Textbelt API`

---

## Author

**Aditya Srinivas Ganti**
[GitHub](https://github.com/aditya-srinivas-ganti) · [LinkedIn](https://www.linkedin.com/in/aditya-srinivas-67a9bb243/)
