# nodes


## precautions
1. linux node must have >=384MB of memory for dpkg and locale-get to work

## constructiong new node

1. add to SMI
2. create `_0-bootstrap.yml` playbook containing something like
  
  ```
   ---
   - hosts: rlyeh # or ulthar
     vars:
       containers:
         - node01
         - node02
     tasks:
       - name: spin up LXD container
         ansible.builtin.include_tasks: common_lxdct.yml
         with_items: '{{containers}}'
   
   - hosts: node01 node02
     roles:
       - basic
       - prom-monitored
       - loki-monitored
       - ...
     tasks:
       - name: install some base pkg
         apt:
           name:
             - foobar
  ```
3. register in .ds zone 
    1. edit `templates/infra/dns/db.ds`
    2. exec `004_dns_2-zone.yml`
4. register in prometheus configs - at least `federate_host_exporters`
    1. edit `group_vars/prom0x.yml` 
    2. exec `009_prom_1-configure.yml` 
    3. exec `007_grafana_1-deploy.yml`
5. if it's going to be world facing:
    1. add domain to skowronski.cloud zone
        1. edit `dns_skowronski_cloud.yml`
        2. exec `dns_base.yml`
    2. add to websites.conf on intranet and deploy
        1. edit `./templates/intranet/nginx/site_FOO.conf`
        2. exec `008_intranet_1-configure.yml`
6. if node needs database (`ENGINE`) engine access:
    1. add new credentials
        1. edit `../rlyeh-secrets/plain/ansible_vars.yml` - `ENGINE_dbs`
        2. run `../rlyeh-secrets/encrypt.sh`
        2. exec `_ENGINE_3-users-and-dbs.yml`  
7. add relevant entries to `host_vars` and if needed - to `group_vars`
8. if node is statefull:
    1. add to duplicati_users, define usual secrets
        1. create new `host_vars/HOSTNAME.yml` if needed
    2. add to `monitored_duplicati_dropbox_paths` and `duplicati_monitored_jobs`
        1. edit `group_vars/prom0x.yml`
        2. exec `009_prom_1-configure.yml`
        3. make sure new node is member of `duplicati_users` in SMI
        4. exec `duplicati_users.yml`



## deconstructiong obsolete node
1. if node is statefull: **REVIEW DATA**
2. delete from .ds zone 
    1. edit `templates/infra/dns/db.ds`
    2. exec `004_dns_2-zone.yml`
3. delete from prometheus configs - at least `federate_host_exporters`
    1. edit `group_vars/prometheus_users.yml` 
    2. exec `009_prom_1-configure.yml` 
    3. exec `007_grafana_1-deploy.yml`
4. if was going to be world facing:
    1. delete domain from skowronski.cloud zone
        1. edit `dns_skowronski_cloud.yml`
        2. exec `dns_base.yml`
    2. delete from websites.conf on intranet and deploy
        1. edit `./templates/intranet/nginx/site_FOO.conf`
        2. exec `008_intranet_1-configure.yml`
5. if node used database (`ENGINE`) engine access:
    1. edit `../rlyeh-secrets/plain/ansible_vars.yml` - remove from `ENGINE_dbs`
        2. run `../rlyeh-secrets/encrypt.sh`
    1. export DB from engine's console
    2. `DROP DATABASE ...`
6. if node was statefull:
    1. if backups would be useful - backup decrypted passphrase that duplicati uses
    2. remove from `monitored_duplicati_dropbox_paths` and `duplicati_monitored_jobs`
        1. edit `group_vars/prom0x.yml`
        2. exec `009_prom_1-configure.yml` 
        3. exec `007_grafana_1-deploy.yml`
7. delete by hand on hypervisor using `lxd delete` 
8. remove from SMI


