

<b> Requirements:

- Ansible >= 1.6
- Oracle Linux (or any RHEL-based Linux System) >= 6.4
- Oracle Database/Grid Infrastructure 12.1.0.1 & 12.1.0.2, 11.2.0.4

</b>

At the moment you can install Oracle RAC, RAC One Node and normal single instances.
You can take a freshly installed machine and configure it from ground up. It'll conofigure users, profiles, kernel parameters, storage and install the database server and create one or more databases.

By default, you can install a single instance database on filesystem storage, without having to change any parameters.

<b>Note: </b>
- You'll need to manually download the Oracle software and make it available to the control-machine (either locally or on a web-server) before running the playbook.
- All roles are built on Oracle Linux 6, but should work with any EL6-based system.
- Storage options only supports block devices at the moment (FS & ASM). Will add support for NFS

<b>The different roles are:</b>

<b> common: </b>
This will configure stuff common to all machines
- Install some generic packages 
- Configure ntp 
- Possibly add a default/deploy user.

<b>orahost:</b>
This will configure the host specific Oracle stuff:
- Add a user & group
- Create directory structures
- Generate ssh-keys and set up passwordless ssh between clusternodes in case of RAC/RAC One node
- Handle filesystem storage (partition devices, creates vg/lv and a ext4 filesystem etc). If you want to create your database on a filesystem (instead of ASM) this is where you define the layout.
- Install required packages
- Change kernel paramemeters
- Set up pam.d/limits config
- Configures Hugepages (as a percentage of total RAM)
- Disables transparent hugepages
- Disables NUMA (if needed)
- Configures the interconnect network (if needed)
- Configures Oracle ASMLib 

<b>orahost-storage:</b>
This role configures storage that shoud be used by ASM.
- Partitions devices (using parted)
- Create ASMlib labels on disks

<b>oraswgi-install:</b>
This role will install and configure Oracle Grid Infrastructure. Tested with 12.1.0.1/12.1.0.2 & 11.2.0.4
- Adds a .profile_grid to the oracle user
- Sets up directory structures
- Copies the install-files to the servers
- Install Oracle Grid Infrastructure

<b>oralsnr:</b>
This role will create a default listener.

Note:
At the moment there is no listener configured when creating a database on a filesystem (i.e no grid infrastructure present). Will be added later though.
- Uses srvctl to add/start the default listener

<b>oraasm-configureasm:</b>
This role will create and configure the ASM-instance with an initial diskgroup.

- Generates a shellscript that uses asmca to create the ASM instance

<b>oraasm-createdg:</b>
This role will create the diskgroup(s) that should be used for database storage. Uses asmca to create diskgroups.
- Generates a shellscript that uses asmca to create the diskgroups. 
Note: It will try to create the initial diskgroup again, and fail but that is ok, the error is ignored and the play will continue.

<b>oraswdb-install:</b>
This role will install the oracle database server(s). If there will be more than one database running it will get its own ORACLE_HOME. It performs both Single Instance/RAC installations.
- Creates a .profile_databasename
- Creates directory structures
- Transfers installfiles to server(s)
- Installs the database-server(s)

<b>oradb-create:</b>
This role creates the databases (RAC/RAC One Node, Single Instance). Performs a dbca silent run to create the database.
Note:
At the moment there is no listener configured when creating a database on a filesystem (i.e no grid infrastructure present). Will be added later though.
- Generates a responsefile to be used by dbca
- Creates the db using dbca
- Changes parameters based on input.

<b>oradb-delete:</b>
This role deletes the databases, using dbca.
Note:
- This is toggled by the variable delete_db (true/false), which is embedded in the 'oracle_databases' dict.

<b>*** THE FOLLOWING ROLES ARE NOT FINISHED/NOT WORKING PROPERLY YET ****</b>

<b>oraswgi-opatch:</b>
This role will use opatch to apply a patch to a Grid Infrastructure home.

<b>oraswgi-clone:</b>
This role will use a previously installed/patched Grid Infrastructure installation to perform a new Grid Infrastructure installation using the clone method

<b>oraswracdb-clone:</b>
This role will take a previously installed/patched Oracle Database Server installation to perform a new database server installation using the clone method.



<b>TODO</b>
- Add support for creating container databases
- Add service to database as part of db-creation
- Add support for NFS storage
- Add support for installing software from NFS 
- Cleanup
- .........
