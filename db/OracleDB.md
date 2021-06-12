# 오라클

- 스키마 : 연관 있는 테이블(표)을 모아놓은 디렉토리로 본질적인 의미는 스키마에 속한 표들을 정의하는 것
- 사용자를 생성하면 그 사용자에 해당되는 스키마가 만들어진다. 

- 사용자 및 스키마 생성

```sql
--cmd 창키고
sqlplus
sys AS SYSDBA --오라클에서 기본적으로 제공하는 사용자로서 시스템데이터베이스어드미니스터(시스템 테이터 관리자)
--passward 비밀번호 없음
--oracle sql reference create user -- 구글에 검색하면 문법이 나온다
CREATE USER maeng IDENTIFIED BY 1111; --사용자 및 스키마 생성
exit --나가기

sqlplus
maeng -- 권한이 필요하다

```

- 사용자 권한 부여

```sql
sqlplus
GRANT DBA TO maeng; --모든 일을 할수있는 권한을 maeng에게 주겠다.
exit
sqlplus
user-name: maeng
password: 1111
```

- relational 테이블생성

  ![image-20210415223310398](https://user-images.githubusercontent.com/80496345/116241060-7638ba80-a79f-11eb-8176-b5e951c8484a.png)

```sql
CREATE TABLE topic(
	id NUMBER NOT NULL,--NUMBER은 데이터 타입으로 반드시 숫자여야하고, NOT NULL은 값이 무조건 있어야 한다는 뜻
    title VARCHAR2(50) NOT NULL,--VARCHAR2는 가변적인 글자를 정할수있고 괄호안에 글자수.
    description VARCHAR2(4000) NOT NULL, --(4000)글자
    created DATE NOT NULL--오라클은 날짜 및 시간을 위한 데이터 타입이 있다.
);
--테이블 출력?
SELECT table_name FROM all_tables WHERE OWNER = 'MAENG';

CREATE TABLE topic(
	id NUMBER NOT NULL,
    title VARCHAR2(50) NOT NULL,
    description VARCHAR2(4000) NOT NULL,
    created DATE NOT NULL
    nation NCHAR(4) DEFAULT '대한민국' -- INSERT사용시 별도의 값을 입력하지 않고 지정해놓은 디폴트값을 그대로 사용할려면 디폴트 쓰면된다.
);
```



## SQL이란?

- 데이터베이스는 엄청 난 양의 데이터를 저장하고 쓸 수 있다.
- 명령어를 통해서 데이터베이스를 제어할 수 있다. --> 자동화 할 수 있다.
- 이러한 명령어를 SQL이라고 한다.
- Structured Query Language 구조화 된 정보(엑셀과같은)를 처리하도록 요청하는 컴퓨터 언어
- 관계형 데이터베이스는 SQL을 사용한다.



## CRUD

- 행 추가

![image-20210415224057446](https://user-images.githubusercontent.com/80496345/116240983-6a4cf880-a79f-11eb-8463-a8f07fc93b2a.png)

```sql
INSERT INTO topic--테이블명
	(id,title,description,created)--속성순서 다넣을꺼면  안적어도됌?
	VALUES(--속송의 순서에 따른 값
        	1,
        	'ORACLE',
        	'ORACLE is...',
        	SYSDATE
    );
INSERT INTO topic
	(id,title,description,created)
	VALUES(
        	2,
        	'MySQL',
        	'MySQL is...',
        	SYSDATE
    );
INSERT INTO topic
	(id,title,description,created)
	VALUES(
        	3,
        	'SQL Server',
        	'SQL Server is...',
        	SYSDATE
    );
commit; --추가하고 무조건 커밋해야한다.
```

- 행 읽기

  - 모든 행과 모든 열을 가져오기

    ```sql
    SELECT * FROM topic;
    ```

  - 자기가 원하는 열을 가져오기

    ```sql
    SELECT id,title,created FROM topic;
    ```

  - 자기가 원하는 행을 가져오기

    ```sql
    SELECT * FROM topic WHERE id = 1;
    SELECT * FROM topic WHERE id > 1;
    ```

  - 자기가 원하는 행과 열 가져오기

    ```sql
    SELECT id,title,created FROM topic WHERE id = 1;
    ```

- 행 읽기 -정렬과 페이징

  - 정렬

    ```sql
    SELECT * FROM topic ORDER BY id DESC: --id를 기준으로 정렬하는데 큰숫자가 먼저 나온다.
    SELECT * FROM topic ORDER BY id ASC: --id를 기준으로 정렬하는데 작은숫자가 먼저 나온다.
    SELECT * FROM topic ORDER BY title ASC: --title을 기준
    ```

  - 페이징 : 양이 너무 많다고 하면 페이지를 만드는 것 처럼 데이터를 나누는 방법

    ```sql
    SELECT * FROM topic
    		OFFSET 1 ROWS --0번째(컴퓨터언어니까) 이후에 있는 행들만 가져온다 2번 3번행이 나옴
    		FETCH NEXT 1 ROWS only; --하나만 가져오겠다 2번행 나옴
    		FETCH NEXT 1 ROWS only; --두개를 가져오겠다 2,3번행 나옴
    ```

- 행 수정

  ```sql
  UPDATE topic
  		SET
  		 title = 'MSSQL',
  		 descrition = 'MSSQL is...'
  		WHERE
  		 id = 3;
  
  UPDATE buyTBL SET price = price * 1.5; --구매 테이블의 현재 단가를 ahen 1.5배 인상
  ```

-  행 삭제

  ```sql
  DELETE FROM topic WHERE id = 3;
  
  DELETE FROM testTBL4 WHERE FirstName = 'peter' AND ROWNUM <= 2 --피터중에서 중상위 2건만 삭제
  ```

- PRIMARY KEY : 프라이머키가 지정된 값을 가진 행은 중복된 값을 막아주고 지정된 행의 데이터를 찾을 시 훨씬 빨리 찾아 준다. NULL값이 있을 수 없으므로 NOT NULL 적을 필요없다.

  ```sql
  CREATE TABLE topic(
  	id NUMBER NOT NULL,
      title VARCHAR2(50) NOT NULL,
      description VARCHAR2(4000) NOT NULL,
      created DATE NOT NULL,
      CONSTRAINT PK_TOPIC PRIMARY KEY(id) --제약조건 고유이름 프라머리키(어떤프레임ex:id,title) 고유이름 생략가능
      --또는 id NUMBER PRIMARY KEY 로도 가능
  );
  ```

- PRIMARY KEY : 다른 테이블의 프라이머 키를 가리킨다. 1대 다 관계를 가짐

```sql
CREATE TABLE topic(
	id NUMBER NOT NULL,
 	--생략
   FORIGN
);
```

- SEQUENCE : 다음 추가할 행의 순서값을 자동으로 해준다.

  ```sql
  CREATE SEQUENCE SEQ_TOPIC; --밑에 안쓰면 1부터 1씩 증가
  	START WITH 1 --시작값을 설정가능
  	INCREMENT BY 1 --증가값을 설정가능
  INSERT INTO topic
  	(id,title,description,created)
  	VALUES(
          	SEQ_TOPIC.NEXTVAL,
          	'MongoDB',
          	'MongoDB is...',
          	SYSDATE
      );
  SELECT SEQ_TOPIC.CURRVAL FROM topic; --현재 시퀀스 값
  SELECT SEQ_TOPIC.CURRVAL FROM DUAL; --가상테이블(DUAL)
  ```

- Count : 몇개의 데이터가 있는지 가르쳐줌

  ```sql
  select count(*) from 테이블 이름;
  ```

  

- 서버와 클라이언트

  - 서버: 서비스를 제공하는 호스트
  - 클라이언트 : 서비스를 요청하는 호스트(고객)
  - 서버를 둬서 다른 공간에 있어도 많은 사용자들이 같이 데이터베이스를 사용할 수 있다.

![image-20210416012759277](https://user-images.githubusercontent.com/80496345/116240990-6ae58f00-a79f-11eb-94d5-8a6b6bb9790e.png)

## 테이블

- 테이블 삭제

  ```sql
  DROP TABLE topic;
  ```

- 테이블 구조를 남겨 놓는 삭제 

  ```sql
  TRUNCATE TABLE topic;
  ```

- **ORA-02449: unique/primary keys in table referenced by foreign keys**

  **ORA-02449 : 외래 키에 의해 참조되는 고유/기본키가 테이블에 있습니다**

  ```sql
  DROP TABLE parent_table CASCADE CONSTRAINTS;

  출처: https://joont.tistory.com/140 [Toward the Developer]

  

- 테이블 수정

  - 열의 추가

  ```sql
  UPDATE topic
  		SET
  		 title = 'MSSQL',
  		 descrition = 'MSSQL is...'
  		WHERE
  		 id = 3;
  
  UPDATE buyTBL SET price = price * 1.5; --구매 테이블의 현재 단가를 ahen 1.5배 인상
  ```

  - 열의 삭제
  
  - 열의 이름 변경
  
    ```
    ALTER table bookTBL RENAME COLUMN descrition TO description;
    ```
  
  - 열의 데이터 형식 변경
  
  - 열의 제약 조건 추가 및 삭제



## 이클립스를 이용해 오라클 접속 및 쿼리 보내기

1. 접속(서버와 클라이이언트 통로 연결 connection)

```java
public Connection getConnection() throws ClassNotFoundException, SQLException {
		Connection con = null;
		String jdbcURL = "jdbc:oracle:thin:@localhost:1521:xe";
		String driver = "oracle.jdbc.OracleDriver";
		String id = "hr";
		String pwd = "1234";
		
		Class.forName(driver);
		con = DriverManager.getConnection(jdbcURL,id,pwd);
		
		return con;
	}

```

2. 통로를 통해 query를 보내기

- statement를 통해

```java
public void query1() throws ClassNotFoundException, SQLException {
	Connection con = this.getConnection(); 
	String sql = "INSERT INTO testTBL2(id,username,age) " //쿼리작성
			+ "VALUES (7,'lee','addr')";
	// id가 2번인 userName을 lee로 변경하는 쿼리를 작성하시오.
	sql = "UPDATE testTBL2 SET userName='lee' WHERE id = 2";
	// userName 이 kim인 자료를 모두 삭제하는 쿼리를 작성하시오.
	sql = "DELETE FROM where userName = 'kim'";
	Statement stmt = con.createStatement();//통로 생성
	int affectedCount = stmt.executeUpdate(sql);//보내기 
	if(affectedCount>0) { //테이블의 변경이 있다(리턴이 1)
		System.out.println("입력완료");
	} else {
		System.out.println("입력실패");
	}
	stmt.close();
	con.close();
}
```
- preparedstatement를 통해

```java
	public void query3() throws NumberFormatException, IOException, ClassNotFoundException, SQLException {
		this.makeGisaData();
		System.out.println(list.size());
		//gisaTBL에 한줄의 값을 삽입
		StudentDTO dto = list.get(1);//리스트 가져오기
		Connection con = this.getConnection();
		String sql = "INSERT INTO gisaTBL Values (?,?)";//데이터가 들어가는 컬럼의 구조를 먼저 결정하므로써 컴파일 여러번 할 필요가 없다. ?,?는 차례대로 인덱스 1, 2을 뜻한다.
		PreparedStatement pstmt = con.prepareStatement(sql);//먼저 쿼리(sql) 구조를 보내 전용통로를 만든다.
		pstmt.setInt(1, dto.getStdNo()); //얘는 SetINT,string등으로 호출하여 잘못된 타입의 값이 들어가면 에러나온다
		pstmt.setString(2, dto.getEmail());
		
		int affectedCount = pstmt.executeUpdate();//select는 executeQuery()
		if(affectedCount>0) {
			System.out.println("입력완료");
		} else {
			System.out.println("입력실패");
		}
		pstmt.close();
		con.close();
	}
```
3. 잘갔는지 확인

```sql
SELECT * FROM gisaTBL; //테이블 확인
```

4. 자원확보를 위한 통로를 닫는다

```java
	pstmt.close();
	con.close();
```

5. C:\oraclexe\app\oracle\product\11.2.0\server\jdbc\lib



## 조인

- 조인 : 두 개 이상의 테이블을 서로 묶어서 하나의 결과 집합으로 만들어 내는것.

- INNER JOIN(내부조인):조건에 맞는 것을 출력

  ```sql
  SELECT B.userID,U.userName,U.mobile1 || u.mobile2 AS "연락처"
  	FROM buyTbl B
  		INNER JOIN userTbl U
  			ON buyTbl.userID - userTbl.userID
  	--WHERE B.userID = 'JYP'; JYP인것만 출력
  	--ORDER BY U.userID; 회원 아이디 순으로 출력
  ```

  

- OUTER JOIN(외부조인): 조인 조건에 맞지 않는 것들도 출력해준다.

  ```sql
  SELECT B.userID,U.userName,U.mobile1 || u.mobile2 AS "연락처"
  	FROM buyTbl B
  		<LEFT|RIGHT|FULL>OUTER JOIN userTbl U
  			ON buyTbl.userID - userTbl.userID
  ```



## 뷰

- 보안과 단순화를 



## 인덱스

- 데이터를 좀 더 빠르게 찾을 수 있도록 해주는 도구
- 검색의 속도가 무척 빨라질 수 있다.
- 종류로는 B-TREE, 등이 있다.
- 인덱스는 프라이머 키, 유니크 설정시 자동적으로 되고 열 단위로  생성된다..
- 결론
  - 인덱스는 열단위에 생성
  - WHERE절에 사용되는 열에 인덱스를 만들어야하고 자주 사용해야 가치가 있다.
  - 데이터의 중복도가 높은 열은 인덱스를 만들어도 효과 없음
  - 조인에서 자주 사용되는 열에는 인덱스를 생성해주는게 좋다
  - INSERT/UPDATE/DELETE가 얼마나 자주 일어나는지 고려
  - 사용하지 않는 인덱스는 제거



## 스토어드 포로시저와 함수

- 스토어드 포로시저
  - 스토어드 프로시저와 함수는 오라클에서 제공하는 프로그래밍 기능
  - 매개변수도 사용 가능 호출은 EXECUTE문을 사용
  - 함수는 반환하는 값이 반드시 있고 SELECT문 안에서 사용
  - 테이블 형식을 반환하는 함수도 사용할 수 있다
  - 커서는 일반 프로그래밍의 파일 처리와 비슷한 방법 제공
- 함수

- 커서
- 패키지 - 자바에 있는거 처럼 똑같음



## 트리거

- 트리거는 테이블 또는 뷰와 관련되어 INSERT, UPDATE, DELETE 등의 이벤트가 발생될 때 작동하는 데이터 베이스 개체 중 하나다.