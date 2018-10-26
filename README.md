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

Revert: find the one to be reverted, insert the `key`, `group`, `type`, `value`, and regenerate the blockId, set parentId to current latest, timestamp to now. => update logic using the old data.

## Add namespace concept

Different namespace with different access key and secret.

Basicly, I usally use name space as different environment: test, development, production, 
or diffenent production service group in different region: cn, uk, usa, 
or in game related field, different game region: Green Valley Region 1...

id namespace key secret authority (isShow orderId)

authority: maybe query, write, and write also has query auth.

orderId: link to the id in front of it.

## Support multi server clients as load balance for query




