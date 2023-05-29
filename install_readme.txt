From Oracle VirtualBox, Shared Folders, share your Downloads folder.

Grab the tarball and install file from the Works folder under the NetworkOT Sharepoint.
	install_run_command_package
	automation_code.tar.
On the VM, go to the root directory, then:
	sudo cp /media/sf_Downloads/install_run_command_package .
	sudo ./install_run_command_package <insert your ubuntu vm username>
	
Once that is complete..........
	edit $USER/aaa_config.txt
		Remove top line with leading "#"
		update line beginning with "acs" with your corp login ID and password
	
	copy contents of /scripts/Toolbox/add_to_bashrc.txt to $HOME/.bashrc
	
	restart the VM
		
Should be good to go.
