# Web Proxy Autograder

## What this script does
Grades a student HTTP proxy for:
```
1) correct forwarding,
2) blocklist enforcement,
3) caching behavior (serve-from-cache + TTL refresh), and
4) extra credit link prefetch.
```
Prereqs
```
Linux with iptables (root required)
make, a working ./proxy target (built by make)
Python 3.8+ with psutil (sudo apt install install python3-psutil)
Internet access to http://netsys.cs.colorado.edu/
```
## How to run
```
sudo python3 grade.py \
  --port 8000 \
  --timeout 20 \
  --cache-dir cache \
  --artifacts-dir artifacts
```
Arguments
```
--port: proxy listen port (default 8000)
--timeout: cache TTL passed to ./proxy (default 20 seconds)
--cache-dir: proxy cache directory (default ./cache)
--artifacts-dir: where all outputs go (default ./artifacts)
--blocklist: filename read by the proxy (default blocklist in CWD)
```
Important: Script must run as root (uses iptables to block the origin during some tests).

All artifacts are saved under --artifacts-dir (default: ./artifacts/).  
```
proxy.log – stdout/stderr of the proxy while grading
ref_index.html – direct fetch of the reference page
proxy_index.html – first fetch via proxy
proxy_index_cached.html – fetch via proxy while origin blocked (Test 3a)
proxy_index_after_ttl.html – fetch via proxy after TTL (Test 3b)
ref_text1.txt, ref_apple_ex.png – reference files for prefetch (Test 4)
proxy_text1_blocked.txt, proxy_apple_ex_blocked.png – proxy responses while origin blocked (Test 4)
```

## Step-by-step flow
