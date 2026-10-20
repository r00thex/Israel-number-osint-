## Israel osint tools 

## 🔍 How This Tool Works

``` git clone https://github.com/r00thex/Israel-number-osint-.git

ls

cd Israel-number-osint-

python Israel_osint.py ```


### Step-by-Step — What Happens

| Step | Action | Location in Code |
|---|---|---|
| **1. Input** | User provides a phone number (menu option 1) | `do_lookup()` |
| **2. Normalize** | `+972 50-123-4567` → `0501234567` | `normalize_israeli_phone()` |
| **3. Validate** | 9–10 digits? Area code in `2, 3, 4, 5, 8, 9`? | Same function; raises `ValueError` if invalid |
| **4. Endpoint Resolve** | Obfuscated `_BLOB` → Actual URL | `_api()` |
| **5. HTTP GET** | `?phone=0501234567` (includes retry logic) | `IsraelPhoneLookup.search()` |
| **6. Response Parse** | JSON → `dict` / 404 → `None` | Same method |
| **7. Display** | Color-coded status + raw JSON | `do_lookup()` |
| **8. Buffer** | Stores in memory (for saving) | `results` list |
| **9. Save (option 2)** | Writes to a timestamped `.json` file | `save_to_sdcard()` |

---

### What the Tool **Does** (✅ Features)

| Feature | Details |
|---|---|
| ✅ **Phone Number Lookup** | Converts common Israeli (IL) formats into a canonical digit sequence |
| ✅ **Input Normalization** | Handles `+972`, `972`, `050`, dashes, parentheses, and spaces |
| ✅ **Retry Logic** |  Auto-retry and exponential backoff for 429 / 500 / 502 / 503 / 504 errors |
| ✅ **Respects `Retry-After`** | Waits for the duration specified by the server |
| ✅ **TTY-safe colors** | Colors automatically disabled when piped (e.g., `| jq .`) for clean output |
| ✅ **SD card export** | Saves to `/sdcard/IsraelOSINT/*.json` (on Android/Termux) |
| ✅ **Multi-path fallback** | Falls back to `$HOME`, `$EXTERNAL_STORAGE`, or the current working directory if `/sdcard` is unavailable |
| ✅ **Python API** | Includes `lookup()` and `IsraelPhoneLookup` — usable beyond just the CLI |
| ✅ **Obfuscated endpoint** | The URL is not exposed in `grep` or `strings` |
| ✅ **Ctrl-C safe** | Exits with code 130, without showing a traceback |
| ✅ **Timestamped saves** | Prevents overwriting previous files |

---

### Response structure

The tool displays exactly what the API returns. It may contain various fields (see for yourself):

```json
{

#1 📸 𝐈𝐧𝐬𝐭𝐚𝐠𝐫𝐚𝐦 𝐃𝐚𝐭𝐚
👤 𝐍𝐚𝐦𝐞: OR_morali18
🪪 𝐔𝐬𝐞𝐫 𝐈𝐃: 9741464977
📞 𝐏𝐡𝐨𝐧𝐞: +972504216222
🔗 𝐔𝐬𝐞𝐫𝐧𝐚𝐦𝐞: @or_morali18
📧 𝐄𝐦𝐚𝐢𝐥: or0543121@gmail.com
────────────────────────
#2 📸 𝐈𝐧𝐬𝐭𝐚𝐠𝐫𝐚𝐦 𝐃𝐚𝐭𝐚
👤 𝐍𝐚𝐦𝐞: Morali
🪪 𝐔𝐬𝐞𝐫 𝐈𝐃: 14035404872
📞 𝐏𝐡𝐨𝐧𝐞: +0504216222
🔗 𝐔𝐬𝐞𝐫𝐧𝐚𝐦𝐞: @morali781
────────────────────────
#3 📘 𝐅𝐚𝐜𝐞𝐛𝐨𝐨𝐤 𝐃𝐚𝐭𝐚
👤 𝐍𝐚𝐦𝐞: Or Morali
🪪 𝐔𝐬𝐞𝐫 𝐈𝐃: 100025888987221
📞 𝐏𝐡𝐨𝐧𝐞: +972504216222
────────────────────────
  
}
```

The tool does not **fabricate or add any data**—it simply displays and saves the information.

---

### Use cases

**Appropriate use (✅):**
- 🔐 Verifying information for your own phone number
- 🏢 Business KYC / fraud checks (subject to a documented policy)
- 📞 Verifying information for phone numbers where consent has been obtained
- 🧪 API integration testing / data pipeline prototyping

**Inappropriate use (❌):**
- 🚫 Tracking individuals without their consent
- 🚫 Doxxing / harassment
- 🚫 Mass scraping / creating spam lists
- 🚫 Use as a "spy" tool

For more details, see: [Legal & Privacy](#-legal--privacy).

---

### Performance and limitations

| Aspect | Value |  |---|---|
| Per lookup | ~0.5–3s (depending on the network) |
| Memory buffer | ~a few KB per record |
| Retry | 3 times (default), with a 0.5s base backoff |
| Timeout | 10s per request |
| Concurrent | ❌ No — sequential |
| Batch | ❌ No built-in support; implement a `for` loop yourself |
