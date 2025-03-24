# Ivh-homes

Findings of each version

>>V1-Specific Findings
>Strictly letter-based queries.

>Only first 10 results are returned per request.

>Rate limit: 100 requests → cooldown of 15s.

>Expansion Strategy: Use prefix-based recursion (a → ab → abc).
>Total number of records found-271
>Total Requests for V1-1139
--------------------------------------------------
>>V2-Specific Findings
>Supports numbers (e.g., "1a2b3c").

>12 results per request.

>Rate limit: 50 requests → cooldown of 40s.

>Expansion Strategy: Start with letters and numbers (a-z, 0-9).
>Total number of records found-509
>Total Requests for V2-2170
----------------------------------------------------
 V3-Specific Findings
>Supports symbols in the middle (e.g., "a-b", "c.d", "x + y").

>15 results per request.

>Rate limit: 80 requests → cooldown of 45s.

>Expansion Strategy: Extract prefixes including symbols.
>Total number of records found-599
>>Total Requests for V3-2612
-------------------------------------------------------
Systematic Approach to Discovering API Behavior

 Step 1: Understanding the API Endpoints
Before making complex requests, we tested the API to check:
 What kind of responses it returns
 What inputs it accepts
Whether it has different versions

 Findings:

The API has three versions: V1, V2, and V3.

Each version has different features, response limits, and rate restrictions.

 Step 2: Identifying Features & Constraints
After testing multiple requests, we discovered the following key details:

Version	Results per Query	Supports Numbers	Supports Symbols (+, -, ., " ")	Max Requests Before Cooldown,	Cooldown Time
V1	10 results	per request,	max 100 requests and cooldown of	15 sec
V2	12 resultsper request,	max 50 requests and cooldown of	40 sec
V3	15 results	 per request,max 	80 requests and cooldown of	45 sec
 Key Differences:

V1: Only supports letters (a-z) and gives 10 results per request.

V2: Supports letters + numbers (0-9) and returns 12 results per request.

V3: Supports letters, numbers, and symbols (+, -, ., " "), returning 15 results per request.

 Step 3: Developing a Query Strategy
Since each API request only gives limited results, we needed a smart way to extract all possible names.

Approach Used
 Start with basic queries ("a", "b", "c", ..., "z").
 Extract all returned names.
 Generate new prefixes from these names and search again.
Continue this process until no new names are found.

Example:
 Query "a" → API returns: ["apple", "ant", "abc"]
 Query "ap", "an", "ab" → Get more names.
 Keep repeating until no more new names appear.

 Step 4: Handling API Rate Limits (429 Errors)
Problem:
Each version has a request limit before it blocks further queries.

Solution:
 Keep track of number of requests made.
Pause the program when the limit is reached.
 Resume after waiting for the cooldown period.

 Code Example:


if request_count % batch_size == 0:  
    print(f" Reached {batch_size} requests, waiting {cooldown_time} seconds...")  
    time.sleep(cooldown_time)  # Wait before sending more requests  
This prevents API blocks while maintaining efficient data collection.

 Step 5: Handling Different Character Sets
Since each API version allows different characters, we adjusted our approach accordingly:

V1: Search using only letters (a-z).

V2: Include numbers (0-9) in queries.

V3: Search with letters, numbers, and symbols, but symbols only appear inside words (not at the start).

 Example for V3:
 "hello-123" is valid
 "-hello123" is not a valid name

 Step 6: Final Optimization & Data Extraction
 We successfully collected all possible names from the API.
 Ensured we never hit the rate limit by using cooldowns.
 Used efficient query expansion to get maximum coverage.


---------------------------------------------------------
 Documentation of Problem-Solving Process
 Challenge 1: Handling Rate Limits (429 Errors)
Problem
The API enforced rate limits after exceeding the allowed request count per batch.

V1 allowed 100 requests, V2 only 50, and V3 only 80 before requiring a cooldown.

Solution
 Implemented automatic cooldown handling

--------------------------------------------------------------------------
if request_count % batch_size == 0:  
    print(f" Reached {batch_size} requests, waiting {cooldown_time} seconds...")  
    time.sleep(cooldown_time) 
    # Cooldown after batch 
  -----------------------------------------------------------------------
 >Effect: Prevented hitting the API limit while maintaining efficiency.
------------------------------------------------------------------
 Challenge 2: Expanding Queries to Extract All Names
Problem
API only returns 10-15 results per query

If we only query basic letters (A-Z, 0-9), we would miss many names.

Solution
> Dynamically generated new prefixes from received names
>Used a queue to store & process unexplored prefixes
>used different rules per version:

V1: Only letters

V2: Letters + numbers

V3: Letters + numbers + symbols
------------------------------------------------------
 Challenge 3: Handling Different Character Sets
Problem
V1 rejected numbers & symbols.

V2 supported numbers but not symbols.

V3 supported both numbers & symbols, but no words started with symbols.

Solution
 Designed separate query expansion strategies per API version
 Filtered results based on character compatibility

 Final Results
After implementing all optimizations, we successfully:
 Extracted all possible names for each API version
 Avoided rate limiting issues with proper batching & cooldowns
 Optimized query expansion for maximum coverage
---------------------------------------------------
>>Tool used for testing Google Colab.
---------------------------------------------------
