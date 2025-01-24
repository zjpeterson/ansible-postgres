# ansible-postgres

Demo of using Ansible for PostgreSQL database automation.

`setup.yml` - Installs postgres and makes some dummy data

`db_info.yml` - Queries and displays version info, number of DBs, disk usage across all DBs

`db_create.yml` - Self-service database creation with admin user setup

`aap_query_jt.yml` - Creates JTs in AAP with surveys for selection of present DBs

`db_query.yml` - Ensures a select-only user exists and uses it to run a query and dump the results to CSV

## Demo Setup

This is intended to run in AWS but could run anywhere.

1. Build AAP
2. Build pgsql machines with tag `Application=pgsql`
3. Configure credentials: AWS API and EC2 SSH
4. Configure dynamic inventory in AAP
```yaml
filters:
  tag:Application: pgsql
keyed_groups:
  - prefix: tag
    key: tags
hostnames:
  - tag:Name
compose:
  ansible_host: private_ip_address
```
5. Make JT for `db_info.yml`
6. Make JT for `db_create.yml`
7. Make JT for `aap_query_jt.yml`
8. Make Worfklow JT linking all 3 above, with survey populating `database_name`
9. Run `setup.yml` to populate DBs

## Demo 1
Run the Workflow, which shows the following:
1. Info job is able to show version/size/count data to the console
2. Info job is able to decide where to put the next DB
3. Create job makes the DB as requested
4. New DB is connectable
5. New DB shows up in the Query JT surveys

## Demo 2
Run one of the Query JTs created by Demo 1, which shows the following:
1. Data can be queried as YAML
2. YAML results can be saved as CSV to anywhere (in this case, S3)
