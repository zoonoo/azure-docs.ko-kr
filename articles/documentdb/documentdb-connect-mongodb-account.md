<properties 
	pageTitle="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결 | Microsoft Azure" 
	description="MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결하는 방법을 알아봅니다. 현재 미리 보기를 지원합니다. MongoDB 연결 문자열을 사용하여 연결합니다." 
	keywords="MongoDB 연결 문자열"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결하는 방법

표준 MongoDB 연결 문자열 URI 형식을 사용하여 MongoDB에 대한 프로토콜 지원을 사용하는 Azure DocumentDB 계정에 연결하는 방법을 알아봅니다.

## 계정의 연결 문자열 정보 가져오기

1. 새 창에서 [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. **모든 설정** 블레이드에서 **연결 문자열**을 클릭합니다. **모든 설정**으로 이동하려면 표시줄에서 **찾아보기**를 클릭하고 **DocumentDB** 계정을 클릭한 다음 MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 선택합니다.

	![모든 설정 블레이드의 스크린샷](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. 미리 구성된 연결 문자열을 포함하는 MongoDB에 드라이버를 사용하여 계정에 연결하는 데 필요한 모든 정보를 가진 **연결 문자열 정보** 블레이드가 열립니다.

	![연결 문자열 블레이드의 스크린샷](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## 연결 문자열 요구 사항

DocumentDB가 두 가지 특정 요구 사항을 포함한 표준 MongoDB 연결 문자열 URI 형식을 지원해야 합니다. DocumentDB 계정에는 인증 및 SSL을 통한 보안 통신이 필요합니다. 따라서 연결 문자열 형식은 다음과 같습니다.

	mongodb://username:password@host:port/[database]?ssl=true

여기서 이 문자열의 값은 위에 표시된 연결 문자열 블레이드에서 사용할 수 있습니다.

- Username(필수)
	- DocumentDB 계정 이름
- 암호(필수)
	- DocumentDB 계정 암호
- 호스트(필수)
	- DocumentDB 계정의 FQDN
- 포트(필수)
	- 10250
- 데이터베이스(선택 사항)
	- 연결에서 사용하는 기본 데이터베이스
- ssl=true(필수)

예를 들어 위의 연결 문자열 정보에 표시된 계정을 사용하는 것이 좋습니다. 유효한 연결 문자열은 다음과 같습니다.
	
	mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## MongoDB의 C# 드라이버에 연결
이미 언급했듯이 모든 DocumentDB 계정에는 인증 및 SSL 통한 보안 통신이 모두 필요합니다. MongoDB 연결 문자열 URI 형식이 ssl=true 쿼리 문자열 매개 변수를 지원하는 반면 MongoDB C# 드라이버를 사용하면 MongoClient를 만들 경우 MongoClientSettings 개체를 사용해야 합니다. 위의 계정 정보에 따르면 다음 코드 조각은 계정에 연결하여 "작업" 데이터베이스로 작업하는 방법을 보여 줍니다.

	        MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
	

## 다음 단계


- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 있는 [MongoChef를 사용](documentdb-mongodb-mongochef.md)하는 방법을 알아봅니다.
- MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 DocumentDB를 탐색합니다.

 

<!---HONumber=AcomDC_0720_2016-->