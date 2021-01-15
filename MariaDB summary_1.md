



```mariadb
DROP TABLE item;

CREATE TABLE item (
	id INT AUTO_INCREMENT PRIMARY KEY,
	name NVARCHAR(10) NOT NULL,
	code CHAR(5) UNIQUE,
	price INT,
	regdate DATE
);


ALTER TABLE item AUTO_INCREMENT = 1000;
INSERT INTO item VALUES (NULL,'상품이름은바지입니다',11115,1000,CURRENT_DATE);
INSERT INTO item VALUES (NULL,'aaaaaaaaaa',11129,1000,CURRENT_DATE);
SELECT * FROM item;
```





```mariadb
CREATE TABLE IF NOT EXISTS users(
	id CHAR(4),
	pwd NVARCHAR(10),
	NAME NVARCHAR(20)
);
ALTER TABLE users ADD CONSTRAINT uers_pk PRIMARY KEY (id);
CREATE TABLE IF NOT EXISTS items(
	id INT(10) AUTO_INCREMENT PRIMARY KEY,
	name NVARCHAR(20),
	price INT(5),
	regdate DATE
);

ALTER TABLE items AUTO_INCREMENT = 1000;

SELECT * FROM users;
SELECT * FROM items;

INSERT INTO items VALUES (id, 'item05',10000,CURRENT_DATE);

INSERT INTO users (id,name) VALUES ('id01','james');
INSERT INTO users VALUES('id02','pwd02','kim');

# DELETE FROM items; 해당 테이블에 있는 모든 정보가 삭제 / 복구도 할 수 없다
DELETE FROM items WHERE id  = 1001;

# UPDATE items SET name = 'item05'; 해당 테이블에 있는 모든 name 변수가 item05로 바뀐다
UPDATE items SET name = 'item06', price = 15000, regdate = '20201018' WHERE id = 1002;
```





```mariadb
DROP TABLE IF EXISTS product;
CREATE TABLE IF NOT EXISTS product(
	Pcoid CHAR(3) NOT NULL,
	Pserial CHAR(4) NOT NULL,
	pname NVARCHAR(10) NOT NULL,
	pprice INT(10) NOT NULL,
	qt INT(10) NOT NULL
);

ALTER TABLE product ADD CONSTRAINT mypk PRIMARY KEY (Pcoid,Pserial);
ALTER TABLE product ALTER COLUMN pprice SET DEFAULT 10000;
ALTER TABLE product ADD CONSTRAINT mycheck CHECK (qt > 0);
ALTER TABLE product ADD CONSTRAINT myunique UNIQUE (pname);

INSERT INTO product VALUES ('111','1111','p1',pprice);

SELECT * FROM product;

ALTER TABLE product DROP CONSTRAINT mycheck;  # 제약조건을 제거users
```

