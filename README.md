# MySQL과 Visual Studio C언어 연동

Visual Studio 2022에서 C 언어를 사용하여 MySQL 데이터베이스와 연동하는 방법을 다룹니다.
MySQL 서버 설치부터 C 코드로 데이터베이스에 접속하고 데이터를 조회하는 과정까지 단계별로 설명합니다.

## 📌 프로젝트 개요

- **주제**: Visual Studio C언어와 MySQL 연동
- **목표**: C 언어를 사용하여 MySQL 데이터베이스에 접속하고 데이터를 조회하는 프로그램 구현
- **환경**:
  - 운영 체제: Windows
  - 개발 도구: Visual Studio 2022
  - 데이터베이스: MySQL 8.0.42

## 🛠️ 개발 환경 설정

### 1. MySQL 서버 설치

- MySQL 8.0.42 버전 다운로드: [MySQL Community Server](https://dev.mysql.com/downloads/mysql/)
- Windows(x86, 64-bit), ZIP Archive 형식으로 다운로드하여 설치

### 2. Visual Studio 2022 설치 및 프로젝트 생성

- Visual Studio 2022 설치
- 새로운 C++ 콘솔 애플리케이션 프로젝트 생성

### 3. 프로젝트 속성 설정

- **C/C++ → 일반 → 추가 포함 디렉터리**:
  - `C:\Program Files\MySQL\MySQL Server 8.0\include`
- **링커 → 일반 → 추가 라이브러리 디렉터리**:
  - `C:\Program Files\MySQL\MySQL Server 8.0\lib`
- **링커 → 입력 → 추가 종속성**:
  - `libmysql.lib`
- `libmysql.dll` 파일을 프로젝트의 Debug 폴더에 복사

## 🗃️ 데이터베이스 구성

### 1. 데이터베이스 및 테이블 생성

```sql
CREATE DATABASE testDB;
USE testDB;

CREATE TABLE customer (
    u_id INT PRIMARY KEY,
    u_name CHAR(20) NOT NULL,
    address VARCHAR(200)
);

INSERT INTO customer (u_id, u_name, address) VALUES
(100, '이호석', '서울'),
(200, '이희경', '광주'),
(300, '박덕기', '인천'),
(400, '배호종', '대전'),
(500, '지광성', '부산'),
(600, '김기동', '대전');

SELECT * FROM customer;
```

## 💻 C 언어를 통한 MySQL 접속 및 데이터 조회

### 1. MySQL 접속 프로그램

```c
#include <stdio.h>
#include <WinSock2.h>
#include <mysql.h>
#pragma comment(lib, "libmysql.lib")
#pragma comment(lib, "ws2_32.lib")

#define MYSQL_HOST "localhost"
#define MYSQL_USER "root"
#define MYSQL_PWD "1111"
#define MYSQL_DB "testdb"
#define PORT 3306

int main()
{
    MYSQL mysql;

    mysql_init(&mysql);

    if (!mysql_real_connect(&mysql, MYSQL_HOST, MYSQL_USER, MYSQL_PWD, MYSQL_DB, PORT, 0, 0))
    {
        printf("[접속 실패]\n");
        printf("MySql 접속 NO\n");
        printf("오류 메시지: %s\n", mysql_error(&mysql));
    }
    else
    {
        mysql_query(&mysql, "set names euckr");
        printf("[접속 성공]\n");
        printf("MySql 접속 OK\n");

        mysql_close(&mysql);
    }
    return 0;
}
```

### 2. 데이터 조회 프로그램

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <WinSock2.h>
#include <mysql.h>
#pragma comment(lib, "libmysql.lib")
#pragma comment(lib, "ws2_32.lib")

#define MYSQL_HOST "localhost"
#define MYSQL_USER "root"
#define MYSQL_PWD "1111"
#define MYSQL_DB "testdb"
#define PORT 3306

int main()
{
    MYSQL mysql;
    MYSQL_ROW row;
    MYSQL_RES* res;

    char sql[1024];

    mysql_init(&mysql);

    if (!mysql_real_connect(&mysql, MYSQL_HOST, MYSQL_USER, MYSQL_PWD, MYSQL_DB, PORT, 0, 0))
    {
        printf("[접속 실패]\n");
        printf("MySql 접속 NO\n");
        printf("오류 메시지: %s\n", mysql_error(&mysql));
        return 1;
    }

    mysql_query(&mysql, "set names euckr");

    sprintf(sql, "SELECT * FROM customer");
    if (mysql_query(&mysql, sql))
    {
        printf("쿼리 실행 실패: %s\n", mysql_error(&mysql));
        mysql_close(&mysql);
        return 1;
    }

    res = mysql_store_result(&mysql);

    while ((row = mysql_fetch_row(res)) != NULL)
    {
        printf("u_id: %s, u_name: %s, address: %s\n", row[0], row[1], row[2]);
    }

    mysql_free_result(res);
    mysql_close(&mysql);

    return 0;
}
```

## ✅ 결과

- MySQL 데이터베이스에 성공적으로 접속하고, `customer` 테이블의 데이터를 조회하여 출력합니다.

## 📚 참고 자료

- [MySQL 공식 다운로드](https://dev.mysql.com/downloads/mysql/)
- [MySQL C API 문서](https://dev.mysql.com/doc/c-api/en/)

---

이 프로젝트는 C 언어를 사용하여 MySQL 데이터베이스와의 연동을 실습하는 데 중점을 두었습니다.
데이터베이스 프로그래밍에 대한 이해를 높이고, 실제 애플리케이션 개발에 활용할 수 있는 기반을 마련하는 데 도움이 될 것입니다.
