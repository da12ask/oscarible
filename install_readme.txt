From Oracle VirtualBox, Shared Folders, share your Downloads folder.

Grab the tarball and install file from the Works folder under the NetworkOT Sharepoint.
	install_run_command_package
	automation_code.tar.gz
	
	https://sharepoint.xcelenergy.com/sites/spop02/networkot/DesignArch/_layouts/15/start.aspx#/Works/Forms/AllItems.aspx?RootFolder=%2Fsites%2Fspop02%2Fnetworkot%2FDesignArch%2FWorks%2FAutomation%20Code%20%28Oscarible%29&FolderCTID=0x012000899C338A23A6BF4C920B6F48B2DCA818&View=%7B5E7928EA%2D5913%2D4C1A%2D9512%2DCF142577B8BC%7D

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
