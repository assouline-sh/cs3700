## Project 3: BGP Router

### Approach and Strategy
#### Step 1: Basic support for update and data messages
I followed the approach recommended by the project description. First, after opening sockets to the router's neighbors, I implemented support for receiving "update" and "data" messages. For update messages, I first saved a copy of the announcement (later used for disaggregation) to a list, sent a copy of the message to neighboring routers, and then appended the necessary information from the packet to the forwarding table. For data messages, I first call a helper method find_route that calculates which network in the forwarding table has the longest matching prefix to the destination of the data message. If there is no match, send a "no route" message back, if there is one matching route, send the packet to there, and if there are multiple matching routes with the same longest matching prefix, proceed to the five steps for selecting a path. 

#### Step 2: Five rules for selecting a path when multiple options are available
First, check which of the multiple given routes has the highest local preference. If one route has the highest, forward the data packet to there, or else move on to self origin. If one route has selfOrigin as True, forward the data packet to there, or else move on to AS path. If one route has the shortest AS path, forward the data packet to there, or else move on to preferred origin. If one route has the preferred origin, forward the data packet to there, or else move on to the lowest ip address and send the packet to the route with the lowest ip address.

#### Step 3: Support for dump messages and table response
If the router receives a "dump" message, send a "table" message which includes a copy of the forwarding table

#### Step 4: Support for withdraw message
For withdraw messages, I removed the dead entry from the forwarding table if the exact entry is present, then sent a copy of the message to neighboring routers by calling the same helper function send_to_neighbors as my function that handles update messages does.

#### Step 5: Route aggreggation and disaggregation
I expanded upon my update message function to include that an update message should only be appended to the forwarding table if it cannot be aggregated with an entry that already exits. I check this with an aggregate method that returns True or False whether the packet can be aggregated. To determine this, for each route in the forwarding table, the networks with a new netmask are calculated to check if they can be encompassed with a single network and netmask. This function also calls another function called coalesce, which iterates through the forwarding table once an aggregation to occurs, to check if it can be aggregated again. With aggregation implemented, I expanded upon my withdraw function to support disaggregation. If there is no route in the forwarding table that is an exact match, the entry is found in the maintained list of updates and consequently removed from the update list, and the forwarding table is rebuilt with this new update list. The forwarding table is coalesced to ensure it cannot be further aggregated.


### Challenges I faced
I faced many challenges with this project- both small and big. Initially I found it difficult to get started, and had to carefully map out my approach on paper before starting to code. I also faced issues in understanding how netmasks and longest prefix matching work, and I had to try plenty of examples before breaking down the steps my code would have to execute. It was also difficult to keep track of the scope of this project, as there were so many interwoven components that it was difficult to isolate and test functionality. However, I was able to clearly define steps to achieve a particular goal, and this made it easier to tackle small, manageable problems. During testing, I often faced an issue of packets being forwarded to the incorrect neighbor. I realized that my using a dictionary to store networks and their corresponding peer, when a forwarding table would have the same network but different peer, it would replace the peer entry instead of appending a new entry to the dictionary (since a dictionary's keys must be unique). This caused a lot of issues, but was resolved by using a list instead to store the whole route. 


### Design
I think that my project has good design in several areas. To begin, each function is very clearly defined with a single purpose, reducing code duplication and increasing code readability. For example, I am able to use the same helper function to forward both update and withdraw packets. Helper methods to send a given message to all valid neighbors, convert an IP address into binary, convert binary into an IP address, and obtaining an address from a network and netmask have all been called frequently within my code. I also believe that my design for disaggregation is sleek and simple. I iterate through the list of routes to withdraw and delete them from the saved list of update messages. From this new list, the forwarding table is built again. Finally, a helper method called coalesce is called to further aggregate the table, if needed.


### Testing
I tested each helper function individually in an IDE with example input to ensure that they worked as intended before implementation. I used the config files to test my code, and used extensive print statements to understand the status of data at each step of my code. 
