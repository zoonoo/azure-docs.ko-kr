---
title: MongoDB에 대 한 Azure Cosmos DB API에 대 한 질문과 대답
description: Azure Cosmos DB의 MongoDB API에 대 한 질문과 대답을 확인 하세요.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 08899018d03209dab09f61d4dd74feceee03b246
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019020"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API에 대 한 질문과 대답
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

MongoDB에 대 한 Azure Cosmos DB의 API는 응용 프로그램에서 MongoDB에 대 한 기존 커뮤니티 지원 Sdk 및 드라이버를 사용 하 여 네이티브 Azure Cosmos 데이터베이스 엔진과 쉽고 투명 하 게 통신할 수 있도록 하는 유선 프로토콜 호환성 계층입니다. 이제 개발자는 기존 MongoDB 도구 체인 기술을 사용 하 여 Azure Cosmos DB를 활용 하는 응용 프로그램을 빌드할 수 있습니다. 개발자는 다중 지역 쓰기 복제, 자동 인덱싱, 백업 유지 관리, 재정적 지원 되는 Sla (서비스 수준 계약) 등의 글로벌 배포를 포함 하는 Azure Cosmos DB의 고유한 기능을 활용할 수 있습니다.

## <a name="how-do-i-connect-to-my-database"></a>내 데이터베이스 연결하려면 어떻게 할까요?

Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결하는 가장 빠른 방법은 [Azure Portal](https://portal.azure.com)로 이동하는 것입니다. 계정으로 이동하여 왼쪽 탐색 메뉴에서 **빠른 시작** 을 클릭합니다. 빠른 시작은 데이터베이스에 연결 하는 코드 조각을 얻는 가장 좋은 방법입니다.

Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 적용됩니다. Azure Cosmos DB 계정은 TLS를 통한 인증 및 보안 통신이 필요 하므로 TLSv 1.2를 사용 해야 합니다.

자세한 내용은 [Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결](connect-mongodb-account.md)을 참조하세요.

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 동안 오류 코드?

일반적인 MongoDB 오류 코드와 함께 MongoDB 용 Azure Cosmos DB API에는 고유한 특정 오류 코드가 있습니다. 이러한 [정보는 문제 해결 가이드](mongodb-troubleshoot.md)에서 찾을 수 있습니다.

## <a name="supported-drivers"></a>지원되는 드라이버

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB에 사용할 수 있도록 MongoDB용 Simba 드라이버가 지원됩니까?

예, MongoDB에 대 한 Azure Cosmos DB의 API와 함께 Simba의 Mongo ODBC 드라이버를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 .NET 웹 앱 빌드](create-mongodb-dotnet.md)
* [Azure Cosmos DB에서 Java 및 MongoDB API를 사용하여 콘솔 앱 만들기](create-mongodb-java.md)
