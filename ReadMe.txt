Automation How-To

aaa_config.txt
	userids and passwords
	Format:
	<aaa system>;<userid>;<password>
		aaa system is freeform.  This will map to a listofdevices entry.  if you want to reference a different set of user/pswd creds; just give it a name, enter it, with this format, into aaa_config.  Then, when you add a device, just put that in for "auth system".
	
	Logic allows you to add to this list and to associate a user/pswd to multiple "site ID's"
		
goto.sh
	Base script.  Uses listofdevices to build devicemenu, which is then used to display a login menu.  Can pass up to three free-form arguements to narrow display set.
	
listofdevices
	List of devices in network.  "sh_cdp" job gather CDP information and output it in the proper format.  Base file for all other scripts.
	Format:
		<mgmt IP> <device name> <device make> <login method (ssh|telnet)> <site ID> <auth system> <device model>
	
devicemenu
	Built by goto.sh for each run.  List of devices for execution based on variable input.  
		Subset of listofdevices.
	
loginto.exp
	Called by goto.sh and auto_command_run to actually log in to devices and execute commands.  This is the script that does the actual data gathering/execution.

fix_autocommand_run_output.bash
	Removes control characters, formatting issues, and extraneous characters from output.

update_device_file.awk
	Checks for valid entries. If an entry in device_file is invalid (no longer in listofdevices) it is removed. Script will no longer attempt to contact old/decommissioned devices when running older canned scripts.  Updates device_file entries with latest value from listofdevices. (This is especially useful pre-deployment from staging when management IPs likely changed or if the login credentials have changed. I.e going from local/staging/deployment auth to production TACACS.)  Just ensure listofdevices has the latest/accurate info, and subsequent script runs will auto populate those changes.
	Called from auto_command_run
		Feeds listofdevices (source-of-truth) into an array, then does lookup against device_file.  Any differences, listofdevices wins.
		awk -f /scripts/Toolbox/update_device_file.awk "$listofdevices" "$device_file" | sort -k 2,2 | > "$jobs_directory"/updated_device_file
 		mv "$jobs_directory"/updated_device_file "$device_file"


	
auto_command_run
	This reads device_file, if it exists, otherwise it runs the first argument
	Then executes commmands in command_file, if it exists, otherwise it run the second argument
	Then it passes these to loginto.exp for execution.
	
	-m "Manual mode".  Operates much like goto.sh, except walks you through selecting devices and commands for each.
		If you select "save", it will copy the following files to /saved/shrink_wrapped/<job id string>
			cleanup.awk		Script that can be called to cleanup script output
			command_file		Commands to be run on devices for this job
			device_file		Devices to be viewed
			output			Result of auto_command_run
			mod_file		Free form bash script to pipe output from auto_command_run in various ways
			munge.awk		Free form awk script to further format output
			
	device_file format:
		Can now use a list of just manageable IPs or names in device_file. Scripts will auto update them with the relevant entries from listofdevices. 
	
	command_file format:
		If you just enter a list of commands, it will run the entire list against every device in device_file.
		Device specific commands
			Break up the command_file into sections headed by a line from device_file, it will only run the commands in that section on that particular device
			
run_canned.sh
	Runs saved jobs
		Format: run_canned.sh <job> <data state> <report_type> <selector> 
		No args: creates a menu of saved jobs.
			1st arguement: job (directory of job in $HOME/saved_jobs/shrink_wrapped)
			2nd arguement: data_state (new or old)
			3rd arguement: report_type.  Pass "report", for normal output; or "excel", for csv; via "print_type" below.
			4th arguement: "selector".  Free form expression to select a particular output group. Selects on switchname and/or command.  Regex allowed.
				This is looked for in the "print it" decision at the top of munge.awk script.  Regex can be passed to select anything from the switch/command line.
			5th arguement:  Device file.  If specified, passes device file argument to auto_command_run.  Allows previous scripts to be used from within another without creating a new directory.
			6th arguement:  Command file.  If specified, passes command file argument to auto_command_run.  Allows previous scripts to be used from within another without creating a new directory.
!
!
!   Append the word "test" to the end of either auto_command_run or run_canned to elicit a test run.  This will output an itemized list of what commands will be run on each device.  Good tool to double check your work if using complex jobs or, espeially, scripts that make changes.
!

Note:  For "auto_command_run -m", "run_canned.sh" , and "goto.sh" (alias: runthings, canned, and go"); any of the three command line arguements can be a regex expression.  Just enclose the regex expression (no spaces!) in quotes ( " ) and it will be evaluated. Tip: if using multiple terms when you's expect a space, just use a "." instead.

batch_job
	File placed in job directory to indicate a complex script that, most probably, uses iterations of auto_command_run jobs to create a more complex report.  
	run_canned.sh skips the auto_command_run execution for the complex job, itself; but allows the nested jobs to execute normally, from the menu and command line, with the same execution format.
	
saved_jobs/shrink_wrapped/
	Where saved jobs (from auto_command_run) live.

Handy alias:
	
alias canned='$HOME/saved_jobs/shrink_wrapped/run_canned.sh'
alias go='/scripts/Toolbox/goto.sh'
alias ll='ls -alF'
alias runthings='/scripts/Toolbox/auto_command_run -m'


	
	
	
