# networking guidelines

## deciated vSwitch <-> HC network direct connect
HC network:
* `172.16.0.0/28` network
* `172.16.0.1` gateway
* `172.16.0.2` IP of ulthar
* `10.1.0.1/8` IPsec IP of ulthar

vSwitch side:
* `172.16.0.16/28` network
* `172.16.0.17` gateway
* `172.16.0.18` IP of rlyeh
* `10.0.0.1/8` IPsec IP of rlyeh

## IP allocation scheme: `10.LOCATION.CATEGORY.SEQ_ID`

## locations
* 0 - Rlyeh
* 1 - Ulthar
* 10 - C10

## categories
* 0 - reserved for HVs
* 1 - web public
* 2 - mysql db
* 3 - psql db
* 4 - dns
* 5 - influx db
* 6 - sandbox
* 7 - infra
* 8 - web private
* 9 - monitoring
* 10 - redis
* 11 - consul
* 254 - remote clients
* 255 - remote clients

## sequential IDs

`SEQ_ID` is global id of server in given category, even across different locations

## DNS

DNS aliases are `${SERVICE_NAME}${SEQ_ID}`, where `${SEQ_ID}` is derived from IP