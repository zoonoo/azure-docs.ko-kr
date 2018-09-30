---
title: 오픈 소스 NoSQL 데이터베이스를 사용하여 Azure Cosmos DB 다중 마스터 사용
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963903"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>오픈 소스 NoSQL 데이터베이스를 사용하여 Azure Cosmos DB 다중 마스터 사용

Azure Cosmos DB 다중 마스터 지원은 Cosmos DB에서 지원하는 모든 오픈 소스 NoSQL 제품을 사용할 수 있는 네이티브 서버 쪽 구현입니다. 모든 Cosmos DB 지원 SDK에서 액세스할 수 있습니다.

Azure Cosmos DB는 이러한 오픈 소스 NoSQL 데이터베이스에 대한 다중 마스터 지원을 제공하는 전 세계 첫 번째 서비스입니다.

|모델  |지원  |
|---------|---------|
|MongoDB  | 활성-활성  |
|그래프  | 활성-활성 |
|Cassandra  | 활성-활성   |

## <a name="use-mongodb-clients-with-multi-master"></a>다중 마스터를 사용하여 MongoDB 클라이언트 사용

다중 마스터 기능은 MongoDB API 계정에 대해 사용하도록 설정되어 있으며, 다른 Cosmos DB API에 대해서도 동일한 방식으로 적용됩니다. 다중 마스터를 MongoDB API 계정에 대해 사용하도록 설정한 후에는 클라이언트 응용 프로그램의 각 인스턴스가 읽기 및 쓰기에 대해 기본 영역을 선택할 수 있습니다. MongoDB 드라이버 관점에서 기본 영역은 클라이언트에 주 복제본 세트로 나타납니다. 이 방식으로 분산 데이터베이스의 모든 영역은 주 복제본 세트로 작동할 수 있습니다. Azure Cosmos DB 다중 마스터를 사용하면 글로벌로 분산된 MongoDB 응용 프로그램에 대한 쓰기 대기 시간을 크게 줄일 수 있습니다. 

### <a name="set-the-primary-region"></a>주 지역 설정

MongoDB 클라이언트의 각 인스턴스는 MongoDB 클라이언트 드라이버에서 수락된 “응용 프로그램 이름” 필드에 `@<preferred_primary_region_name>`을 추가하여 주 지역을 선택할 수 있습니다. 대부분의 드라이버는 다음과 같이 연결 문자열에서 이를 수락합니다.

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

연결한 후 다음과 같은 경우 중 하나가 발생할 수 있습니다.

* 기본 지역 이름을 쓰기 영역으로 사용할 수 있는 경우 Azure Cosmos DB는 이를 주 지역으로 선택합니다.

* 기본 지역 이름이 제공되지 않는 경우 Azure Cosmos DB는 기본 지역을 주 지역으로 선택합니다.

* 기본 지역 이름이 제공되지만 데이터베이스 계정의 쓰기 영역으로 사용할 수 없는 경우 Azure Cosmos DB는 주 지역으로 사용할 수 있는 가장 가까운 쓰기 지역을 선택합니다.

NodeJS 드라이버와 같은 특정 드라이버는 항상 초기 연결 문자열에 지정된 호스트로 쓰기를 먼저 발행합니다. 그러한 드라이버의 경우 쓰기가 주 지역으로 보내지도록 하려면 앱 이름 외에, 지역 이름을 포함하도록 연결 문자열 내 DNS 이름을 수정해야 합니다. 지역 이름은 공백 없이 지정해야 합니다. 예: 

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>충돌 해결 모드

항상 Azure Cosmos DB MongoDB API 계정에 대한 충돌 해결 모드는 쓰기를 수락한 지역 서버 타임스탬프를 사용하는 최종 작성자 우선(last-writer-wins)입니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Cosmos DB MongoDB API 계정에 대한 다중 마스터 지원에 대해 알아보았습니다. 다음으로 아래 리소스를 살펴봅니다.

* [Azure Cosmos DB 계정에 다중 마스터를 지원하는 방법](enable-multi-master.md)

* [Azure Cosmos DB의 충돌 해결 이해](multi-master-conflict-resolution.md)
