# 여러가지 sql문

**AUTO_INCREMENT 값을 초기화**

```sql
ALTER TABLE [TABLE명] AUTO_INCREMENT = [시작할 값];
```

**AUTO_INCREMENT 값을 초기화 후, 테이블 안의 모든 데이터의 ID값을 재조정하는 방법**

```sql
ALTER TABLE [테이블명] AUTO_INCREMENT=1;
SET @COUNT = 0;
UPDATE [테이블명] SET [AUTO_INCREMENT 열 이름] = @COUNT:=@COUNT+1;
```

위 쿼리문은 현재 모든 데이터 ID값을 1부터 재조정하고, 다음 인덱스 값을 [마지막 행번호 + 1] 로 정하는 명령어이다.