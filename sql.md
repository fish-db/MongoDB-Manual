## sql

### 1 插入操作

1.1 向user集合中插入两条记录
> db.user.insert({'name':'Gal Gadot','gender':'female','age':28,'salary':11000})

> db.user.insert({'name':'Mikie Hara','gender':'female','age':26,'salary':7000})

1.2 同样也可以用save完成类似的插入操作
> db.user.save({'name':'Wentworth Earl Miller','gender':'male','age':41,'salary':33000})

### 2 查找操作

2.1 查找集合中的所有记录

> db.user.find()

2.2 查找集合中的符合条件的记录

(1)单一条件

- Exact Equal:
  查询age为了23的数据
  > db.user.find({"age":23})

- Great Than:
  查询salary大于5000的数据
  > db.user.find({salary:{$gt:5000}})

- Fuzzy Match
  查询name中包含'a'的数据
  > db.user.find({name:/a/})

- 查询name以G打头的数据
  > db.user.find({name:/^G/})


(2)多条件"与"
查询age小于30，salary大于6000的数据
> db.user.find({age:{$lt:30},salary:{$gt:6000}})

(3)多条件"或"
查询age小于25，或者salary大于10000的记录
> db.user.find({$or:[{salary:{$gt:10000}},{age:{$lt:25}}]})

(4)不等于查询
查询年龄不等于23的记录(这里返回结果中，会包含没有年龄字段的记录)
> db.user.find({"age":{ $ne: 23}})

(5)利用正则表达式的查询
查询名字中含有字母E的记录(i表示忽略大小写)
> db.user.find({name:/E/i})
也可以用：
> db.user.find({name:{$regex:'E',$options:'i'}})

查询名字中含有字母E的记录(默认区分大小写)
> db.user.find({name:/E/})
等价于：
> db.user.find({name:{$regex:'E'}})

查询某个字段以“.0”结尾的记录
db.user.find(name:/\.0$/)
这里的"//"中的内容表示是正则表达式，"."需要转义，"$"符号表示结尾。
2.3 查询第一条记录
将上面的find替换为findOne()可以查找符合条件的第一条记录。
将上面的find替换为findOne()可以查找符合条件的第一条记录。
> db.user.findOne({$or:[{salary:{$gt:10000}},{age:{$lt:25}}]})

2.4 查询记录的指定字段
查询user集合中所有记录的name,age,salary,sex_orientation字段
> db.user.find({},{name:1,age:1,salary:1,sex_orientation:true})

注意：这里的1表示显示此列的意思，也可以用true表示。
可以看到，默认_id，字段都是显示的。如果要其不显示，只需将其显示指定为false:
> db.user.find({},{name:1,age:1,salary:1,sex_orientation:true,_id:false})
2.5 查询指定字段的数据，并去重。
查询gender字段的数据，并去掉重复数据
> db.user.distinct('gender')
[ "male", "female" ]
2.6 对查询结果集的操作
(1)Pretty Print
为了方便，mongo也提供了pretty print工具，db.collection.pretty()或者是db.collection.forEach(printjson)
> db.user.find().pretty()

(2)指定结果集显示的条目
- a)显示结果集中的前3条记录
  > db.user.find().limit(3)

- b)查询第1条以后的所有数据
  > db.user.find().skip(1)

- c)对结果集排序
  升序
  > db.user.find().sort({salary:1})

  降序
  > db.user.find().sort({salary:-1})


也可以将排序依据的字段，写在一个list里面，如下：
> db.user.find().sort([("salary",1),("name",-1)])

2.7 统计查询结果中记录的条数
(1)统计集合中的所有记录条数
> db.user.find().count()

(2)查询符合条件的记录数
查询salary小于4000或大于10000的记录数
> db.user.find({$or: [{salary: {$lt:4000}}, {salary: {$gt:10000}}]}).count()
4

2.8 查询存在(或不存在)指定字段的记录

查询不存在age字段，但是有gender字段，并且ex为barrymore的记录。

> db.user.find({"age":{$exists:false},"gender":{$exists:true},"ex":"barrymore"})

### 3 删除操作

3.1 删除整个集合中的所有数据

> db.test.insert({name:"asdf"})
> show collections
book
system.indexes
test
user
到这里新建了一个集合，名为test。
删除test中的所有记录。
> db.test.remove()
PRIMARY> show collections
book
system.indexes
test
user
> db.test.find()
可见test中的记录全部被删除。
注意db.collection.remove()和drop()的区别，remove()只是删除了集合中所有的记录，而集合中原有的索引等信息还在，而drop()则把集合相关信息整个删除(包括索引)。

3.2 删除集合中符合条件的所有记录
> db.user.remove({name:'lfqy'})
> db.user.find()


> db.user.find()

> db.user.remove( {salary :{$lt:10}})
> db.user.find()

3.3  删除集合中符合条件的一条记录
> db.user.find()

> db.user.remove({salary :{$lt:10}},1)
> db.user.find()

当然，也可以是db.user.remove({salary :{$lt:10}},true)
4 更新操作
4.1 赋值更新
db.collection.update(criteria, objNew, upsert, multi )
criteria:update的查询条件，类似sql update查询内where后面的
objNew:update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的。
upsert : 如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
multi : mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。


> db.user.update({name:'lfqy'},{$set:{age:23}},false,true)

> db.user.update({name:'lfqy1'},{$set:{age:23}},true,true)

4.2 增值更新
> db.user.find()

> db.user.update({gender:'female'},{$inc:{salary:50}},false,true)



https://blog.csdn.net/xia7139/article/details/12570569 
