---
title: FarmBeats에 대한 재해 복구
description: 이 문서에서는 데이터 복구가 데이터 손실로부터 보호하는 방법을 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: d64735e683ba1133e7d381a68611d204c4068026
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313104"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats에 대한 재해 복구

데이터 복구는 Azure 지역 붕괴와 같은 이벤트에서 데이터가 손실되지 않도록 보호합니다. 이러한 경우 장애 조치(failover)를 시작하고 FarmBeats 배포에 저장된 데이터를 복구할 수 있습니다.

Azure FarmBeats의 데이터 복구는 기본 기능이 아닙니다. FarmBeats에서 Azure 쌍을 이루는 지역에 데이터를 저장하는 데 사용되는 필요한 Azure 리소스를 구성하여 이 기능을 수동으로 구성할 수 있습니다. 활성 - 수동 접근 방식을 사용하여 복구를 활성화합니다.

다음 섹션에서는 Azure FarmBeats에서 데이터 복구를 구성하는 방법에 대한 정보를 제공합니다.

- [데이터 중복 성 활성화](#enable-data-redundancy)
- [온라인 백업에서 서비스 복원](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>데이터 중복 성 활성화

FarmBeats는 **Azure 저장소,** 코스모스 DB 및 타임 시리즈 **인사이트인**세 개의 Azure 자사 서비스에 데이터를 **저장합니다.** 다음 단계를 사용하여 이러한 서비스에 대한 데이터 중복성을 쌍을 이루는 Azure 리전에 사용하도록 설정합니다.

1.  **Azure 저장소** - 이 지침을 따라 FarmBbeats 배포의 각 저장소 계정에 대한 데이터 중복성을 활성화합니다.
2.  **Azure Cosmos DB** - 이 지침을 따라 Cosmos DB 계정에 대한 데이터 중복성을 FarmBeats 배포에 사용할 수 있습니다.
3.  **Azure Time Series Insights(TSI)** - TSI는 현재 데이터 중복성을 제공하지 않습니다. Time Series Insights 데이터를 복구하려면 센서/날씨 파트너에게 가서 데이터를 FarmBeats 배포로 다시 푸시합니다.

## <a name="restore-service-from-online-backup"></a>온라인 백업에서 서비스 복원

Failover를 시작하고 FarmBeats 배포에 대해 위에서 언급한 각 데이터가 저장되는 데이터를 복구할 수 있습니다. Azure 저장소 및 Cosmos DB에 대한 데이터를 복구한 후 Azure 쌍을 이루는 리전에서 다른 FarmBeats 배포를 만든 다음 다음 단계를 사용하여 복원된 데이터 저장소(예: Azure Storage 및 Cosmos DB)의 데이터를 사용하도록 새 배포를 구성합니다.

1. [Cosmos DB 구성](#configure-cosmos-db)
2. [저장소 계정 구성](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB 구성

복원된 코스모스 DB의 액세스 키를 복사하고 새로운 FarmBeats Datahub 키 볼트를 업데이트합니다.


  ![재해 복구](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 복원된 코스모스 DB의 URL을 복사하여 새 FarmBeats Datahub 앱 서비스 구성에서 업데이트합니다. 이제 새 FarmBeats 배포에서 코스모스 DB 계정을 삭제할 수 있습니다.

  ![재해 복구](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>저장소 계정 구성

복원된 저장소 계정의 액세스 키를 복사하고 새 FarmBeats Datahub 키 자격 증명 모음에서 업데이트합니다.

![재해 복구](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 새 FarmBeats 일괄 처리 VM 구성 파일에서 저장소 계정 이름을 업데이트해야 합니다.

![재해 복구](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

마찬가지로 액셀러레이터 저장소 계정에 대한 데이터 복구를 사용하도록 설정한 경우 2단계를 따라 새 FarmBeats 인스턴스에서 가속기 저장소 계정 액세스 키 및 이름을 업데이트합니다.
