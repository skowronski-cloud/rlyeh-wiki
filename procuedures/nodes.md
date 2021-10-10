# nodes

## constructiong new node
### precautions
1. linux node must have >=256MB of memory for dpkg to work
1. add to SMI
2. build by hand on https://10.201.0.1:8006/
3. register in .ds zone 
    1. edit `templates/infra/dns/db.ds`
    2. exec `infra_dns_deploy-zone.yml`
4. register in prometheus configs - at least `federate_host_exporters`
    1. edit `group_vars/prometheus_users.yml` 
    2. exec `infra_prometheus_deploy_rules.yml` 
    3. exec `infra_grafana_deploy.yml`
5. if it's going to be world facing:
    1. add domain to skowronski.cloud zone
        1. edit `dns_skowronski_cloud.yml`
        2. exec `dns_base.yml`
    2. add to websites.conf on intranet and deploy
        1. edit `./templates/infra/intranet/nginx/websites.conf.j2`
        2. exec `infra_intranet_deploy_configs.yml`
6. if node needs database (`ENGINE`) engine access:
    1. add new encrypted credentials
        1. edit `group_vars/ENGINE_users.yml`
        2. exec `dbs_ENGINE_deploy.yml`  
    2. add node to group `ENGINE_users` on SMI
7. if node needs telegram bot:
    1. create it by hand by interacting with `/bot` (BotFather)
        1. `/newbot`
        2. save displayed credentials
    2. add credentials to `telegram_bots`
        1. edit `group_vars/telegram_users.yml`
        2. exec new `_base.yml` playbook for machine
7. delete relevant entries from `host_vars` and if needed - from `group_vars` (when it's last host in group)
8. if node is statefull:
    1. add to duplicati_users, define usual secrets
        1. create new `host_vars/HOSTNAME.yml`
        2. exec new `_base.yml` playbook for machine
    2. add to `monitored_duplicati_dropbox_paths` and `duplicati_monitored_jobs`
        1. edit `group_vars/prometheus_users.yml`
        2. exec `infra_prometheus_deploy_rules.yml` 
        3. make sure new node is member of `duplicati_users` in SMI
        4. exec `duplicati_users.yml`



## deconstructiong obsolete node
1. if node is statefull: **REVIEW DATA**
3. delete from .ds zone 
    1. edit `templates/infra/dns/db.ds`
    2. exec `infra_dns_deploy-zone.yml`
4. delete from prometheus configs - at least `federate_host_exporters`
    1. edit `group_vars/prometheus_users.yml` 
    2. exec `infra_prometheus_deploy_rules.yml` 
    3. exec `infra_grafana_deploy.yml`
5. if was going to be world facing:
    1. delete domain from skowronski.cloud zone
        1. edit `dns_skowronski_cloud.yml`
        2. exec `dns_base.yml`
    2. delete from websites.conf on intranet and deploy
        1. edit `./templates/infra/intranet/nginx/websites.conf.j2`
        2. exec `infra_intranet_deploy_configs.yml`
6. if node used database (`ENGINE`) engine access:
    1. export DB from engine's console
    2. `DROP DATABASE ...`
7. if node used telegram bot:
    1. delete it by hand by interacting with `/bot` (BotFather)
      1. `/deletebot`
    2. remove credentials from `telegram_bots`
      1. edit `group_vars/telegram_users.yml`
8. if node was statefull:
    1. if backups would be useful - backup decrypted passphrase that duplicati uses
    2. remove from `monitored_duplicati_dropbox_paths` and `duplicati_monitored_jobs`
        1. edit `group_vars/prometheus_users.yml`
        2. exec `infra_prometheus_deploy_rules.yml` 
9. delete by hand on 10.201.0.6:8006 - if was never fully deployed it's preferred to *purge*
2. remove from SMI


