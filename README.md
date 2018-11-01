# gin-config-service

Basicly, we'll do a distributed config server, 
support http long pull and websocket push, with web ui admin platform support.

## Config fetch

key,group => value config fetch, http & websocket support.

1. **Data struct**, base on block chain related concept, only insert and no delete:

blockId parentId (childId) timestamp key group type value

2. **Pick DB**, we can pick influx - time series, or mariadb - traditional relational db.

3. **Related Logic**, Add/Update, Query, Revert - No Delete.

Add/Update: `key` + `group` => find the last ? update : add.

The final logic is insert one data, difference is `parentId` = null(insert), `parentId` = last `blockId`(update).

Note: Here need to check double write before update. if last is not the current last, fail over.

Query: `id` + `group` => find last `timestamp`, parse using `type`.

Revert: find the one to be reverted, insert the `key`, `group`, `type`, `value`, and regenerate the blockId, 
set parentId to current latest, timestamp to now. => update logic using the old data.

## Add namespace concept

Different namespace with different access key and secret. Data In different namespace are totally sepreated.

Basicly, I usally use name space as different environment: test, development, production, 
or diffenent production service group in different region: cn, uk, usa, 
or in game related field, different game region: Green Valley Region 1...

nid namespace (isShow orderId)

orderId: link to the id in front of it.

Inserted new data need to add a `namespace` column, which means the namespace of the data.

### Copy A Namespace

This only copy the latest data struct and data, and futher more change need to edit manually.

## User auth

aid key secret namespace authority

authority: maybe query, write, and write also has query auth.

Inserted new data need to add a `minedBy` column, which means who edits the data.

uid email phone password

## Support tag

Sometimes we want to add tag for our config when it performs well.

Add a tag table:

tid namespace timestamp blockId => less than or equal this timestamp / blockId is the snapshoot - tag.

## AB test and batch take effect

Same config table, add white ip address list support.

Need support define ip manually by passing ip in http header / query / body.

The mathched config in AB test always goes first, just as newest timestamp.

If need more than one key to take effect together at once, AB test supports choose many key to take effect together.

## Support multi server clients as load balance for query




