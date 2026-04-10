### URL Shortener — Scale Estimates                                             
                                                                                
  #### Assumptions                                                                
  | Metric | Value |                                                            
  |---|---|                                                                     
  | New URLs/day | 10M |                                                        
  | Read:Write ratio | 100:1 |                                                  
  | Redirects/day | 1B |                                                        
                                                                                
  #### Throughput                                                                 
  | Metric | Calculation | Result |                                             
  |---|---|---|                                                                 
  | Write QPS (steady) | 10,000,000 / (24 × 60 × 60) | ≈ 115 QPS |              
  | Write QPS (peak 3x) | 115 × 3 | ≈ 350 QPS |                                 
  | Read QPS (steady) | 1,000,000,000 / (24 × 60 × 60) | ≈ 11,574 QPS |         
  | Read QPS (peak 3x) | 11,574 × 3 | ≈ 34,722 QPS |                            
                                                                                
  #### Storage (5 Years)                                                          
  | Metric | Calculation | Result |                                             
  |---|---|---|                                                                 
  | Record size | 7B (code) + 200B (URL) + 36B (UUID) + 50B (meta) | ≈ 300 B |  
  | URLs/year | 10M/day × 365 | 3.65B |                                         
  | Storage/year | 3.65B × 300 B | ≈ 1.1 TB |                                   
  | Storage (5 years) | 1.1 TB × 5 | ≈ 5.5 TB |                                 
                                                                                
  #### Short Code Length (Base62)                                                 
  | Length | Unique Codes | Headroom at 3.65B/yr |                              
  |---|---|---|                                                                 
  | 6 chars | 62^6 = 56.8B | ~15 years |                                        
  | 7 chars | 62^7 = 3.52T | ~964 years |                                       
                                                                                
  → **7-character base62 code** provides ample headroom for decades of growth.  

### Why NoSQL for URL Shortener
We choose a NoSQL database for a URL shortener because the access pattern is a simple **key → value lookup (shortCode → longURL)**, which key-value stores handle very efficiently. Systems like Amazon DynamoDB use hash-based partitioning to provide near **O(1) lookups**, unlike SQL databases such as MySQL that rely on B-tree indexes with **O(log n)** complexity. NoSQL also scales horizontally, making it suitable for billions of records with high read throughput. Since we don’t need joins or complex queries, SQL features become unnecessary overhead. This makes NoSQL a better fit for performance, scalability, and simplicity.

- **Redis**
  - In memory key value store
  - Very fast for simple operations like `GET`, `SET`, and `INCR`
  - Best for cache, counters, sessions, and short lived hot data
  - In Bitly, Redis is a good fit for the global counter and caching short code lookups

- **Postgres**
  - Relational database with durable storage on disk
  - Good for tables, SQL queries, indexes, and uniqueness constraints
  - Best for source of truth data
  - In Bitly, Postgres is a good fit for the permanent mapping from short code to long URL

- **Simple mental model**
  - Redis is for speed
  - Postgres is for durable storage
  - You often use both together

