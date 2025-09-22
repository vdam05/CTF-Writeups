# OverTheWire Bandit
Connect to overthewire using ssh <server-name> -p <port-number> where <server-name> comprises of <username>@<host-name>.

Try to find the password then connect to the next server.

To log out, use exit.

# Levels

Level0: bandit0

Level0-1: cat the readme file

Level1-2: Can use cat << - for redirection or cat -- - where '--' signals that no more flags

Level2-3: Can use cat 'spaces in this filename' with quotes for spaces.

Level3-4: Can cd into the inhere directory and ls -a to find hidden files.

Level4-5: Can use file -- * to get the type of file for all files. Find the one that is ASCII text.

Level5-6: Use find with specific flags like -type, -executable, -size to find the file and then cat it

Level6-7: Again, use find with specific flags like -user (for owner) and -group (for group owner) but first, go to the root directory using ../

Level7-8: Use grep to find the word in the file.

Level8-9: Use sort and uniq -u to find unique line. 

Level9-10: Filters out human-readable strings with string and then grep for the string starting with == using wildcard, like grep "==*"

Level10-11: Find human-readable strings with string then convert from base64. 

Level11-12: Use tr to translate ROT13. 