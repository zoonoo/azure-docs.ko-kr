---
title: MongoDB에 대 한 Azure Cosmos DB API에 대 한 질문과 대답
description: Azure Cosmos DB의 MongoDB API에 대 한 질문과 대답을 확인 하세요.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 05b0ff7d4f56a61c5c91848044a30cb9bf1d0f46
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91565312"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API에 대 한 질문과 대답

MongoDB에 대 한 Azure Cosmos DB의 API는 응용 프로그램에서 MongoDB에 대 한 기존 커뮤니티 지원 Sdk 및 드라이버를 사용 하 여 네이티브 Azure Cosmos 데이터베이스 엔진과 쉽고 투명 하 게 통신할 수 있도록 하는 유선 프로토콜 호환성 계층입니다. 이제 개발자는 기존 MongoDB 도구 체인 기술을 사용 하 여 Azure Cosmos DB를 활용 하는 응용 프로그램을 빌드할 수 있습니다. 개발자는 다중 지역 쓰기 복제, 자동 인덱싱, 백업 유지 관리, 재정적 지원 되는 Sla (서비스 수준 계약) 등의 글로벌 배포를 포함 하는 Azure Cosmos DB의 고유한 기능을 활용할 수 있습니다.

## <a name="how-do-i-connect-to-my-database"></a>내 데이터베이스 연결하려면 어떻게 할까요?

Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결하는 가장 빠른 방법은 [Azure Portal](https://portal.azure.com)로 이동하는 것입니다. 계정으로 이동하여 왼쪽 탐색 메뉴에서 **빠른 시작**을 클릭합니다. 빠른 시작은 데이터베이스에 연결 하는 코드 조각을 얻는 가장 좋은 방법입니다.

Azure Cosmos DB에는 엄격한 보안 요구 사항과 표준이 적용됩니다. Azure Cosmos DB 계정은 TLS를 통한 인증 및 보안 통신이 필요 하므로 TLSv 1.2를 사용 해야 합니다.

자세한 내용은 [Cosmos 데이터베이스를 Azure Cosmos DB의 API for MongoDB에 연결](connect-mongodb-account.md)을 참조하세요.

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB API를 사용 하는 동안 오류 코드?

Azure Cosmos DB의 API for MongoDB는 일반적인 MongoDB 오류 코드 외에도 자체적인 특정 오류 코드를 갖고 있습니다.

| Error               | 코드  | Description  | 해결 방법  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 사용 된 총 요청 단위 수가 컨테이너에 대 한 프로 비전 된 요청 단위 요금 보다 더 많이 제한 되어 있습니다. | Azure Portal에서 컨테이너 또는 컨테이너 집합에 할당된 처리량을 크기 조정하거나 다시 시도하는 것이 좋습니다. |
| ExceededMemoryLimit | 16501 | 다중 테넌트 서비스로써 작업이 클라이언트의 메모리 할당량을 초과했습니다. | [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에서 보다 제한적인 쿼리 조건을 통해 작업 범위를 줄이거나 고객 지원에 문의하세요. <br><br> 예: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>지원되는 드라이버

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB에 사용할 수 있도록 MongoDB용 Simba 드라이버가 지원됩니까?

예, MongoDB에 대 한 Azure Cosmos DB의 API와 함께 Simba의 Mongo ODBC 드라이버를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 .NET 웹 앱 빌드](create-mongodb-dotnet.md)
* [Azure Cosmos DB에서 Java 및 MongoDB API를 사용하여 콘솔 앱 만들기](create-mongodb-java.md)
