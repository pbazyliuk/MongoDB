# MongoDB

# 1) Накатить бэкап базы (1)

  # mongoimport --db sample ./users.json


# 2) Найти средний возраст людей в системе (1.5)

  # request:
 	
  # db.users.aggregate([{$group: {_id:null, age: {$avg:"$age"} } }])


  # response:

  # { "_id" : null, "age" : 30.38862559241706 }


# 3) Найти средний возраст в штате Аляска (1.5)

  # request:

    # db.users.aggregate([{ $match : { address : /Alaska/ } }, {$group: {_id:null, age: {$avg:"$age"} } }]);

  # response:

    # { "_id" : null, "age" : 31.5 }


# 4) Начиная от Math.ceil(avg + avg_alaska) найти первого человека с другом по имени Деннис (1.5)

  # request:

  # db.users.aggregate( [ {$match: { age: {$lt: 62}}}, {$unwind: "$friends"}, { $project : {name: 1, address: 1, "friendsName" : { $split: ["$friends.name", " "] }} },{ $project : {name: 1, address: 1,  friendsName: { $arrayElemAt: [ "$friendsName", 0 ] }, } },{ "$match": { "friendsName": { "$regex": "Dennis", "$options": "i" } }}, { $limit : 1 }])

  # response:

 #  { "_id" : ObjectId("5adf3c1544abaca147cdd34b"), "name" : "Macias Shannon", "address" : "221 Gardner Avenue, Tetherow, Virginia, 464", "friendsName" : "Dennis" }


# 5) Найти людей из того же штата, что и предыдущий человек и посмотреть какой фрукт любят больше всего в этом штате (аггрегация) (1.5)

  # request:
    # db.users.aggregate([{ $match : { address : /Virginia/ }}, {"$sortByCount" : "$favoriteFruit" }]);

  # response:
  # { "_id" : "strawberry", "count" : 14 }
  # { "_id" : "banana", "count" : 9 }
  # { "_id" : "apple", "count" : 7 }


# 6) Найти саммого раннего зарегистрировавшегося пользователя с таким любимым фруктом (1.5)

  # request:

  # db.users.find( { favoriteFruit: "strawberry" }, { _id: 1, name: 1, address: 1, registered: 1, favoriteFruit: 1 }).sort({"registered"  : 1}).limit(1)

  # response:

  # { "_id" : ObjectId("5adf3c1544abaca147cdd3d5"), "name" : "Randall Conner", "address" : "167 Berkeley Place, Nogal, Washington, 4955", "registered" : "2014-01-02T02:39:51 -02:00", "favoriteFruit" : "strawberry" }


# 7) Добавить этому пользовелю свойтво: { features: 'first apple eater' } (1)

  # request:

    # db.users.update({_id:  ObjectId("5adf3c1544abaca147cdd3d5")}, { $set: {features: "first apple eater"}})

  # response: 

    # WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })


  # check: 

    # request:

    # db.users.find( { favoriteFruit: "strawberry" }, { _id: 1, name: 1, address: 1, registered: 1, favoriteFruit: 1, features: 1 }).sort({"registered" : 1}).limit(1)

  # response:

  # { "_id" : ObjectId("5adf3c1544abaca147cdd3d5"), "name" : "Randall Conner", "address" : "167 Berkeley Place, Nogal, Washington, 4955", "registered" : "2014-01-02T02:39:51 -02:00", "favoriteFruit" : "strawberry", "features" : "first apple eater" }


  # 8) Удалить всех любителей клубники (написать количество удаленных пользователей) (1)

  # request
    # db.users.remove({favoriteFruit: "strawberry"})

  # response:
    # WriteResult({ "nRemoved" : 253 })
