<properties 
	pageTitle="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 MongoChef 사용 | Microsoft Azure" 
	description="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 MongoChef를 사용하는 방법을 알아봅니다. 현재 미리 보기를 지원합니다." 
	keywords="MongoChef"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="mimig"/>

# MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 MongoChef 사용

MongoChef를 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정에 연결하려면 다음을 수행해야 합니다.

- [MongoChef](http://3t.io/mongochef) 다운로드 및 설치
- MongoDB [연결 문자열](documentdb-connect-mongodb-account.md) 정보에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 필요합니다.

## MongoChef에서 연결을 만듭니다.  

MongoChef 연결 관리자에 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 추가하려면 다음 단계를 수행합니다.

1. [여기](documentdb-connect-mongodb-account.md)에 있는 지침을 사용하여 MongoDB 연결 정보에 대한 프로토콜 지원을 사용하는 DocumentDB를 검색합니다.

	![연결 문자열 블레이드의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectionStringBlade.png)

2. **연결**을 클릭하여 연결 관리자를 열려고 **새 연결**을 클릭합니다.

	![MongoChef 연결 관리자의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. **새 연결** 창의 **서버** 탭에서 MongoDB 및 포트에 대한 프로토콜 지원을 사용하는 DocumentDB 계정의 호스트(FQDN)을 입력합니다.
	
	![MongoChef 연결 관리자 서버 탭의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. **새 연결** 창의 **인증** 탭에서 인증 모드 **표준(MONGODB CR 또는 SCARM-SHA-1)**을 선택하고 사용자 이름 및 암호를 입력합니다. 기본 인증 DB(관리자)를 그대로 사용하거나 고유한 값을 지정합니다.

	![MongoChef 연결 관리자 인증 탭의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. **새 연결** 창의 **SSL** 탭에서 **SSL 프로토콜을 사용하여 연결** 확인란 및 **자체 서명된 SSL 인증서 허용** 라디오 단추를 확인합니다.

	![MongoChef 연결 관리자 SSL 탭의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. **연결 테스트** 단추를 클릭하여 연결 정보의 유효성을 검사하고 **확인**을 클릭하여 새 연결 창으로 돌아간 다음 **저장**을 클릭합니다.

	![MongoChef 테스트 연결 창의 스크린샷](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## MongoChef를 사용하여 데이터베이스, 컬렉션 및 문서를 만듭니다.  

MongoChef를 사용하여 데이터베이스, 컬렉션 및 문서를 만들려면 다음 단계를 수행합니다.

1. **연결 관리자**에서 연결을 강조 표시하고 **연결**을 클릭합니다.

	![MongoChef 연결 관리자의 스크린샷](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. 호스트를 마우스 오른쪽 단추로 클릭하고 **데이터베이스 추가**를 선택합니다. 데이터베이스 이름을 지정하고 **확인**을 클릭합니다.
	
	![MongoChef 데이터베이스 추가 옵션의 스크린샷](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **컬렉션 추가**를 선택합니다. 컬렉션 이름을 지정하고 **만들기**를 클릭합니다.

	![MongoChef 컬렉션 추가 옵션의 스크린샷](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. **컬렉션** 메뉴 항목을 클릭한 다음 **문서 추가**를 클릭합니다.

	![MongoChef 문서 추가 메뉴 항목의 스크린샷](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. 문서 추가 대화 상자에서 다음을 붙여 넣고 **문서 추가**를 클릭합니다.

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. 이번에는 다음 내용을 사용하여 다른 문서를 추가합니다.

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. 샘플 쿼리를 실행합니다. 예를 들어 'Andersen' 성을 가진 가족을 검색하고 부모 및 상태 필드를 반환합니다.

	![MongoChef 쿼리 결과의 스크린샷](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## 다음 단계

- MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하는 DocumentDB를 탐색합니다.
- MongoDB [미리 보기 개발 지침](documentdb-mongodb-guidelines.md)에 대한 프로토콜 지원을 사용하는 DocumentDB에 대해 읽습니다.

 

<!---HONumber=AcomDC_0928_2016-->