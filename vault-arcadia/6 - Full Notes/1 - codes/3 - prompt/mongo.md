
# 1 
user's indexes:
```
[{
	"name" : "_id_",
	"key" : {
		"_id" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "11203 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"_id" : 1
			},
			"name" : "_id_"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "new_rule_1",
	"key" : {
		"new_rule" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "24 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"new_rule" : 1
			},
			"name" : "new_rule_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "policy_role_master_1",
	"key" : {
		"policy_role_master" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "55 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"policy_role_master" : 1
			},
			"name" : "policy_role_master_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "sip_id_1",
	"key" : {
		"sip_id" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "305122 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"unique" : true,
		"spec" : {
			"v" : 2,
			"unique" : true,
			"key" : {
				"sip_id" : 1
			},
			"name" : "sip_id_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "verify_1",
	"key" : {
		"verify" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "561 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"unique" : true,
		"spec" : {
			"v" : 2,
			"unique" : true,
			"key" : {
				"verify" : 1
			},
			"name" : "verify_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "verify_hash_1",
	"key" : {
		"verify_hash" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "36 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"verify_hash" : 1
			},
			"name" : "verify_hash_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "status_1",
	"key" : {
		"status" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "94 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"status" : 1
			},
			"name" : "status_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "qr_code_1",
	"key" : {
		"qr_code" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "10 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"qr_code" : 1
			},
			"name" : "qr_code_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "custom_username_1",
	"key" : {
		"custom_username" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.users",
	"accesses" : "133 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"unique" : true,
		"spec" : {
			"v" : 2,
			"unique" : true,
			"key" : {
				"custom_username" : 1
			},
			"name" : "custom_username_1"
		}
	},
	"host" : "ee5ece358334:30000"
}]
```
Why this query use index new_rule, and why it is ineffective ?
```
{
	"command" : {
		"aggregate" : "users",
		"pipeline" : [
			{
				"$match" : {
					"$and" : [
						{
							"active" : {
								"$in" : [
									true
								]
							}
						},
						{
							"new_rule" : {
								"$in" : [
									true
								]
							}
						}
					]
				}
			},
			{
				"$group" : {
					"_id" : {
						"policy_role_master" : "$policy_role_master"
					},
					"count" : {
						"$sum" : 1
					}
				}
			},
			{
				"$project" : {
					"_id" : false,
					"policy_role_master" : "$_id.policy_role_master",
					"count" : true
				}
			},
			{
				"$sort" : {
					"count" : -1
				}
			},
			{
				"$limit" : 4
			}
		],
		"cursor" : {
			
		},
		"lsid" : {
			"id" : UUID("b5369404-cc8e-4e49-8983-e93b508f5ce2")
		},
		"$clusterTime" : {
			"clusterTime" : Timestamp({ t: 1761895602, i: 1 }),
			"signature" : {
				"hash" : BinData(0,"q650PBtW0cl8Zk0gU1q6IuWyuAg="),
				"keyId" : Long("7514739574405333000")
			}
		},
		"$db" : "db_api",
		"$readPreference" : {
			"mode" : "primary"
		}
	},
	"keysExamined" : 208865,
	"docsExamined" : 208865,
	"cursorExhausted" : true,
	"numYield" : 211,
	"nreturned" : 4,
	"locks" : {
		"ReplicationStateTransition" : {
			"acquireCount" : {
				"w" : Long("1")
			}
		},
		"Global" : {
			"acquireCount" : {
				"r" : Long("220")
			}
		},
		"Mutex" : {
			"acquireCount" : {
				"r" : Long("8")
			}
		}
	},
	"readConcern" : {
		"level" : "local",
		"provenance" : "implicitDefault"
	},
	"writeConcern" : {
		"w" : "majority",
		"wtimeout" : 0,
		"provenance" : "implicitDefault"
	},
	"responseLength" : 430,
	"millis" : 906,
	"planSummary" : "IXSCAN { new_rule: 1 }",
	"ts" : ISODate("2025-10-31T14:26:51.921+07:00")
}
```
---
# 2

joined_conversations indexes:
```
[{
	"name" : "conv_id_1_sip_id_1_platform_1",
	"key" : {
		"conv_id" : 1,
		"sip_id" : 1,
		"platform" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.joined_conversations",
	"accesses" : "313873 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"conv_id" : 1,
				"sip_id" : 1,
				"platform" : 1
			},
			"name" : "conv_id_1_sip_id_1_platform_1"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "_id_",
	"key" : {
		"_id" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.joined_conversations",
	"accesses" : "39188 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"_id" : 1
			},
			"name" : "_id_"
		}
	},
	"host" : "ee5ece358334:30000"
},
{
	"name" : "conv_type_1_sip_id_1",
	"key" : {
		"conv_type" : 1,
		"sip_id" : 1
	},
	"type" : "REGULAR",
	"ns" : "db_api.joined_conversations",
	"accesses" : "37403 since 10/30/2025, 4:43:34 PM",
	"properties" : {
		"spec" : {
			"v" : 2,
			"key" : {
				"conv_type" : 1,
				"sip_id" : 1
			},
			"name" : "conv_type_1_sip_id_1"
		}
	},
	"host" : "ee5ece358334:30000"
}]
```

why this query is too slow:
```
{
	"command" : {
		"find" : "joined_conversations",
		"filter" : {
			"conv_id" : "94b6f697-b8a9-4190-bf0e-efff719ef2a0"
		},
		"limit" : Long("50"),
		"projection" : {
			"sip_id" : 1,
			"is_admin" : 1,
			"invite_by" : 1
		},
		"skip" : Long("0"),
		"sort" : {
			"_id" : -1
		},
		"lsid" : {
			"id" : UUID("e203bf4a-9a8a-4d35-a396-d85604771105")
		},
		"$clusterTime" : {
			"clusterTime" : Timestamp({ t: 1761896392, i: 1 }),
			"signature" : {
				"hash" : BinData(0,"UoMSqgg2UR3KkAq7hS5i7VQLfoo="),
				"keyId" : Long("7514739574405333000")
			}
		},
		"$db" : "db_api"
	},
	"keysExamined" : 80353,
	"docsExamined" : 80353,
	"cursorExhausted" : true,
	"numYield" : 88,
	"nreturned" : 3,
	"locks" : {
		"ReplicationStateTransition" : {
			"acquireCount" : {
				"w" : Long("1")
			}
		},
		"Global" : {
			"acquireCount" : {
				"r" : Long("90")
			}
		},
		"Mutex" : {
			"acquireCount" : {
				"r" : Long("1")
			}
		}
	},
	"readConcern" : {
		"level" : "local",
		"provenance" : "implicitDefault"
	},
	"responseLength" : 481,
	"millis" : 690,
	"planSummary" : "IXSCAN { _id: 1 }",
	"ts" : ISODate("2025-10-31T14:39:53.607+07:00")
}
```
---
# 3: After apply sort conv_id:
```

db.system.profile.find({
    // millis: $.gte(100),
    // ns: "db_api.joined_conversations"
    ns: "db_api.joined_conversations"
    // ns: /joined_conversations/
})

    .limit(10)
    .pretty()
    .project({
        "command": 1,
        "keysExamined": 1,
        "docsExamined": 1,
        "fromMultiPlanner": 1,
        "cursorExhausted": 1,
        "numYield": 1,
        "nreturned": 1,
        "locks": 1,
        "readConcern": 1,
        "writeConcern": 1,
        "responseLength": 1,
        "millis": 1,
        "planSummary": 1,
        "ts": 1,
        "ns": 1
    })
    .sort({
        // millis: -1, // time executed
        ts: -1 // timestamp, 
        ,docsExamined: -1
    })

>>>Output: 
[{
	"ns" : "db_api.joined_conversations",
	"command" : {
		"find" : "joined_conversations",
		"filter" : {
			"conv_id" : "f6b8805d-1ba3-414c-9665-43f5ee21a325"
		},
		"limit" : Long("200"),
		"projection" : {
			"sip_id" : 1,
			"is_admin" : 1,
			"invite_by" : 1
		},
		"skip" : Long("9400"),
		"sort" : {
			"conv_id" : 1,
			"_id" : -1
		},
		"lsid" : {
			"id" : UUID("92b48bf3-6f24-42c3-bcfd-45efa62d4d45")
		},
		"$clusterTime" : {
			"clusterTime" : Timestamp({ t: 1761904120, i: 2 }),
			"signature" : {
				"hash" : BinData(0,"4nWioPz5at2O6C9wlYP/dcEVe2I="),
				"keyId" : Long("7514739574405333000")
			}
		},
		"$db" : "db_api"
	},
	"keysExamined" : 9999,
	"docsExamined" : 9999,
	"numYield" : 20,
	"nreturned" : 101,
	"locks" : {
		"ReplicationStateTransition" : {
			"acquireCount" : {
				"w" : Long("1")
			}
		},
		"Global" : {
			"acquireCount" : {
				"r" : Long("22")
			}
		},
		"Mutex" : {
			"acquireCount" : {
				"r" : Long("1")
			}
		}
	},
	"readConcern" : {
		"level" : "local",
		"provenance" : "implicitDefault"
	},
	"responseLength" : 8832,
	"millis" : 55,
	"planSummary" : "IXSCAN { conv_id: 1, sip_id: 1, platform: 1 }",
	"ts" : ISODate("2025-10-31T16:48:40.666+07:00")
},
{
	"ns" : "db_api.joined_conversations",
	"command" : {
		"find" : "joined_conversations",
		"filter" : {
			"conv_id" : "f6b8805d-1ba3-414c-9665-43f5ee21a325"
		},
		"limit" : Long("200"),
		"projection" : {
			"sip_id" : 1,
			"is_admin" : 1,
			"invite_by" : 1
		},
		"skip" : Long("9200"),
		"sort" : {
			"conv_id" : 1,
			"_id" : -1
		},
		"lsid" : {
			"id" : UUID("09ebb5f9-6b77-4bc6-a359-f179df9140e3")
		},
		"$clusterTime" : {
			"clusterTime" : Timestamp({ t: 1761904119, i: 6 }),
			"signature" : {
				"hash" : BinData(0,"rnLvQdoKiASX4HvyrZhQCCDasvY="),
				"keyId" : Long("7514739574405333000")
			}
		},
		"$db" : "db_api"
	},
	"keysExamined" : 9999,
	"docsExamined" : 9999,
	"numYield" : 20,
	"nreturned" : 101,
	"locks" : {
		"ReplicationStateTransition" : {
			"acquireCount" : {
				"w" : Long("1")
			}
		},
		"Global" : {
			"acquireCount" : {
				"r" : Long("22")
			}
		},
		"Mutex" : {
			"acquireCount" : {
				"r" : Long("1")
			}
		}
	},
	"readConcern" : {
		"level" : "local",
		"provenance" : "implicitDefault"
	},
	"responseLength" : 9103,
	"millis" : 50,
	"planSummary" : "IXSCAN { conv_id: 1, sip_id: 1, platform: 1 }",
	"ts" : ISODate("2025-10-31T16:48:39.939+07:00")
},
{
	"ns" : "db_api.joined_conversations",
	"command" : {
		"find" : "joined_conversations",
		"filter" : {
			"conv_id" : "f6b8805d-1ba3-414c-9665-43f5ee21a325"
		},
		"limit" : Long("200"),
		"projection" : {
			"sip_id" : 1,
			"is_admin" : 1,
			"invite_by" : 1
		},
		"skip" : Long("9000"),
		"sort" : {
			"conv_id" : 1,
			"_id" : -1
		},
		"lsid" : {
			"id" : UUID("09ebb5f9-6b77-4bc6-a359-f179df9140e3")
		},
		"$clusterTime" : {
			"clusterTime" : Timestamp({ t: 1761904118, i: 86 }),
			"signature" : {
				"hash" : BinData(0,"/X/GNvqGY12ZSlfQOiYYbXwnPrc="),
				"keyId" : Long("7514739574405333000")
			}
		},
		"$db" : "db_api"
	},
	"keysExamined" : 9999,
	"docsExamined" : 9999,
	"numYield" : 20,
	"nreturned" : 101,
	"locks" : {
		"ReplicationStateTransition" : {
			"acquireCount" : {
				"w" : Long("1")
			}
		},
		"Global" : {
			"acquireCount" : {
				"r" : Long("22")
			}
		},
		"Mutex" : {
			"acquireCount" : {
				"r" : Long("1")
			}
		}
	},
	"readConcern" : {
		"level" : "local",
		"provenance" : "implicitDefault"
	},
	"responseLength" : 9173,
	"millis" : 56,
	"planSummary" : "IXSCAN { conv_id: 1, sip_id: 1, platform: 1 }",
	"ts" : ISODate("2025-10-31T16:48:39.537+07:00")
}]
```
--> It works, so we need to add sort conv_id to every query now?
---
# 4: 
