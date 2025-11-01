# ⚙️ Running a Thoreum Node (Mainnet)

This guide walks you through initializing and running a **Thoreum (THR)** node on mainnet. It includes quick-start commands, peer bootstrapping, and optional mining flags.

---

## ✅ Prerequisites

- **Node client (geth fork):**  
  GitHub → https://github.com/ThoreumPoW/go-thoreum
- **Genesis file (mainnet):**  
  GitHub → https://github.com/ThoreumPoW/genesis  
  (Download `genesis.json` from the repo’s mainnet folder when provided there.)
- **Ports open on firewall:**  
  - P2P: `30357` (default in examples)  
  - HTTP RPC: `8545` (change as you like)  
  - (Optional) WebSocket RPC: `8546`  
- **Chain ID:** `357`

> Tip: If you don’t want to build from source, download the prebuilt binaries from the releases page in `go-thoreum`.

---

## 📥 Step 1 — Create a Working Directory & Place Files

```bash
mkdir thoreum && cd thoreum
# Place the 'geth' (or geth.exe on Windows) binary here
# Place the 'genesis.json' here (from https://github.com/ThoreumPoW/genesis)
```

Windows (PowerShell):
```powershell
mkdir thoreum; cd thoreum
# Copy geth.exe and genesis.json into this folder
```

---

## 🧱 Step 2 — Initialize the Data Directory with Genesis

```bash
./geth --datadir data init genesis.json
```

Windows:
```powershell
.\geth.exe --datadir data init genesis.json
```

This creates the chain database at `./data` using Thoreum’s genesis.

---

## ▶️ Step 3 — Start the Node (Full Sync + JSON-RPC)

**Linux/macOS:**
```bash
./geth --datadir ./data --networkid 357 --port 30357 --http --http.addr 0.0.0.0 --http.port 8545 --http.api "eth,net,web3,personal,miner" --http.corsdomain "*" --ws --ws.addr 0.0.0.0 --ws.port 8546 --ws.api "eth,net,web3" --syncmode "full" console
```

**Windows (PowerShell):**
```powershell
.\geth.exe `
  --datadir .\data `
  --networkid 357 `
  --port 30357 `
  --http --http.addr 0.0.0.0 --http.port 8545 `
  --http.api "eth,net,web3,personal,miner" `
  --http.corsdomain "*" `
  --ws --ws.addr 0.0.0.0 --ws.port 8546 --ws.api "eth,net,web3" `
  --syncmode "full" console
```

> Notes  
> • `--networkid 357` = Thoreum mainnet  
> • `--syncmode "full"` is recommended for mining pools/miners/exchanges.  
> • Remove `console` if you want a non-interactive background run.

---

## 🌐 Step 4 — Add Peers (Bootstrap)

In the geth **console**, add the bootnodes (paste one per line):

```javascript
admin.addPeer("enode://8b26111a77c923c20473b2e345f3a7984c9e0a6983336008b833d01d045058646bdf74faf5239a5cabccbd0ff31df39afa28ea98bbde3e60834b1f2cf7d3d499@209.74.80.15:30357")
admin.addPeer("enode://31de2d7b0c902ca2c16abf609434659ff6aa0213a405d7530cf48082cb56d6fe94696439752d4a8f6973c9ef107223dbc5edf8c9616df7525e4fa9d52f1bddf2@45.77.65.93:30357")
```

You can also pass `--bootnodes "<enode1>,<enode2>"` on the command line if you prefer.

---

## ⛏️ (Optional) Step 5 — Enable Mining

> Thoreum uses **Ethash** PoW and has **no dev fee** and **no uncle rewards** in consensus. Rewards go entirely to the miner (per emission schedule).

**Linux/macOS:**
```bash
./geth   --datadir ./data   --networkid 357   --port 30357   --http --http.addr 0.0.0.0 --http.port 8545   --http.api "eth,net,web3,personal,miner"   --syncmode "full"   --mine --miner.threads=1 --miner.etherbase 0xYourTHRAddress
```

**Windows:**
```powershell
.\geth.exe `
  --datadir .\data `
  --networkid 357 `
  --port 30357 `
  --http --http.addr 0.0.0.0 --http.port 8545 `
  --http.api "eth,net,web3,personal,miner" `
  --syncmode "full" `
  --mine --miner.threads=1 --miner.etherbase 0xYourTHRAddress
```

Replace `0xYourTHRAddress` with your **own** wallet address for rewards.

---

## 🔧 Ports & Security

- **P2P**: `--port 30357` (open TCP/UDP in firewall)
- **HTTP RPC**: `--http.port 8545` (expose only if necessary; lock down via firewall/allow-list)
- **WebSocket RPC**: `--ws.port 8546` (optional; also firewall-protect)
- Limit APIs if running public-facing nodes; never expose `personal` externally.

---

## 🧭 Useful Links

- **Source (go client):** https://github.com/ThoreumPoW/go-thoreum  
- **Genesis repo:** https://github.com/ThoreumPoW/genesis  
- **Org:** https://github.com/ThoreumPoW/  
- **Website):** https://thoreum.org     
- **Block Explorer:** https://thoreum.info  

---

## 🛠️ Troubleshooting

- **“Genesis mismatch” / init errors**  
  Ensure you used **the exact `genesis.json`** and ran `geth --datadir data init genesis.json` before starting.
- **No peers / not syncing**  
  - Verify firewall rules for P2P port `30357` are open (TCP/UDP).  
  - Add the **enodes** above via `admin.addPeer`.  
- **RPC not reachable**  
  - Check `--http.addr` (use `0.0.0.0` to bind all interfaces).  
  - Confirm firewall allows `8545` (or your custom port).  
- **Slow sync**  
  - Ensure SSD-backed storage.  
  - Increase system file descriptors and check CPU/RAM availability.

---

## 🧪 Quick Health Checks (in console)

```javascript
net.peerCount
eth.syncing          // false when fully synced
eth.blockNumber      // growing height = good
admin.peers.length
```

---

**Happy syncing!** If you run into issues, open an issue on the `go-thoreum` repo with your OS, geth version, command flags, and logs. 💚
