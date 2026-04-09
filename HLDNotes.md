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
    
