## **Step 1: Estimating Request Size**

For simplicity, let's assume:

- **Request + Response total size** = **10 KB** per request.

This includes:

- HTTP headers (~500B each for request & response)
- Request payload (~1 KB)
- Response payload (~8 KB)
- This assumes a typical API call. For media-heavy applications, sizes can be much larger.

---

## **Step 2: Calculating Throughput**

\[
\text{Throughput} = \text{Requests per second} \times \text{Request size}
\]

For **1 million requests per second**:
\[
1,000,000 \times 10 \text{ KB} = 10 \text{ GB/s}
\]

The system must handle **10 GB of data per second**.

---

## **Step 3: Determining Server Capacity**

### **Server Throughput Assumption**

Let's assume:

- **Each server can handle 50,000 requests per second** (based on benchmarks of optimized Nginx or an application server).
- **Each server has a 10 Gbps network card**, capable of **1.25 GB/s**.

### **Number of Servers Needed**

\[
\text{Total Servers} = \frac{1,000,000}{50,000} = 20 \text{ servers}
\]

Each server handles **50K RPS**, and with **20 servers**, we achieve **1M RPS**.

---

## **Step 4: Infrastructure Considerations**

1. **Network Bandwidth**

   - **Each request is 10 KB** → **1M RPS = 10 GB/s**.
   - **A single 10 Gbps NIC handles ~1.25 GB/s**.
   - **8 servers (with 10 Gbps each) are required to handle 10 GB/s**, meaning each of the 20 servers should have **dual 10 Gbps NICs** or better.
2. **CPU & RAM**

   - High-performance servers with **32-core CPUs and 128 GB RAM** are optimal.
   - More CPU cores allow handling requests in parallel.
3. **Load Balancing**

   - **Use Nginx, HAProxy, or AWS ALB** to evenly distribute load.
   - **CDNs** should be used for static assets to reduce backend load.
4. **Caching**

   - Implement **Redis, Memcached** for frequently accessed data.
   - Database queries should be optimized with **indexing & read replicas**.

---

## **Final Calculation Summary**

| Metric              | Calculation                                            | Value                |
| ------------------- | ------------------------------------------------------ | -------------------- |
| Request Size        | Fixed for easy calculation                             | **10 KB**      |
| Total Throughput    | \( 1M \times 10 KB \)                                  | **10 GB/s**    |
| Server Throughput   | 50K RPS per server                                     | **50,000 RPS** |
| Servers Required    | \( 1,000,000 \div 50,000 \)                            | **20 servers** |
| Network Requirement | 10 GB/s total → Dual**10 Gbps NICs per server** | **20 servers** |

---