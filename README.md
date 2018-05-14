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
    Math.ceil(30.38862559241706 + 31.5) = 62;

    # db.users.aggregate( [ { $skip : 62 }, {$unwind: "$friends"}, { $project : {name: 1, address: 1, "friendsName" : { $split: ["$friends.name", " "] }} },{ $project : {name: 1, address: 1,  friendsName: { $arrayElemAt: [ "$friendsName", 0 ] }, } },{ "$match": { "friendsName": { "$regex": "Dennis", "$options": "i" } }}])

    # response:

    #  { "_id" : ObjectId("5adf3c1544abaca147cdd539"), "name" : "Keller Nixon", "address" : "591 Jamison Lane, Idamay, Minnesota, 3128", "friendsName" : "Dennis" }


# 5) Найти активных людей из того же штата, что и предыдущий человек и посмотреть какой фрукт любят больше всего в этом штате (аггрегация) (1.5)

    # request:
    state = "Minnesota"

    # db.users.aggregate([{ $match : { $and: [ { address : /Minnesota/}, { isActive : true}] } }, {"$sortByCount" : "$favoriteFruit" }, {$limit: 1}]);

    # response:
    
    # { "_id" : "apple", "count" : 4 }
    

# 6) Найти саммого раннего зарегистрировавшегося пользователя с таким любимым фруктом (1.5)

    # request:
    favoriteFruit = "apple"

    # db.users.find( { favoriteFruit: "apple" }, { _id: 1, name: 1, address: 1, registered: 1, favoriteFruit: 1 }).sort({"registered"  : 1}).limit(1)

    # response:

    # { "_id" : ObjectId("5adf3c1544abaca147cdd568"), "name" : "Magdalena Compton", "address" : "742 Paerdegat Avenue, Oceola, Northern Mariana Islands, 1793", "registered" : "2014-01-02T10:16:56 -02:00", "favoriteFruit" : "apple" }


# 7) Добавить этому пользовелю свойтво: { features: 'first apple eater' } (1)

    # request:
    objectID = ObjectId("5adf3c1544abaca147cdd568")

    # db.users.update({_id:  ObjectId("5adf3c1544abaca147cdd568")}, { $set: {features: "first apple eater"}})

    # response: 

    # WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })


    # check: 

      # request:

      # db.users.find( { favoriteFruit: "apple" }, { _id: 1, name: 1, address: 1, registered: 1, favoriteFruit: 1, features: 1 }).sort({"registered" : 1}).limit(1)

      # response:

      # { "_id" : ObjectId("5adf3c1544abaca147cdd3d5"), "name" : "Randall Conner", "address" : "167 Berkeley Place, Nogal, Washington, 4955", "registered" : "2014-01-02T02:39:51 -02:00", "favoriteFruit" : "strawberry", "features" : "first apple eater" }


  # 8) Удалить всех любителей клубники (написать количество удаленных пользователей) (1)

    # request
    
    # db.users.remove({favoriteFruit: "strawberry"})

    # response:
  
    # WriteResult({ "nRemoved" : 253 })
