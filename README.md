# RunProcessAsUser-ScreenConnect
Built as an accessory to ConnectWise ScreenConnect Remote Administration Software.  Allows commands via the web interface to NT AUTHORITY\SYSTEM Command Prompt, to be run in the Active Console Session. (Windows Guests Only)

Background/Goal: 
On the Host page of the web interface, input sent to the "Commands" Tab of any Windows Guest, is executed on the machine via the ScreenConnect service, which results in the command being executed as the SID "NT Authority\SYSTEM".  This is not a problem, however I wanted to also be able to send commands to the Guest and have them be executed within the interactive user session (Console Session) of the machine, as the user who is currently logged in to that session, whoever that might be at that point.  I found that from time to time, I needed to execute certain commands as the user to get the desired result, but was not able to without connecting into the session and taking physical control of the user's session / using the toolbox.


Description:
The console program I wrote to accomplish this is called "rpau.exe" (Run Process As User). It was written in PureBasic 5.41, and accomplishes this goal with very small footprint on the system (19kb).  I placed a copy of it in the C:\Windows directory (PATH) to be able to run it from the Command Tab by simply calling it by name:

C:\Windows\system32]rpau

Error: No Options / Parameters Given.

       [Run Process As User] v0.32.131

Syntax1: rpau.exe --hidden "[ProgramName]" "[CmdLineArgs]" "[WorkingDirectory]"
       : You can optionally include "--hidden" to visibly hide the executed
       : program. Enclose parameters in double-quotes as shown.
       : The only required parameter is [ProgramName]. However, if you want
       : to specify [WorkingDirectory] without specifying [CmdLineArgs],
       : [CmdLineArgs] must still be passed as: "". (Empty String).

Example: rpau.exe "cmd.exe" "/c ipconfig ] test.txt" "C:\ProgramData"


Syntax2: rpau.exe --username
       : Returns the Username of the account in the Console Session. No 
       : Additional Parameters Expected.

Example: rpau.exe --username

There are currently 2 "modes" it works in. The program can only be run from the SYSTEM account, and will do nothing except exit if launched as a normal user. (Security/Redundant Use Case)
- Syntax 1 is for running a process as the user in their session, with the option of suppressing the visibility of any windows with --hidden. ('--hidden' option is for running things like batch scripts, etc. without visually interrupting the user, or running the risk of stealing their window focus.)  It is basically a command-line interface to the CreateProcess() API (ProgramName, Arguements, WorkingDirectory can be specified), while impersonating the logged on user in an environment block.
- Syntax 2 returns printed information to the Command Tab, specifically the username of the account which is logged in to the console session. ScreenConnect already provides this information, but it's just there for convenience.

(See Screenshots Folder for Examples)


Limitations/ToDo:
- Currently, does not support Terminal-Services style configurations (More than one concurrent console session).  I plan to add an option to enumerate the WTS Sessions, and to select which one specifically to direct the commands to, but haven't had time to yet.
- Plan to add other options to perform other actions as the user.