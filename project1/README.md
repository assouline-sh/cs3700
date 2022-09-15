## Project 1: Socket Basics

###Approach and Strategy

### Project 1: Socket Basics
#### Step 1: Program Parameters
My program first begins by configuring the program parameters such that a user can specify the TCP port the server is listening on, if they want to use a TLS encrypted socket connection, and the required hostname and username.

#### Step 2: Socket Connection 
A socket connection is then opened according to the previous parameters given. If the client should use a TLS encrypted socket connection and no other port is specified, it uses the default port 27994; if the client does not need to use a TLS encrypted socket connection and no other port is specified, it uses the default port 27993.

#### Step 3: "hello" Message
Once the socket is connected, the client sends a "hello" message to the server and it receives a "start" message that details the game ID. This value is saved, as future communication must include this game ID.

#### Step 4: Guess Target Word 
##### Step 4.1 First Guess
The first guess from the client is "adieu" because this word contains the majority of vowels, and thus the program will be able to immediately eliminate words with specific vowels. This condition quickly eliminates a plethora of possible words, since every word must have at least one vowel and consequently these letters appear frequently. The client then receives the response from the server for this guess.
##### Step 4.2 Helper Functions
Two helper functions are included for this program. not_in_word_list is a compiled list of letters that cannot be in the target word (server responded with a corresponding 0). The function not_in_word determines which letters from a guess should be added to this list given a guess and the returned marks. If there is more than one instance of a letter in the word, the client will only add this letter to not_in_word_list if its other instance also gets a mark of 0 from the server.

The second function not_retry handles cases when the response from the server is not "retry"; it reduces repeated code.
##### Step 4.3 Iterate Through Words
If the client receives a "retry" response for the guess "adieu," the program will add the "adieu" letters that are not in the target word to not_in_word_list. If a "bye" response is received, the client closes the socket, prints the flag, and exits the program. If an "error" response is received, the client closes the socket and exits the program with the given error message.
After the client receives a "retry" response for the guess "adieu" and adds its letters to not_in_word_list, the client then iterates through the given file of possible words and will send a guess to the server if it encounters a word that does not have any of its letters in not_in_word_list. If a guess is sent and it receives a "retry" response, the client relays the response to the not_in_word helper function to update the not_in_word_list according to the returned marks. If a "bye" response is received, the client closes the socket, prints the flag, and exits the program. If an "error" response is received, the client closes the socket and exits the program with the given error message. 
This process is repeated for every word in the word file that does not have letters in not_in_word_list until there are no more words from the file to read.
#### Step 5: Close Socket
If the target word is not found, the socket simply closes.


### Challenges
The major challenge I faced during this project was accounting for words with two or three instances of the same letter. This is because for some words, instances of a particular letter may be given a mark of 0 (and thus should be added to the not_in_word_list) while a different instance of that same letter will be given a mark of 1 or 2 elsewhere in the word. In these cases, it was difficult to have my program recognize that these letters should not be added to the not_in_word_list, because doing so would result in the target word never being found. 
I addressed this issue in my not_in_word function. If there was more than one instance of a particular letter in the word, I would investigate the mark the other instance of that letter was given. Only if all instances of the letter in the word received a mark of 0, would it be added to the not_in_word_list.


### Testing
I tested possible use cases locally to test my program. I tested the response when given appropriate input, with or without the -s flag, if the required hostname or username were omitted, if the hostname could be a DNS name or IP address, and if given other incorrect user inputs. 
