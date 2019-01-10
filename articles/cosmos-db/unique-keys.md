---
title: Azure Cosmos DB의 고유 키 사용
description: Azure Cosmos DB 데이터베이스에서 고유 키를 사용하는 방법 알아보기
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 20e5c96110f07d8eaec218ed167c87a48fd65782
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037381"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Azure Cosmos DB의 고유 키

고유 키는 Cosmos 데이터베이스에 데이터 무결성 레이어를 추가할 수 있는 기능을 제공합니다. 고유 키 정책은 Cosmos 컨테이너를 만들 때 만듭니다. 고유 키를 사용하면 논리 파티션에 있는 하나 또는 여러 값의 고유성이 보장됩니다([파티션 키](partition-data.md)마다 고유성을 보장할 수 있음). 고유 키 정책을 사용하여 컨테이너를 만들면 해당 정책은 고유 키 제약 조건에 지정된 대로 새로운(또는 업데이트된) 중복 항목을 논리 파티션 내에 만들 수 없도록 차단합니다. 파티션 키를 고유 키와 결합하면 컨테이너 범위 내에서 항목의 고유성이 보장됩니다.

예를 들어 이메일 주소를 고유 키 제약 조건으로, `CompanyID`를 파티션 키로 사용하는 Cosmos 컨테이너가 있다고 가정해 봅시다. 사용자의 이메일 주소를 고유 키를 구성하면 각 항목이 지정된 `CompanyID` 내에서 고유한 이메일 주소를 갖습니다. 이메일 주소가 중복되고 파티션 키가 동일한 두 항목을 만들 수 없습니다.  

이메일 주소가 같은 항목을 여러 개 만들 수 있지만 이름, 성 및 이메일 주소가 모두 같은 항목을 여러 개 만들 수는 없는 기능을 사용자에게 제공하려면 고유 키 정책에 대한 경로를 추가하면 됩니다. 이메일 주소를 기반으로 고유 키를 만드는 대신 이름, 성, 이메일 주소의 조합을 사용하여 고유 키를 만들 수도 있습니다(복합 고유 키). 이 경우 지정된 `CompanyID` 내에서 세 값의 고유한 조합이 허용됩니다. 예를 들어 각 항목이 고유 키 제약 조건을 준수하는 다음 값이 컨테이너에 포함할 수 있습니다.

|CompanyID|이름|성|메일 주소|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

위의 표에 나열된 조합을 사용하여 또 다른 항목을 삽입하려고 시도하면 고유 키 제약 조건이 충족되지 않았다는 오류가 표시됩니다. "지정된 ID 또는 이름을 사용하는 리소스가 이미 있습니다" 또는 "지정된 ID, 이름 또는 고유 인덱스를 사용하는 리소스가 이미 있습니다"라는 반환 메시지를 받게 됩니다.  

## <a name="defining-a-unique-key"></a>고유 키 정의

Cosmos 컨테이너를 만들 때에만 고유 키를 정의할 수 있습니다. 고유 키의 범위는 논리 파티션으로 지정됩니다. 이전 예에서 우편 번호를 기준으로 컨테이너를 분할하면 결국 각 논리 파티션에서 중복 항목이 발생하게 됩니다. 고유 키를 만들 때 다음 속성을 고려해야 합니다.

* 다른 고유 키를 사용하도록 기존 컨테이너를 업데이트할 수 없습니다. 다시 말해서, 고유 키 정책을 사용하여 컨테이너를 만든 후에는 정책을 변경할 수 없습니다.

* 기존 컨테이너의 고유 키를 설정하려면 고유 키 제약 조건을 사용하여 새 컨테이너를 만들고, 적절한 데이터 마이그레이션 도구를 사용하여 기존 컨테이너에서 새 컨테이너로 데이터를 이동해야 합니다. SQL 컨테이너의 경우 [데이터 마이그레이션 도구](import-data.md)를 사용하여 데이터를 이동합니다. MongoDB 컨테이너의 경우 [mongoimport.exe 또는 mongorestore.exe](mongodb-migrate.md)를 사용하여 데이터를 이동합니다.

* 고유 키 정책에 허용되는 최대 경로 값은 16입니다(예: /firstName, /lastName, /address/zipCode). 각 고유 키 정책에 허용되는 최대 고유 키 제약 조건 또는 조합은 10개이고 각 고유 인덱스 제약 조건의 결합 경로는 60자를 초과하면 안 됩니다. 이전 예제에서는 이름, 성, 이메일 주소가 합쳐져서 하나의 제약 조건이 되었으며, 사용 가능한 16개 경로 중 3개를 사용합니다.

* 컨테이너에 고유 키 정책이 있으면 항목을 만들고 업데이트하고 삭제하는 요청 단위(RU) 요금이 약간 더 높습니다.

* 스파스 고유 키는 지원되지 않습니다. 일부 고유 경로 값이 누락되면 null 값으로 간주되어 고유성 제약 조건에 포함됩니다. 따라서 이 제약 조건을 만족하는 null 값을 갖는 단일 항목만 가능합니다.

* 고유 키 이름은 대/소문자를 구분합니다. 예를 들어 컨테이너의 고유 키 제약 조건이 /address/zipcode로 설정되었다고 가정해 봅시다. 데이터에 ZipCode라는 필드가 있으면 Cosmos DB는 "null"을 고유 키로 삽입합니다. "zipcode"와 "ZipCode"는 다르기 때문입니다. 이처럼 대/소문자를 구분하므로 ZipCode가 포함된 모든 레코드를 삽입할 수 없습니다. 중복되는 "null"이 고유 키 제약 조건을 위반하기 때문입니다.

## <a name="next-steps"></a>다음 단계

* [논리 파티션](partition-data.md)에 대한 자세한 정보
