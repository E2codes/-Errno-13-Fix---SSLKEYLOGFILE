# -Errno-13 - SSLKEYLOGFILE

I was running into an issue with pip install, the output giving me:
**PermissionError: [Errno 13] Permission denied: `'C:\\Users\\`**


![13](https://github.com/user-attachments/assets/207e4daf-d501-4788-be9e-f5413e48aa6b)


Initally, I focused on the fact that it was a permissions error and therefore tried the following:  

-Ran pycharm as administrator  
-Checked which python and pip where being used, (incase my path was incorrect or I was on a global interpreter.)


![get](https://github.com/user-attachments/assets/62eab7bb-a412-4c7a-9fc4-05cef92882bc)

All good here.


While I was working on this project there was a pop up alert stating that the antivirus might be interfearing with some features.
So with that in mind, and the consideration of maybe a previous install or file was corrupted???? Went ahead and ran:

```bash
  pip cache purge

```
![cache](https://github.com/user-attachments/assets/349be981-0a17-4de9-a22b-751556a2b717)


And tried to install again, with no success.  


(At this point I stood up and got a DR.Pepper)

Upon returning I widened my pigeonholed view of the issue and looked into the context.keylog_filename = sslkeylogfile.


From what I was gathering it started to look like this was a enviroment variable issue. And what I hypothesis is that this happened when I was working with Wireshark. Either I accidently, or an extension I was using incorrectly, set the SSLKEYLOGFILE path to a directory instead of a file path. WHICH IS BAD. Since this should be pointing at a file. 


## All my other programs inherited this and everything broke.

When I was trying to run pip, it would search for the "file" SSLKEYLOGFILE was supposed to be pointed to and ended up at a directory. Giving me the Permission Error.

Anyway, since I am not actively debugging TLS traffic I decided to unset it. 

```bash
 Remove-Item Env:\SSLKEYLOGFILE
```
![remove](https://github.com/user-attachments/assets/60eef612-abbb-4a19-9485-dd165863dac5)


AND GOOD HEAVENS IT WORKED. 


## TLS key logging matters
TLS key logging is used to decrypt HTTPS traffic in tools like Wireshark for debugging or analysis.


## Pointing at a directory is a no no
When we attempt to open the path as a file for writing we receive a permission denied or access error since directories can't be opened like files. 


## Your enviromental variables will propagate
When we have an enviromental variable set in Windows user it can affect all child processes, hence why pip/python was impacted in this case.

