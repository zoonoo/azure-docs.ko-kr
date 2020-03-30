---
title: Azure Cosmos DB 자동 조종 모드에서 처리량에 대해 자주 묻는 질문
description: Azure Cosmos DB 데이터베이스 및 컨테이너에 대한 자동 조종 모드에 대해 자주 묻는 질문
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483311"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Azure Cosmos DB 자동 조종 모드에서 프로비저닝된 처리량에 대해 자주 묻는 질문(미리 보기)
자동 조종 모드를 사용하면 Azure Cosmos DB는 사용량에 따라 컨테이너 또는 데이터베이스의 RU/s를 자동으로 관리하고 확장합니다. 이 문서에서는 자동 조종 모드에 대한 일반적인 질문에 대한 답변을 답변합니다. 

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-autopilot-mode-supported-for-all-apis"></a>모든 API에 대해 자동 조종 모드가 지원되고 있습니까?
예. 자동 조종 모드는 모든 API에 대해 지원됩니다: 코어(SQL), 그렘린, 테이블, 카산드라 및 MongoDB용 API.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>다중 마스터 계정에 는 자동 조종 모드가 지원되고 있습니까?
예. 다중 마스터 계정에는 자동 조종 모드가 지원됩니다. 최대 RU/s는 코스모스 계정에 추가된 각 리전에서 사용할 수 있습니다. 

### <a name="what-is-the-pricing-for-autopilot"></a>오토파일럿의 가격은 어떻게 됩니까?
자세한 내용은 Azure Cosmos DB [가격 페이지를](https://azure.microsoft.com/pricing/details/cosmos-db/) 참조하십시오. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>컨테이너 또는 데이터베이스에 대해 자동 조종 장치를 사용하도록 설정하려면 어떻게 해야 합니까?
Azure 포털을 사용하여 만든 새 컨테이너 및 데이터베이스에서 자동 조종 모드를 사용할 수 있습니다. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>자동 조종 모드로 컨테이너 또는 데이터베이스를 만드는 CLI 또는 SDK 지원이 있습니까?
현재 미리 보기 릴리스에서는 Azure 포털에서 자동 조종 모드로만 리소스를 만들 수 있습니다. CLI 및 SDK에 대한 지원은 아직 제공되지 않습니다.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>기존 컨테이너 또는 데이터베이스에서 자동 조종 장치를 활성화할 수 있습니까?
현재 새 컨테이너 및 데이터베이스에서 자동 조종 장치를 만들 때 사용할 수 있습니다. 기존 컨테이너 및 데이터베이스에서 자동 조종 모드를 사용하도록 설정하는 지원은 아직 사용할 수 없습니다. [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 를 사용하여 기존 컨테이너를 새 컨테이너로 마이그레이션하거나 [피드를 변경할](change-feed.md)수 있습니다. 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>컨테이너 또는 데이터베이스에서 자동 조종 모드를 끌 수 있습니까?
예. 프로비저닝된 처리량에 대한 '수동' 옵션으로 전환하여 자동 조종 장치를 끌 수 있습니다. 미리 보기 릴리스에서 자동 조종 모드에서 수동 모드로 전환한 후에는 동일한 리소스에 대해 자동 조종 장치를 다시 활성화할 수 없습니다. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>공유 처리량 데이터베이스에 대해 자동 조종 모드가 지원되고 있습니까?
예. 공유 처리량 데이터베이스에 대해 자동 조종 모드가 지원됩니다. 이 기능을 사용하려면 데이터베이스를 만들 때 자동 조종 모드 및 **프로비저닝 처리량** 옵션을 선택합니다. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>자동 조종 장치를 사용할 때 공유 처리량 데이터베이스당 허용되는 컬렉션 수는 몇 개입니까?
자동 조종 모드가 활성화된 공유 처리량 데이터베이스의 경우 허용되는 컬렉션 수는 MIN(25, 데이터베이스의 최대 RU/s/1000)입니다. 예를 들어 데이터베이스의 최대 처리량이 20,000 RU/s인 경우 데이터베이스에 MIN(25, 20,000 RU/s/1000) = 20개의 컬렉션이 있을 수 있습니다. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>각 최대 RU/s 옵션과 관련된 스토리지 제한은 무엇입니까?  
각 최대 RU/s에 대한 GB의 저장 한도는 데이터베이스 또는 컨테이너의 최대 RU/s / 100입니다. 예를 들어 최대 RU/s가 20,000 RU/s인 경우 리소스는 200GB의 저장소를 지원할 수 있습니다. 사용 가능한 최대 RU/s 및 스토리지 옵션에 대한 [자동 조종 장치 제한](provision-throughput-autopilot.md#autopilot-limits) 문서를 참조하십시오. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>최대 처리량과 관련된 스토리지 제한을 초과하면 어떻게 되나요?
데이터베이스 또는 컨테이너의 최대 처리량과 관련된 저장소 제한을 초과하면 Azure Cosmos DB는 해당 저장소 수준을 지원할 수 있는 다음 최상위 계층으로 최대 처리량을 자동으로 증가시다. 예를 들어 데이터베이스 또는 컨테이너가 스토리지 제한이 50GB인 4000 RU/s의 최대 처리량 옵션으로 프로비전되었다고 가정합니다. 리소스 저장소가 100GB로 증가하면 데이터베이스 또는 컨테이너의 최대 RU/s가 자동으로 20,000RU/s로 증가하여 최대 200GB를 지원할 수 있습니다. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>트래픽 급증에 따라 자동 조종 장치가 얼마나 빨리 확장 및 축소될 까요?
오토파일럿은 들어오는 트래픽에 따라 최소 및 최대 RU/s 범위 내에서 RU/s를 즉각적으로 확장하거나 축소합니다. 청구는 1시간 분단위로 이루어지며, 여기서 특정 시간 중 가장 높은 RU/s에 대한 요금이 부과됩니다. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>자동 조종 모드에 대한 사용자 지정 최대 처리량(RU/s) 값을 지정할 수 있습니까?
현재 미리 보기 릴리스 중에 최대 처리량(RU/s)에 대한 [네 가지 옵션](provision-throughput-autopilot.md#autopilot-limits) 중에서 선택할 수 있습니다.

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>데이터베이스 또는 컨테이너의 최대 RU/s(상위 계층으로 이동)를 늘릴 수 있습니까? 
예. 컨테이너에 대한 **& 설정 조정 조정** 또는 데이터베이스의 **배율** 조정 옵션에서 자동 조종 장치의 경우 더 높은 최대 RU/s를 선택할 수 있습니다. 이 작업은 서비스가 더 높은 규모를 지원하기 위해 더 많은 리소스를 프로비저닝하기 때문에 완료하는 데 시간이 걸릴 수 있는 비동기 확장 작업입니다(일반적으로 RU/s에 따라 4-6시간). 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>데이터베이스 또는 컨테이너의 최대 RU/s(하위 계층으로 이동)를 줄일 수 있습니까?
예. 데이터베이스 또는 컨테이너의 현재 저장소가 축소하려는 최대 RU/s 계층과 연결된 [저장소 제한](#what-is-the-storage-limit-associated-with-each-max-rus-option) 보다 낮으면 최대 RU/s를 해당 계층으로 줄일 수 있습니다. 예를 들어 최대 RU/s로 20,000RU/s를 선택한 경우 스토리지가 50GB 미만인 경우 최대 RU/s를 4000 RU/s로 축소할 수 있습니다(4000 RU/s와 연결된 저장소 제한).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>최대 RU/s와 물리적 파티션 간의 매핑은 무엇입니까?
최대 RU/s를 처음 선택하면 Azure Cosmos DB가 프로비전됩니다: 최대 RU/s/ 10,000 RU/s = 실제 파티션의 #. 각 [물리적 파티션은](partition-data.md#physical-partitions) 최대 10,000RU/s 및 50GB 스토리지를 지원할 수 있습니다. 저장소 크기가 증가함에 따라 Azure Cosmos DB는 자동으로 파티션을 분할하여 저장소 증가를 처리하기 위해 더 많은 물리적 파티션을 추가하거나 저장소가 [연결된 제한을 초과하는](#what-is-the-storage-limit-associated-with-each-max-rus-option)경우 최대 RU/s 계층을 늘립니다. 

데이터베이스 또는 컨테이너의 최대 RU/s는 모든 실제 파티션에 걸쳐 균등하게 분할됩니다. 따라서 단일 물리적 파티션의 총 처리량은 데이터베이스 또는 컨테이너의 최대 RU/s / 물리적 파티션으로 확장할 수 있습니다. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>들어오는 요청이 데이터베이스 또는 컨테이너의 최대 RU/s를 초과하면 어떻게 됩니까?
전체 사용 RU/s가 컨테이너 또는 데이터베이스의 최대 RU/s를 초과하는 경우 최대 RU/s를 초과하는 요청이 제한되고 429 상태 코드가 반환됩니다. 100% 이상 정규화된 사용률을 초래하는 요청도 제한됩니다. 정규화된 사용률은 모든 물리적 파티션에서 RU/s 사용률의 최대값으로 정의됩니다. 예를 들어 최대 처리량이 20,000 RU/s이고 각각 10,000RU/s로 확장할 수 있는 두 개의 물리적 파티션(P_1 및 P_2)이 있다고 가정합니다. 주어진 두 번째 에서 P_1 6000 RU를 사용하고 8000 RU를 P_2 경우 정규화된 사용률은 MAX(6000 RU/ 10,000 RU, 8000 RU/ 10,000 RU) = 0.8입니다.

> [!NOTE]
> Azure Cosmos DB 클라이언트 SDK 및 데이터 가져오기 도구(Azure Data Factory, 대량 실행기 라이브러리)는 429s에서 자동으로 다시 시도하므로 가끔 429s는 괜찮습니다. 429s의 지속적인 높은 수는 최대 RU /s를 증가하거나 [핫 파티션에](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)대한 분할 전략을 검토해야 함을 나타낼 수 있습니다.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>자동 조종 장치를 사용할 때 429s (제한 / 속도 제한)를 볼 수 있습니까? 
예. 두 가지 시나리오에서 429s를 볼 수 있습니다. 첫째, 전체 사용 RU/s가 컨테이너 또는 데이터베이스의 최대 RU/s를 초과하면 서비스는 그에 따라 요청을 제한합니다. 

둘째, 핫 파티션이 있는 경우( 즉, 다른 파티션 키 값에 비해 요청량이 지나치게 높은 논리 파티션 키 값) 기본 물리적 파티션이 RU/s 예산을 초과할 수 있습니다. 핫 파티션을 방지하려면 저장소와 처리량을 모두 균등하게 분배하는 [좋은 파티션 키를 선택하는](partitioning-overview.md#choose-partitionkey) 것이 가장 좋습니다. 

예를 들어 20,000RU/s의 최대 처리량 옵션을 선택하고 4개의 물리적 파티션이 있는 200GB의 스토리지가 있는 경우 각 물리적 파티션을 최대 5000 RU/s까지 자동 크기 조정할 수 있습니다. 특정 논리 파티션 키에 핫 파티션이 있는 경우 기본 물리적 파티션이 5000 RU/s를 초과할 때( 즉, 정규화된 사용률을 100% 초과하면) 429s를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 컨테이너 또는 데이터베이스에서 자동 조종 장치를 사용하도록 설정하는](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)방법에 대해 알아봅니다.
* [자동 조종 장치의 이점에](provision-throughput-autopilot.md#benefits-of-autopilot-mode)대해 알아보십시오.
* [논리 및 물리적 파티션에](partition-data.md)대해 자세히 알아봅니다.
