# My Review on URL Shortener Design

## What I Learned

### Asking Questions First

What impressed me most was how the candidate didn’t rush into building the system. Instead, they started by asking smart questions about traffic volume, URL length, and whether links could be deleted. That made me realize how easily I could have underestimated the scale. Without knowing we’d need to handle 100 million URLs daily, I might have designed something only suitable for small sites. This showed me that asking the right questions upfront is more valuable than jumping straight to a clever solution.

### 301 vs 302 Redirects

The section on 301 and 302 made me more interested because of its complexity. Both redirects lead users to the correct destination, but each impacts system performance in distinct ways. A 301 tells the browser the redirect is permanent, so the browser caches it and future visits don't hit the server. This improves system performance but reduces my ability to monitor user activity. A 302 sends every request to the server first, allowing full logging but adding load. I learned that the decision between the two becomes clear when identifying whether the priority is conserving resources or collecting data.

### Why Base 62 Works Better

The chapter explained two methods that can be used or utilized to construct short URL links. The first method requires using a hash function to create short URLs which will result in database collisions that require additional database verification. 


The base 62 conversion method establishes a distinct numerical identifier for each URL which then transforms into a brief code composed of letters and numbers. The process requires no extra checks because it only needs mathematical operations. 

Most systems prefer this method because I now understand its widespread adoption in actual applications or in the real-world applications.

## Comparison: Hash + Collision Resolution vs. Base 62 Conversion

| Aspect | Hash + Collision Resolution | Base 62 Conversion |
|--------|----------------------------|-------------------|
| **How It Works** | Apply hash function (MD5, SHA-1, CRC32) to long URL, take first 7 characters | Generate a unique numeric ID for each URL, convert that number to base 62 using letters and numbers |
| **Collision Handling** | Requires checking database each time; if collision occurs, append string and hash again | No collisions possible because the unique ID guarantees a distinct short URL every time |
| **Database Queries** | Multiple queries per request (one check + potentially several retries) | One query per request (simple insert) |
| **Performance** | Degrades over time as database grows and collisions become more frequent | Constant and predictable O(1) performance regardless of data size |
| **Complexity** | High - requires collision detection, retry logic, and optional Bloom filter optimization | Low - pure mathematical conversion with no retry logic needed |
| **Predictability** | Unpredictable - response time varies based on number of collisions encountered | Predictable - every request takes roughly the same amount of time |
| **Real-World Usage** | Rarely used in production due to collision overhead | Widely adopted by TinyURL, Bitly, and most modern URL shorteners |

### Why Base 62 Conversion Is Better

| Reason | Explanation |
|--------|-------------|
| **No Collisions** | Each unique ID maps to exactly one short URL. No need for collision detection or resolution logic. |
| **Faster Performance** | Pure mathematical conversion means no multiple database round trips per request. |
| **Scales Linearly** | Performance remains consistent even at 365 billion records. |
| **Simpler Code** | Less complex logic means fewer bugs and easier maintenance. |
| **Industry Standard** | Proven approach used by real-world services like TinyURL and Bitly. |


### Caching Is a Must

The database cannot handle reading more than 11000 requests every second because it lacks sufficient capacity. The system design implements a caching mechanism that maintains frequently accessed links which results in reduced database demands and improved operational efficiency. The high read demand compared to write demand makes caching one of  top priorities for system design.

---

### What the Author Did Well

The author organized all the content into an interview format which began with the requirement stage followed by both quick calculations and detailed design work. The problem-solving process should start with those steps.

The actual numbers of 365 TB over 10 years and 1,160 writes per second made the presentation authentic instead of being purely theoretical. 

The two hashing approaches became easier to understand through their visual representation which showed their differences.

### Where I Got Stuck

The author should have provided additional information about three particular elements that need further explanation.

**Duplicate URL Checking**

The simple instruction to check the long URL against existing database records becomes complex when you have to search through billions of documents. The author mentioned Bloom filters for collisions but she did not explain their connection to duplicate checking processes. The missing piece created a gap in my understanding.

**Database Sharding**

The author only touched on database sharding at the end of his work. The establishment of sharding as a fundamental requirement occurs when a database contains 365 billion records. The data needs to be split into two parts. The process for checking duplicates needs to assess which parts of the data are duplicated. The unique ID generation will be impacted by the data splitting process. The questions above represent actual problems which any person developing this system would need to resolve.

**Race Conditions**

I must stay alert for any potential race condition issues. What occurs when two users attempt to create a short link for the identical long URL at the same moment? The two users will search the database and create separate links for the identical content. The system design did not solve the problem.

**Cache Reliability**

The cache section presented too basic information about its functions. What happens when the cache server experiences a failure? What happens when multiple users attempt to access a link which just expired? The system needs to handle these failures because they directly impact the ability to maintain service during critical business operations.

---

## What I Want to Learn More About

The article leaves me with three subjects which I want to study more deeply.

### Distributed ID Generation

The author mentioned Chapter 7 on this topic, so I'm guessing it's a big deal. The creation of unique identifiers that increase sequentially across multiple servers must maintain system performance which I need to explore further.

### Database Sharding

I need to master the methods which enable the distribution of extensive datasets across multiple database systems. The selection of a sharding strategy requires the determination of both appropriate sharding keys and shard query methods.

### Bloom Filters

The author indicated that Bloom filters will assist with checking for duplicate entries. The author wants to learn about Bloom filter operations which provide space savings and determine optimal usage scenarios.

### Cache Strategies

I want to advance my understanding beyond the basic caching principle. I want to study how different eviction policies function while learning about cache stampede management and the methods which ensure database synchronization with remote caches without breaking the system.

---

## Conclusion

The chapter provided me important knowledge for creating a URL shortener. I need to know the main requirements for the system the importance of initial inquiries and the advantages of base 62 over other methods. The calculations about system scaling proved useful to me.

The current blueprint represents a foundational structure but it requires detailed development work to transform into a fully operational system. My upcoming tasks will focus on the study of sharding procedures and distributed ID systems and the reliability of caching systems and traffic distribution management.

The chapter helped me gain important knowledge. The chapter provided me fundamental concepts while demonstrating the difficulties encountered during implementation.

## References
Xu, A. (2020). Design a URL shortener. In System design interview: An insider's guide (Vol. 1, pp. 107–124). Byte Code LLC.

Wikipedia contributors. (2026, March 21). Bloom filter. In Wikipedia.https://en.wikipedia.org/wiki/Bloom_filter


