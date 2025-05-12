# 🔗 Scalable URL Shortener Design (TinyURL Clone)

---

## 🔸 1. Clarify Requirements

- Should we generate a unique short key per URL request?
- Target scale? (100M+ URLs)
- Latency goals? (~10ms for shortening and redirection)

---

## 🔸 2. Key Design Goals

| Requirement          | Solution                             |
|----------------------|--------------------------------------|
| Generate short links | Encode global counter with Base62    |
| Guarantee uniqueness | Use atomic counter (Redis or DB seq) |
| Fast lookups         | Use in-memory cache like Redis       |
| Scalability          | Stateless backend + scalable ID gen  |

---

## 🔸 3. Architecture Diagram

```
                       +------------------+
                       |     Clients      |
                       +------------------+
                              |
         +--------------------▼--------------------+
         |         Load Balancer / API Gateway      |
         +--------------------┬--------------------+
                              |
            +-----------------▼------------------+
            |          URL Shortening Service     | (Stateless)
            |  - Encode Base62                    |
            |  - Decode short key                 |
            +-----------------┬------------------+
                              |
              +---------------▼--------------+
              |      Redis (Atomic INCR)     | ←-- Counter
              +---------------┬--------------+
                              |
         +--------------------▼--------------------+
         |          Persistent Storage (DB)        |
         |  - Key: Short ID                        |
         |  - Value: Original long URL             |
         |  - e.g., PostgreSQL / DynamoDB          |
         +--------------------┬--------------------+
                              |
                    +---------▼---------+
                    |  Redis Cache (opt)| ←-- Fast GET lookup
                    +-------------------+
```

---

## 🔸 4. High-Level Flow

```
Client → [POST /shorten] → Service:
  1. Fetch next ID from counter (e.g., Redis)
  2. Encode to Base62: 12345 → "dnh"
  3. Store in DB: "dnh" → "https://long.com/path"
  4. Return short URL: tiny.url/dnh

Client → [GET /dnh] → Service:
  1. Look up key "dnh" → long URL
  2. Redirect user
```

---

## 🔸 5. Why It’s Unique Without Checking

- The **counter is atomic** (`Redis INCR`, DB sequence).
- **Every ID is globally unique**.
- **Base62 encoding is deterministic** → no collision.
- ✅ No need to check for duplicates!

---

## ✅ Java Implementation (Clean & Interview-Ready)

```java
import java.util.HashMap;
import java.util.Map;

public class TinyUrlService {

    private static final String BASE_HOST = "http://tiny.url/";
    private static final String CHARSET = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";

    // Simulating Redis auto-increment counter
    private long globalId = 100000;  // start from a large number for shorter keys

    // Storage maps
    private Map<String, String> keyToUrl = new HashMap<>();
    private Map<String, String> urlToKey = new HashMap<>();

    // Shorten a long URL
    public String encode(String longUrl) {
        if (urlToKey.containsKey(longUrl)) {
            return BASE_HOST + urlToKey.get(longUrl);
        }

        long id = globalId++;
        String key = encodeBase62(id);
        keyToUrl.put(key, longUrl);
        urlToKey.put(longUrl, key);
        return BASE_HOST + key;
    }

    // Decode a short URL to original
    public String decode(String shortUrl) {
        String key = shortUrl.replace(BASE_HOST, "");
        return keyToUrl.getOrDefault(key, "URL not found");
    }

    // Base62 encoding of ID
    private String encodeBase62(long id) {
        StringBuilder sb = new StringBuilder();
        while (id > 0) {
            int remainder = (int) (id % 62);
            sb.append(CHARSET.charAt(remainder));
            id /= 62;
        }
        return sb.reverse().toString();  // Most significant digit first
    }
}
```

---

## 🔍 Example Execution

```java
TinyUrlService service = new TinyUrlService();

String short1 = service.encode("https://example.com/page1");
System.out.

println(short1);  // e.g., http://tiny.url/aZ9

String long1 = service.decode(short1);
System.out.

println(long1);   // https://example.com/page1
```

---

## 📌 Summary of Uniqueness Guarantee

| Feature                | Design Choice               |
|------------------------|-----------------------------|
| Unique key per request | Yes, via global counter     |
| Collision possibility  | None — guaranteed unique ID |
| Base62 collisions      | Impossible with unique ID   |
| Check before insert?   | ❌ Not required              |
| Speed                  | O(1), no retry loops        |

---

## 🔧 Enhancements You Can Mention in Interview

- Use **Redis INCR** or **Kafka-based ID generator** for distributed counter.
- Add **expiry timestamps** for temporary or anonymous URLs.
- Use **Snowflake** ID for globally unique, time-sharded keys.
- Store mappings in **PostgreSQL** with a **UNIQUE constraint** on short keys.
- Add **rate limiting**, **analytics**, and **dashboard support**.

---