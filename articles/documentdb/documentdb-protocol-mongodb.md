<properties 
    pageTitle="MongoDB에 대한 DocumentDB 프로토콜 지원 | Microsoft Azure" 
    description="현재 공개 미리 보기에 사용할 수 있는 MongoDB에 대한 DocumentDB 프로토콜 지원에 대해 알아봅니다." 
    keywords="MongoDB"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>


# <a name="documentdb-protocol-support-for-mongodb"></a>MongoDB에 대한 DocumentDB 프로토콜 지원

## <a name="what-is-azure-documentdb?"></a>Azure DocumentDB 정의
Azure DocumentDB는 신속하고 예측 가능한 성능, 고가용성, 자동 크기 조정 및 개발의 용이성을 위해 구축된 완전히 관리되는 NoSQL 데이터베이스 서비스입니다. 유연한 데이터 모델, 일관된 짧은 대기 시간 및 풍부한 쿼리 기능은 원활한 규모를 필요로 하는 웹, 모바일, 게임, IoT와 다른 여러 응용 프로그램에 매우 적합합니다. 자세한 내용은 [DocumentDB 소개](documentdb-introduction.md)를 참고합니다.

## <a name="what-is-documentdb-protocol-support-for-mongodb?"></a>MongoDB에 대한 DocumentDB 프로토콜 지원이란?
이제 DocumentDB 데이터베이스는 MongoDB에 대해 작성된 앱의 데이터 저장소로 사용될 수 있습니다. MongoDB에 기존 [드라이버](https://docs.mongodb.org/ecosystem/drivers/) 를 사용하면 응용 프로그램이 대부분 연결 문자열만을 변경하여 쉽고 투명하게 DocumentDB와 통신할 수 있습니다.  이 미리 보기 기능을 사용하면 고객이 DocumentDB의 완전하게 관리되고 확장성 있는 NoSQL 데이터베이스를 활용하여 MongoDB에 친숙한 기술과 도구를 계속 사용하는 동시에 Azure 클라우드에서 응용 프로그램을 쉽게 빌드하고 실행할 수 있습니다.

MongoDB에 대한 DocumentDB 프로토콜 지원을 사용하면 핵심 MongoDB API 함수가 데이터베이스를 쿼리할 뿐만 아니라 데이터를 만들고 읽고 업데이트하고 삭제(CRUD)할 수 있습니다. 현재 구현된 기능은 해당 클라우드 플랫폼에 대한 Azure를 평가하는 공통 플랫폼, 프레임워크, 도구 및 대규모 MongoDB 고객의 요구에 따라 우선 순위가 지정됩니다.
  

## <a name="next-steps"></a>다음 단계


- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 [만드는](documentdb-create-mongodb-account.md) 방법을 알아봅니다.
- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정을 [연결하는](documentdb-connect-mongodb-account.md) 방법을 알아봅니다.
- MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정이 있는 [MongoChef를 사용](documentdb-mongodb-mongochef.md) 하는 방법을 알아봅니다.
- MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 DocumentDB를 탐색합니다.

 



<!--HONumber=Oct16_HO2-->


