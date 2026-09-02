Reflect on This
1. Package Managers (APT vs Snap)
A developer might choose Snap because it is easy to install and usually includes the 
dependencies the application needs. It can also update automatically. The trade-offs 
are that Snap applications can use more disk space and sometimes take longer to start 
compared to APT packages.

2. Search Tools (grep vs ripgrep)
For a large project, I would prefer rg because it is generally faster than grep and is 
designed for searching source code. It also automatically skips directories like .git 
and other files that usually don't need to be searched.

3. Internet Utilities (curl vs wget)
I would use curl when I need to fetch data from an API and pass it to another command, 
such as jq. I would use wget when I need to download files, especially 
large files or multiple files.
The main difference is that curl is more focused on transferring data and interacting 
with servers/APIs, while wget is mainly designed for downloading files and web content.

4. The Power of Piping (curl + jq)
I could use the GitHub API to get information about my public repositories and then 
use jq to display only their names and programming languages.
