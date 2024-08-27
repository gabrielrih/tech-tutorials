# Running a compact on a specific collection

Considerations:
- You must performe this action node by node, first in the secondaries. Then you "force" a failover and performe in the primary (which is now a secondary).
- The username must have the [dbAdmin](https://www.mongodb.com/docs/manual/reference/command/compact/#compact-required-privileges) permission.

Connecting to the node on Atlas:
```sh
mongo "mongodb://pl-0-brazilsouth-azure.dbj6q.mongodb.net:1024" --ssl --authenticationDatabase admin --username myusername --password mypassword
```

Picking the right database:
```sh
use local
```

Compacting a collection:
```sh
db.runCommand({ compact: "collection_name" })
```

> This commands locks the shell, so if you want to see the command running you must open a new mongo connection and runs it (where local is the database name):
```sh
db.currentOp(
		{
		"active" : true,
		"ns" : /^local\./
		}
	)
```

If you want to see the freeStorageSize you can run this command. This is important to be run before and after the compacting, so you can see how many space were released to the OS:
```sh
db.runCommand({ 
    collStats: "collection_name",
    scale: 1024 * 1024 * 1024
})
```

Usefull script to compact all needed collections:
```js
db.getMongo().setReadPref("secondary")

use <database_name>;

const allDbCollections = db.getCollectionNames()

allDbCollections.forEach( collName =>{
    let collectionStats = db.getCollection(collName).aggregate([{"$collStats":{ storageStats: {} }}]).next()

    let freeSize = collectionStats.storageStats.freeStorageSize / (1024 * 1024)
    let totalSize = collectionStats.storageStats.totalSize / (1024 * 1024)
    let percentage = ((freeSize/totalSize) * 100).toFixed(0)
    let freeSizeGB = (freeSize / 1024).toFixed(0)

    if (rs.isMaster().secondary) {
        if (percentage >= 10 && freeSizeGB >= 1) {
            print('Starting to run compact on collection ' + collName)
            db.runCommand({ "compact": collName })
        }   
    } else {
        print('It is forbbiden to run compact on primary node')
    }
})
```

References: 
- [Compact](https://www.mongodb.com/docs/manual/reference/command/compact/)
