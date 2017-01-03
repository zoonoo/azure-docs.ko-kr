---
title: "MongoDB에 대한 DocumentDB 프로토콜 지원이란? | Microsoft Docs"
description: "MongoDB에 대한 DocumentDB 프로토콜 지원이란? 이러한 지원을 통해 관리되는 클라우드 기반 서비스인 Azure DocumentDB를 MongoDB용으로 작성된 응용 프로그램의 데이터 저장소로 사용할 수 있습니다."
keywords: "MongoDB 정의"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 0703fa4f4d3eb9f23aa9b3c0b90267fac9557f13
ms.openlocfilehash: 876fff6a63ccf1a3c10a6852927c60b454436954


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>MongoDB에 대한 DocumentDB 프로토콜 지원이란?

이제 DocumentDB 데이터베이스는 MongoDB에 대해 작성된 앱의 데이터 저장소로 사용될 수 있습니다. 즉 MongoDB 데이터베이스에 대해 기존 [드라이버](https://docs.mongodb.org/ecosystem/drivers/)를 사용하면 MongoDB를 위해 작성된 응용 프로그램에서 이제는 DocumentDB와 통신하고 MongoDB 데이터베이스 대신 DocumentDB 데이터베이스를 사용할 수 있습니다. 대부분의 경우 연결 문자열을 변경하기만 하면 MongoDB에서 DocumentDB로 전환할 수 있습니다. 이 기능을 사용하면 Azure 클라우드에서 MongoDB 데이터베이스 응용 프로그램을 쉽게 빌드하고 실행할 수 있습니다. 즉, DocumentDB에서 완벽하게 관리하고 확장성 있는 NoSQL 데이터베이스를 활용하면서 MongoDB에 친숙한 기술과 도구를 계속 사용할 수 있습니다.

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>MongoDB에 DocumentDB 프로토콜 지원을 사용하는 이점
**서버 관리 필요 없음** - DocumentDB는 완벽히 관리되는 서비스이므로 인프라 또는 Virtual Machines를 직접 관리할 필요가 없습니다. DocumentDB는 20 + [Azure 지역](https://azure.microsoft.com/regions/services/)에서 사용할 수 있습니다.

**무제한 확장** - 처리량과 저장소를 독립적이고 탄력적으로 확장할 수 있습니다. 용량을 추가하여 초당 수백만 개의 요청을 쉽게 처리할 수 있습니다.

**엔터프라이즈급** - DocumentDB는 로컬 및 하위 지역 장애에 대비하여 99.99%의 가용성 및 데이터 보호 기능을 제공하는 다중 로컬 복제본을 지원합니다. DocumentDB에는 엔터프라이즈 급 [규정 준수 인증서](https://www.microsoft.com/trustcenter) 및 보안 기능이 있습니다. 

**MongoDB 호환성** - MongoDB에 대한 DocumentDB 프로토콜 지원은 MongoDB와 호환되도록 설계되었습니다. 기존 코드, 응용 프로그램, 드라이버 및 도구를 사용하여 DocumentDB 작업을 수행할 수 있습니다. 

## <a name="how-to-get-started"></a>시작하는 방법
[Azure Portal](https://portal.azure.com)에서 MongoDB에 대한 프로토콜을 지원하는 DocumentDB 계정을 만들고 새 계정에 연결합니다. 

*이것으로 끝입니다!*

자세한 지침에 대해서는 [계정 만들기](documentdb-create-mongodb-account.md) 및 [계정에 연결](documentdb-connect-mongodb-account.md)을 따르세요.

## <a name="next-steps"></a>다음 단계
* [MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정 만들기](documentdb-create-mongodb-account.md) 자습서를 수행하여 DocumentDB 계정을 만듭니다.
* [MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정에 연결](documentdb-connect-mongodb-account.md) 자습서를 수행하여 계정 연결 문자열 정보를 가져오는 방법을 알아봅니다.
* [MongoDB에 대한 프로토콜 지원을 사용하는 DocumentDB 계정으로 MongoChef 사용](documentdb-mongodb-mongochef.md) 자습서를 수행하여 MongoChef에서 DocumentDB 데이터베이스와 MongoDB 앱 사이의 연결을 만드는 방법을 알아봅니다.
* MongoDB [샘플](documentdb-mongodb-samples.md)에 대한 프로토콜 지원을 사용하여 DocumentDB를 탐색합니다.




<!--HONumber=Nov16_HO4-->


