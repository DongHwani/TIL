# MySQL 사용자 권한 명렁어

### 1. 권한 조회

 현재 접속 계정 권한 조회 
~~~CS
SHOW GRANTS FOR CURRENT_USER;
~~~

 사용자 권한 조회
~~~CS
SHOW GRANTS FOR '사용자계정'@'호스트'
~~~

### 2. 권한 부여
~~~CS
//GRANT ALL PRIVILEGES ON {DB}.* TO '사용자계정'@'접근 허용 대상'
GRANT ALL PRIVILEGES ON springdb.* TO 'dhk'@'localhost';                            
GRANT ALL PRIVILEGES ON springdb.* TO 'dhk'@'%';
~~~


### ETC. 사용자 생성 
~~~CS
//CREATE USER {계정이름}@{접근대상} IDENTIFIED BY {비밀번호};
CREATE USER 'dhk'@'%' IDENTIFIED BY 'dhk';
CREATE USER 'dhk'@'localhost' IDENTIFIED BY 'dhk';
~~~
'%' : 어떤 클라이언트든 접근 가능 

'localhost' : 해당 클라이언트만 접근 가능
