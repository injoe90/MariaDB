```python
import pymysql;

config = {
    'user':'staff2',
    'password':'staff2',
    'host':'127.0.0.1',
    'database':'shop1',
    'port':'3306'
};

conn = pymysql.connect(**config);
print(conn);

cursor = conn.cursor();
sql = 'SELCT * FROM users';

cursor.execute(sql);
result = cursor.fetchall();

print(result);

for u in result:
    print('%s, %s, %s' % (u[0],u[1],u[2]));

cursor.close();
conn.close();
```



```python
import pymysql;

config = {
    'user':'director',
    'password':'111111',
    'host':'127.0.0.1',
    'database':'shop1',
    'port':'3306'
};

conn = pymysql.connect(**config);
print(conn);

cursor = conn.cursor();
sql = 'SELCT * FROM users';

cursor.execute(sql);
result = cursor.fetchall();

print(result);

for u in result:
    print('%s, %s, %s' % (u[0],u[1],u[2]));

cursor.close();
conn.close();
```

