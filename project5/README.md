## Project 5: Web Crawler

## Approach and Strategy
#### Step 1: Open Socket
The first step was establishing a socket connection wrapped in TLS to communicate with the site. 

#### Step 2: Log in to Fakebook
A GET request is sent to the login page of Fakebook so that we can obtain the CSRF token and session ID for future communications. We use HTMLParser to parse the HTML response from the site and obtain the CSRF Middleware token to use for login. Afterwards, a POST request is sent to the login page with the CSRF middleware token as the cookie and passed in with the given credentials. We use the response from this POST to update the CSRF token and session ID to use. Finally, a GET request to the /fakebook/ page is made to harvest the initial list of profiles to visit (the frontier).

#### Step 3: Iterate through the frontier, adding pages to crawl as we go and looking for flags
While there are still pages to crawl and we have not found all five flags, we send a GET request to the next page in the frontier to grab more profiles to crawl. The response from the GET request is analyzed to see if a secret flag is contained in the HTML. If a 302 response is received, we perform another GET request to the new location returned by the previous response. If a 403 or 404 response is received, we continue and ignore that URL. If a 503 response is received, we retry the same GET request until it is successful. If the connection is closed unexpectedly, the socket is opened again for communication. After all pages are crawled or all five flags are found, the program ends at prints out all the found flags.

### Challenges I faced
I found it difficult to figure out how to log in to Fakebook. At first I was struggling with how to pass in the username and password, and how to separate this content from the request headers. I also faced confusion about which values to use as the cookie throughout the session. I initially used the values given by the initial GET request response for the CSRF token and session ID value, but I later realized that these values come from the POST request response. My final significant challenge was that my program eventually stopped receiving responses from GET requests. After inspection, I realized that in the last successful GET request response, the value of "Connection" was set to "close," and thus I would need to re open the socket to continue successful communication.

### Testing
To test my code, I incorporated many print statements at each step so I could inspect GET and POST requests and responses, as well as my maintained list of pages already crawled, pages to crawl (frontier), and the number of flags found. By keeping track of these elements and inspecting the HTML being sent and received, I was able to test smaller functionality of my program as I progressed in coding. Also, seeing how many flags I have found provided information about the testing of my program. 
