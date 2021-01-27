# 데이터베이스 모델링

	* 데이터베이스 모델링 또는 데이터 모델링은 현 세계에서 사용하는 작업이나 사물을 DBMS의 데이터베이스 개체로 옮기기 위한 과정이다. 현실 세계의 개체는 데이터베이스 내 각 테이블이라는 개체로 변환된다.
	* 구현하고자 하는 업무에 대해 폭넓고 정확한 지식이 필요하며 데이터베이스 시스템에 대한 깊은 지식과 경험도 갖춰야 한다. 따라서 모델링을 담당하는 사람은 수많은 프로젝트 경험과 데이터베이스 관련 지식을 지녀야 하는 것이 일반적이다.
 * 두 테이블의 업무적 연관성을 관계라고 하며 부모 테이블과 자식 테이블을 구분하는 방법은 주가 되는 쪽이 부모로, 상세가 되는 쪽이 자식으로 분류하는 것이다.
   	* 주로 기준이 하나인 것과 이 기준이 여러 개의 기록을 남기는 것으로 부모 테이블과 자식 테이블을 구분할 수 있다.
      	* 부모테이블과 자식테이블의 관계를 맺어주는 역할은 기본 키와 외래 키의 설정으로 이루어진다.



```mariadb
# 모범답안
DROP TABLE IF EXISTS cart;
DROP TABLE IF EXISTS product;
DROP TABLE IF EXISTS member;
DROP TABLE IF EXISTS order_detail;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS review;

CREATE TABLE IF NOT EXISTS review
(
	mem_id NVARCHAR(30),
	p_id NVARCHAR(10),
	review_detail NVARCHAR(200),
	review_rating INT(5),
	PRIMARY KEY(mem_id, p_id)
);

CREATE TABLE IF NOT EXISTS cart
(
	cart_id INT(10) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	cart_userid NVARCHAR(30),
	cart_itemid NVARCHAR(10),
	cart_amount INT(10)
);

CREATE TABLE IF NOT EXISTS product
(
	p_id NVARCHAR(10) NOT NULL PRIMARY KEY,
	p_category_id INT(5) NOT NULL,
	p_stock INT(10),
	p_name NVARCHAR(30),
	p_price INT(10),
	p_detail NVARCHAR(500),
	p_image_path NVARCHAR(100),
	p_date DATE,
	p_salescount INT(10)
);

CREATE TABLE IF NOT EXISTS member
(
	mem_id NVARCHAR(30) NOT NULL PRIMARY KEY,
	mem_pwd NVARCHAR(30) NOT NULL,
	mem_name NVARCHAR(10) NOT NULL,
	mem_addr NVARCHAR(50),
	mem_phone NVARCHAR(20),
	mem_email NVARCHAR(20),
	mem_joindate DATE,
	mem_status INT(1)
);

CREATE TABLE IF NOT EXISTS orders
(
	order_id NVARCHAR(20) NOT NULL PRIMARY KEY,
	order_mem_id NVARCHAR(30) NOT NULL,
	order_recv_name NVARCHAR(10) NOT NULL,
	order_recv_addr NVARCHAR(50) NOT NULL,
	order_total_price INT(10) NOT NULL,
	order_date DATE,
	order_status INT(1),
	order_msg NVARCHAR(100)
);

CREATE TABLE IF NOT EXISTS order_detail
(
	detail_id NVARCHAR(10) NOT NULL PRIMARY KEY,
	detail_p_id NVARCHAR(10),
	detail_order_id NVARCHAR(20),
	detail_amount INT(5)
);


ALTER TABLE review ADD CONSTRAINT id_fk_product FOREIGN KEY (p_id) REFERENCES product(p_id);
ALTER TABLE review ADD CONSTRAINT id_fk_member FOREIGN KEY (mem_id) REFERENCES member(mem_id);
ALTER TABLE order_detail ADD CONSTRAINT item_fk_detail FOREIGN KEY (detail_p_id) REFERENCES product(p_id);
ALTER TABLE order_detail ADD CONSTRAINT order_fk_detail FOREIGN KEY (detail_order_id) REFERENCES orders(order_id);
ALTER TABLE cart ADD CONSTRAINT user_fk_cart FOREIGN KEY (cart_userid) REFERENCES member(mem_id);
ALTER TABLE cart ADD CONSTRAINT item_fk_cart FOREIGN KEY (cart_itemid) REFERENCES product(p_id);
ALTER TABLE orders ADD CONSTRAINT user_fk_order FOREIGN KEY (order_mem_id) REFERENCES member(mem_id);

INSERT INTO product VALUES('p01',0,100,'T-shirt',10000,'100% 면','C:\Users\sohn0\Desktop\blog\id01',CURRENT_DATE,0);
INSERT INTO product VALUES('p02',0,100,'skirt',15000,'100% 캐시미어','C:\Users\sohn0\Desktop\blog\id02',CURRENT_DATE,0);
INSERT INTO product VALUES('p03',0,100,'sweater',5000,'100% 털','C:\Users\sohn0\Desktop\blog\id03',CURRENT_DATE,0);

INSERT INTO member VALUES('user01','1234','name01','addr1','010-0000-0001','user01@gmail.com',CURRENT_DATE,0);
INSERT INTO member VALUES('user02','1234','name02','addr2','010-0000-0002','user02@gmail.com',CURRENT_DATE,0);
INSERT INTO member VALUES('user03','1234','name03','addr3','010-0000-0003','user03@gmail.com',CURRENT_DATE,0);

SELECT * FROM product;
SELECT * FROM member;

INSERT INTO cart VALUES(NULL, 'user01','p02',5);
INSERT INTO cart VALUES(NULL, 'user02','p01',5);

SELECT * FROM cart;

INSERT INTO review VALUES('user01', 'p01', 'Good',5);
INSERT INTO review VALUES('user02', 'p01', 'Bad',1);

SELECT * FROM review;

# INSERT INTO review VALUES('user01', 'p01', 'Good',1);

INSERT INTO orders VALUES('order01','user01','name01','add1',10000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order02','user01','name01','add1',15000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order03','user02','name01','add1',40000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order04','user03','name01','add1',12000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order05','user02','name01','add1',1000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order06','user01','name01','add1',12000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order07','user02','name01','add1',20000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order08','user02','name01','add1',30000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order09','user03','name01','add1',11000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order10','user01','name01','add1',20000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order11','user02','name01','add1',14000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order12','user03','name01','add1',15000,CURRENT_DATE,NULL,NULL);
INSERT INTO orders VALUES('order13','user02','name01','add1',13000,CURRENT_DATE,NULL,NULL);
SELECT * FROM orders;

INSERT INTO order_detail VALUES('detail01','p01','order01',10);
SELECT * FROM order_detail;

SELECT * FROM orders;
SELECT order_mem_id AS mem_id, SUM(order_total_price) FROM orders GROUP BY order_mem_id ORDER BY SUM(order_total_price);

SELECT * FROM orders AS order_list JOIN
(SELECT order_mem_id AS mem_id, SUM(order_total_price) FROM orders GROUP BY order_mem_id ORDER BY SUM(order_total_price) DESC LIMIT 1) AS sum_of_pay
ON order_list.order_mem_id = sum_of_pay.mem_id;
```

![image-20210119010748156](C:%5CUsers%5Chand1%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210119010748156.png)

| 부모 테이블 | 자식 테이블  |
| :---------: | :----------: |
|   product   |     cart     |
|   member    |    review    |
|             |    orders    |
|             | order_detail |



```mariadb
DROP TABLE IF EXISTS users;
DROP TABLE IF EXISTS product;
DROP TABLE IF EXISTS product_c;
DROP TABLE IF EXISTS cart;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS orders_detail;
DROP TABLE IF EXISTS favorite;
DROP TABLE IF EXISTS review;
DROP TABLE IF EXISTS qna;
DROP TABLE IF EXISTS coupon;
DROP TABLE IF EXISTS coupon_detail;

#테이블 생성
CREATE TABLE IF NOT EXISTS users(
   u_id NVARCHAR(10) PRIMARY KEY,
   u_pwd NVARCHAR(10),
   u_name NVARCHAR(20),
   u_age INT(10),
   u_addr NVARCHAR(20),
   u_mail NVARCHAR(20)
);

CREATE TABLE IF NOT EXISTS product(
   p_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   pc_id INT(10),
   p_name NVARCHAR(10),
   p_price INT(10),
   p_regdate DATE,
   p_qt INT(10),
   p_detail NVARCHAR(500),
   p_image_path NVARCHAR(100),
   p_option NVARCHAR(20)
);

CREATE TABLE IF NOT EXISTS product_c(
   pc_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   pc_name NVARCHAR(10),
   ppc_id INT(10)
);

CREATE TABLE IF NOT EXISTS cart(
   c_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   u_id NVARCHAR(10),
   p_id INT(10),
   c_qt INT(10),
   c_regdate DATE
);

CREATE TABLE IF NOT EXISTS orders(
   o_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   u_id NVARCHAR(10),
   o_name NVARCHAR(10),
   o_addr NVARCHAR(20),
   o_message NVARCHAR(100),
   o_cancel INT(1),
   o_regdate DATE,
   o_discount INT(10),
   o_allprice INT(10)
);

CREATE TABLE orders_detail(
   od_id INT(10) AUTO_INCREMENT UNIQUE, 
   p_id INT(10) NOT NULL,
   o_id INT(10) NOT NULL,
   od_qt INT(10) NOT NULL
);

CREATE TABLE IF NOT EXISTS favorite(
   f_id INT(10) AUTO_INCREMENT UNIQUE,
   u_id NVARCHAR(10),
   p_id INT(10)
);

CREATE TABLE IF NOT EXISTS review(
   r_id INT(10) AUTO_INCREMENT UNIQUE,
   u_id NVARCHAR(10),
   p_id INT(10),
   r_num INT(10),
   r_review NVARCHAR(100)
);

CREATE TABLE IF NOT EXISTS qna(
   q_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   u_id NVARCHAR(10),
   p_id INT(10),
   q_detail NVARCHAR(200),
   q_public INT(1)
);

CREATE TABLE IF NOT EXISTS coupon_detail(
   cpd_id INT(10) AUTO_INCREMENT UNIQUE,
   cp_id INT(10),
   p_id INT(10)
);

CREATE TABLE IF NOT EXISTS coupon(
   cp_id INT(10) AUTO_INCREMENT PRIMARY KEY,
   cp_name VARCHAR(20),
   cp_count INT(10)
);

#FK 제약조건 설정
ALTER TABLE product
ADD CONSTRAINT FK_product_c_p FOREIGN KEY(pc_id) REFERENCES product_c(pc_id);

ALTER TABLE product_c
ADD CONSTRAINT FK_product_c_p_c FOREIGN KEY(ppc_id) REFERENCES product_c(pc_id);

ALTER TABLE cart
ADD CONSTRAINT FK_user_c FOREIGN KEY(u_id) REFERENCES users(u_id),
ADD CONSTRAINT FK_product_c FOREIGN KEY(p_id) REFERENCES product(p_id);

ALTER TABLE orders
ADD CONSTRAINT FK_users_o FOREIGN KEY(u_id) REFERENCES users(u_id);

ALTER TABLE orders_detail
ADD CONSTRAINT FK_product_od FOREIGN KEY (p_id) REFERENCES product(p_id),
ADD CONSTRAINT FK_orders_od FOREIGN KEY (o_id) REFERENCES orders(o_id);

ALTER TABLE favorite
ADD CONSTRAINT FK_users_f FOREIGN KEY(u_id) REFERENCES users(u_id),
ADD CONSTRAINT FK_product_f FOREIGN KEY(p_id) REFERENCES product(p_id);

ALTER TABLE review
ADD CONSTRAINT FK_users_review FOREIGN KEY (u_id) REFERENCES users(u_id),
ADD CONSTRAINT FK_product_review FOREIGN KEY (p_id) REFERENCES product(p_id);

ALTER TABLE qna
ADD CONSTRAINT FK_users_qna FOREIGN KEY (u_id) REFERENCES users(u_id),
ADD CONSTRAINT FK_product_qna FOREIGN KEY (p_id) REFERENCES product(p_id);

ALTER TABLE coupon_detail
ADD CONSTRAINT FK_coupon_cd FOREIGN KEY (cp_id) REFERENCES coupon(cp_id),
ADD CONSTRAINT FK_product_cd FOREIGN KEY (p_id) REFERENCES product(p_id);

#PK 묶어서 지정
ALTER TABLE orders_detail ADD CONSTRAINT PK_orders_detail PRIMARY KEY(p_id,o_id);
ALTER TABLE favorite ADD CONSTRAINT PK_favorite PRIMARY KEY(u_id,p_id);
ALTER TABLE review ADD CONSTRAINT PK_review PRIMARY KEY(u_id,p_id);
ALTER TABLE coupon_detail ADD CONSTRAINT PK_coupon_detail PRIMARY KEY(cp_id,p_id);

#DB생성
# 1) users
INSERT INTO users VALUES('user01','123','한말숙',25,'서울','123ha@naver.com');
INSERT INTO users VALUES('user02','123','김말숙',26,'부산','123ba@daum.com');
INSERT INTO users VALUES('user03','123','박말숙',27,'대구','123ha@naver.com');
INSERT INTO users VALUES('user04','123','송말숙',28,'대전','123ha@naver.com');
INSERT INTO users VALUES('user05','123','공말숙',29,'평택','123ha@naver.com');

# 2) product_c
INSERT INTO product_c VALUES(pc_id,'의류',NULL);
INSERT INTO product_c VALUES(pc_id,'상의',1);
INSERT INTO product_c VALUES(pc_id,'겉옷',1);
INSERT INTO product_c VALUES(pc_id,'하의',1);
INSERT INTO product_c VALUES(pc_id,'악세사리',1);

# 3) product
INSERT INTO product VALUES(p_id,2,'니트',1000,CURRENT_DATE,10,'이상품은 어쩌고저쩌고','C:\Users\USER-PC\Documents\image1','베이지');
INSERT INTO product VALUES(p_id,2,'셔츠',2000,CURRENT_DATE,9,'이상품은 어쩌고저쩌고','C:\Users\USER-PC\Documents\image2','베이지');
INSERT INTO product VALUES(p_id,3,'코트',3000,CURRENT_DATE,11,'이상품은 어쩌고저쩌고','C:\Users\USER-PC\Documents\image3','파랑');
INSERT INTO product VALUES(p_id,5,'양말',4000,CURRENT_DATE,2,'이상품은 어쩌고저쩌고','C:\Users\USER-PC\Documents\image4','빨강');
INSERT INTO product VALUES(p_id,4,'청바지',5000,CURRENT_DATE,6,'이상품은 어쩌고저쩌고','C:\Users\USER-PC\Documents\image5','검정');

# 4) orders
INSERT INTO orders VALUES(o_id,'user01','김동자','서울','문 앞에 놔주세요',0,CURRENT_DATE,'10000','100000');
INSERT INTO orders VALUES(o_id,'user01','김동자','서울','문 앞에 놔주세요',0,CURRENT_DATE,'10000','100000');
INSERT INTO orders VALUES(o_id,'user01','김동자','서울','문 앞에 놔주세요',0,CURRENT_DATE,'10000','100000');
INSERT INTO orders VALUES(o_id,'user01','김동자','서울','문 앞에 놔주세요',0,CURRENT_DATE,'10000','100000');
INSERT INTO orders VALUES(o_id,'user01','김동자','서울','문 앞에 놔주세요',0,CURRENT_DATE,'10000','100000');

# 5) orders_detail
INSERT INTO orders_detail VALUES(od_id,1,2,10);
INSERT INTO orders_detail VALUES(od_id,2,2,20);
INSERT INTO orders_detail VALUES(od_id,5,2,30);
INSERT INTO orders_detail VALUES(od_id,3,2,40);
INSERT INTO orders_detail VALUES(od_id,4,2,50);

# 6) cart
INSERT INTO cart VALUES(c_id,'user01',1,2,CURRENT_DATE);
INSERT INTO cart VALUES(c_id,'user02',2,1,CURRENT_DATE);
INSERT INTO cart VALUES(c_id,'user03',2,3,CURRENT_DATE);
INSERT INTO cart VALUES(c_id,'user02',3,1,CURRENT_DATE);
INSERT INTO cart VALUES(c_id,'user05',1,2,CURRENT_DATE);

# 7) favorite
INSERT INTO favorite VALUES(f_id,'user05',1);
INSERT INTO favorite VALUES(f_id,'user05',2);
INSERT INTO favorite VALUES(f_id,'user04',2);
INSERT INTO favorite VALUES(f_id,'user03',1);
INSERT INTO favorite VALUES(f_id,'user02',1);

# 8) review
INSERT INTO review VALUES(r_id,'user02',1,10,'재질이 좋아요');
INSERT INTO review VALUES(r_id,'user01',1,8,'재질이 좋아요');
INSERT INTO review VALUES(r_id,'user03',1,10,'재질이 좋아요');
INSERT INTO review VALUES(r_id,'user04',1,9,'재질이 좋아요');
INSERT INTO review VALUES(r_id,'user01',2,10,'디자인이 좋아요');

# 9) qna
INSERT INTO qna VALUES(q_id,'user02',1,'재질이 어떻게 이루어져 있나요?',1);
INSERT INTO qna VALUES(q_id,'user03',1,'배송은 언제쯤 오나요?',1);
INSERT INTO qna VALUES(q_id,'user02',3,'배송은 언제쯤 오나요?',1);
INSERT INTO qna VALUES(q_id,'user04',1,'배송은 언제쯤 오나요?',1);
INSERT INTO qna VALUES(q_id,'user05',1,'배송은 언제쯤 오나요?',1);

# 10) coupon
INSERT INTO coupon VALUES(cp_id,'1000원 할인권',1000);
INSERT INTO coupon VALUES(cp_id,'2000원 할인권',2000);
INSERT INTO coupon VALUES(cp_id,'3000원 할인권',3000);
INSERT INTO coupon VALUES(cp_id,'4000원 할인권',4000);
INSERT INTO coupon VALUES(cp_id,'5000원 할인권',5000);

# 11) coupon_detail
INSERT INTO coupon_detail VALUES(cpd_id,1,1);
INSERT INTO coupon_detail VALUES(cpd_id,1,2);
INSERT INTO coupon_detail VALUES(cpd_id,3,3);
INSERT INTO coupon_detail VALUES(cpd_id,4,4);
INSERT INTO coupon_detail VALUES(cpd_id,5,5)
```

![image-20210119012605369](C:%5CUsers%5Chand1%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210119012605369.png)

| 부모 테이블 |  자식 테이블  |
| :---------: | :-----------: |
|    users    |     cart      |
|  product_c  |    review     |
|   coupon    |   favorite    |
|             |      qna      |
|             |    orders     |
|             | orders_detail |
|             | coupon_detail |

