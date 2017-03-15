# pyco_mongo

Usage: Via pyco_mongo.MongoMixin, we can use MongoDB in someway like relative database.

##### 用法：通过pyco_mongo.Mongoxin,  用MongoDB存储关系式数据。

```
 pyco_mongo , which encapsulates MongoMixin by pymongo, 
 make use of MongoDB in someway like relative database
``` 


0. ready
 ```
    python3
    pymongo==3.4.0
```

1. install pyco_mongo 
```
    pip3 install pyco_mongo   
    easy_install pyco_mongo
```

2. default settings of MongoDB 

```python
# pyco_mongo 通过环境变量来配置相关 MongoDB 的服务
 
import os
 
env = os.environ.get
  
mongodb_uri = env('MONGODB_URI', "mongodb://localhost:27017")
mongodb_name = env('MONGODB_NAME', "test")
mongodb_col_seq = env('MONGODB_COL_SEQ', "_seq_coll")
 
# MONGODB_URI, (db_uri), 存储数据的 MongoDB 的服务器地址 
# MONGODB_NAME， (db_name), 存储数据的 MongoDB 的数据库名称 
# MONGODB_COL_SEQ, (不推荐更改!!!)，pyco_mongo 用于存储所有MongoMixin子类的索引id最大值.
 
 ```

3. custom settings 
```python
# 自定义 MongoDB 配置
# pyco_mongo 通过环境变量来配置相关 MongoDB 的服务
# 注意， 不推荐更改默认的 MONGODB_COL_SEQ
# !! Be careful, update the default value of MONGODB_COL_SEQ is not recommended.
import os
 
os.environ.setdefault('MONGODB_URI','your_mongodb_uri')
os.environ.setdefault('MONGODB_NAME','your_mongodb_name')

```

4. CRUD MongoMixin, eg
```python
 
from pyco_mongo import MongoMixin
 
 
#0. 定义数据集 collection_name = 'User'
class User(MongoMixin):
   __fields__ = [
        ('username', str, ''),
        ('nickname', str, ''),
        ('email', str, ''),
        ('address', str, ''),
   ] 
   
   __frozen_keys__ = [
        'username',
   ]
   # keys in __frozen_keys__ can't be update softly.
 
 
#1. create 
u = User.new(
    username='Jane',
    nickname='Jay', 
    email='test@email.com', 
    address='China',
)
 
 
#2. get
u1 = User.get(id=u.id)
u2 = User.get(1)
 
 
#3. update 
u.update(nickname='Van') # success
u.update(username='Van') # fail, username is frozen
 
 
#4. delete
u.delete()
# assert u.deleted == True
 
 
#5. recover deleted object
u.recover()
# assert u.deleted == False 
  
  
#6. find_one
u3 = User.find_one(id=1)
u4 = User.find_one(username='Jane', address='China')
 
 
#7. find all
us = User.find()
ua = User.find(address='China')
 
 
#8. to dict
ud = u.to_dict()
  
  
#9. to json 
uj = u.json()
assert uj['_type'] == 'User'
 
 
#10, __eq__
uq1 = User.new(username='uq')
uq2 = User.new(username='uq')
uqx = User.get(uq1.id)
# assert uq1 != uq2
# assert uq1 == uqx
 
# 其他
 
  
#11. upsert
um = User.upsert({'username':'uq'}, {'email': 'a@bb.cc'})
ux = User.upsert({'username':'xxx'}, {'email','new@bb.cc'})
 
 
#12. paging
ups = User.paging(limit=3)
assert len(ups) <= 3
 
  
#13. count
total = User.count()
count = User.count(deleted=False)

```

