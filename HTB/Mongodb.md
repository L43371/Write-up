# Mongodb guide

### To connect to MongoDB

`┌──(root㉿kali)-[/opt/mongodb-linux-x86_64-3.4.7/bin]
└─# ./mongo mongodb://10.129.228.30:27017
`

Commands

Show database

`show dbs`

`> show dbs
admin                  0.000GB
config                 0.000GB
local                  0.000GB
sensitive_information  0.000GB
users                  0.000GB
`
Use database

`use sensitive_information`
`> show collections
flag
`

To retrieve the file

`> db.flag.find().pretty()
{
        "_id" : ObjectId("630e3dbcb82540ebbd1748c5"),
        "flag" : "1b6e6fb359e7c40241b6d431427ba6ea"
}
`
