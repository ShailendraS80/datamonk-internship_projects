Reflect on This
1. File vs. Directory Permissions
For a file, the x permission means that the file can be executed as a program. For a 
directory, x means you are allowed to enter or access it. So even if I have read 
permission on a directory, I still need x permission to enter it and access the files 
inside.

2. The 777 Risk
Giving a file 777 permissions means everyone can read, write, and execute it. For 
example, if a web server has a script with 777 permissions, an attacker who finds a 
way to access that file could modify it and add malicious code. That code could 
then be used to take control of the website or access sensitive information.

3. Symbolic vs. Octal chmod
Using chmod g+x a_file.txt is safer because it only adds execute permission for the 
group and leaves all the other permissions unchanged. With octal permissions, I would 
have to calculate the complete permission number, which makes it easier to 
accidentally change something I didn't want to.

4. sudo's Power
The command sudo rm -rf / temp_files/ is extremely dangerous. Because of the space 
after /, rm treats / as a target and can try to delete files throughout the entire 
system. sudo gives the command administrator privileges, so it can remove important 
system files that a normal user would not be allowed to delete. This could make the 
entire Linux system unusable.

5. Ownership for Collaboration
Using sudo chown -R :developers project/ is more secure because access is given 
specifically to members of the developers group. It is also easier to manage because 
new developers can be added to the group, and people who leave the team can be removed 
from it. Giving others read and write permissions would allow other users on the 
system to access or modify the project, which creates a greater security risk.
