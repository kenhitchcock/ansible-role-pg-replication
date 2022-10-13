# Ansible Role: pg-replication 

Installs and configures PostgreSQL server on RHEL/CentOS or Debian/Ubuntu servers to replicate Postgresql data between system.

## Requirements

Postgresql will need to be installed on all the systems that will be used in the configuration; note that this role requires postgres user and root access, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:

    - hosts: database
      var_file: 
        - /path/to/vars/file
      roles:
        - role: ansible-role-pg-replication
          become: yes

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

Below is a list of all basic variables used throughout the role.

| Variable | Description | Required | Defaults |
|:--------:|:-----------:|:--------:|:--------:|
| **pg_replication_master** | The name of the Postgresql server that will be the primary database server. This will be the same name as the inventory name. | yes | Not set | 	 
| **pg_replication_primary_conninfo** | The connectivity details of the primary database server, including the name of the replication user.. | yes | Not Set but should look similar to 'host=192.168.5.200 port=5432 user=repuser'  | 	 
| **pg_replication_level** | This is the level of archive file logging. The highest level is logical, NOT to be confused with replication type.. | yes | /var/lib/pgsql/data/pg_hba.conf | 	 
| **pg_replication_archive_wals** | This is to indicate if wal files should be archived. If set to true, the nfs_export var below is required to be set | yes | false | 	 
| **pg_replication_nfs_export** | NFS export and server to be used for the archive files if you choose to use nfs. | no | Not set |
| **pg_replication_mounted_path** | The path where the NFS export would be mounted on the postgresql server. | yes | /mnt/archive_export | 	 
| **pg_replication_user** | The user that will be used for replication | yes | repuser |
| **pg_replication_db** | The database that will be replicated | yes | postgres |
| **pg_replication_database_user** | Which local user is used to manage the postgresql services, files ect.. | yes | postgres | 	 
| **pg_replication_database_group** | Which local group is used to manage the postgresql services, files ect.. | yes | postgres | 	 
| **pg_replication_type** | How replication will be done. Options are logical and log_shipping_with_streaming | yes | log_shipping_with_streaming | 	 
| **pg_replication_backup** | The choice to run a backup before configuration is done | yes | true | 	 
| **pg_replication_backup_loc** | Where the backup files will be stored on the remote postgresql server | yes | /tmp | 	 
| **pg_replication_postgresql_path** | Where postgresql server files have been installed | yes | /var/lib/pgsql | 	 
| **pg_replication_postgresql_data_dir** | What the name of the postgresql server data directory is. | yes | data | 	 
| **pg_replication_export_path** | Location of export files will be dumped. | yes | /tmp/ | 	 
| **pg_replication_local_export_path** | Location of where remote export files will be dumped when copied to ansible executing host. | yes | /tmp/ | 	 
| **pg_replication_export_file** | Name of the export file that will be created if the database is backed up. | yes | postgresql_data.tar.gz | 	 
| **pg_replication_hba_conf_file** | Name and path to the file that will control hba configuration. | yes | /var/lib/pgsql/data/pg_hba.conf | 	 
| **pg_replication_slots** | The number of replication slots that will be used in the Postgresql configuration. | yes | '2' | 	 
| **pg_replication_max_concurrent_connections** | The number of concurrent replication connections. | yes | '2' | 	 
| **pg_replication_max_logical_replication_workers** | The max number of logical replication workers. | yes | '10' | 	 
| **pg_replication_max_worker_processes** | The max number of logical replication worker processes. | yes | '10' | 	 
| **pg_replication_recovery_file** | Path to where the recovery.conf file will be created. | yes | /var/lib/pgsql/data/recovery.conf | 	 
| **pg_replication_trigger_file** | Path to where the trigger file will be located to initiate failover. | yes | /tmp/postgresql.trigger.5432 | 	 
| **pg_replication_pubname** | When using Logical replication, this is the name of the Publisher created on the primary database server. | yes | dbpublisher | 	 
| **pg_replication_subname** | When using Logical replication, this is the name of the Subscriber created on the secondary database server. | yes | dbsubscriber | 	 
| **pg_replication_tablenames** | When using Logical replication, this is part of the query that builds a list of all tables that will be added to be replicated. | yes | "CREATE PUBLICATION {{pg_replication_pubname}} FOR TABLE " | 	 


Below are the dictionaries that used in the role.

The pg_hba_config is to help populate the values that need to be configured in the pg_hba.conf file.

    pg_hba_config:
      hba_config1:
        pg_hba_contype: host
        pg_hba_method: trust
        pg_hba_user: towerrepuser
        pg_hba_db: replication
        pg_hba_source: 0.0.0.0/0
      hba_config2:
        pg_hba_contype: host
        pg_hba_method: trust
        pg_hba_user: tower
        pg_hba_db: tower
        pg_hba_source: 0.0.0.0/0

The pg_slot_names is to configure the slots that are required when using Logical replication. Not required when using log shipping.
    pg_slot_names:
      - postgresql01
      - postgresql02



## Dependencies

None.

## Example Playbook

    - hosts: database
      become: yes
      vars_files:
        - vars/main.yml
      roles:
        - ansible-role-pg-replication

*Inside `vars/main.yml`*:

    postgresql_databases:
      - name: example_db
    postgresql_users:
      - name: example_user
        password: supersecure

## License

MIT / BSD

## Author Information

This role was created in the darkest period of 2020 by [Ken Hitchcock](https://github.com/kenhitchcock).
