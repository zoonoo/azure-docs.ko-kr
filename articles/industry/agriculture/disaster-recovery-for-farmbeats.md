---
title: FarmBeats에 대 한 재해 복구
description: 이 문서에서는 데이터 복구가 데이터 손실을 방지 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 04/13/2020
ms.author: v-umha
ms.openlocfilehash: 1665c535d4b1fb6190ee5736b688b402f8b4a541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683903"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats에 대 한 재해 복구

데이터 복구는 Azure 지역 축소와 같은 이벤트에서 데이터 손실을 방지 합니다. 이러한 이벤트에서 장애 조치 (failover)를 시작 하 고 FarmBeats 배포에 저장 된 데이터를 복구할 수 있습니다.

데이터 복구는 Azure FarmBeats의 기본 기능이 아닙니다. FarmBeats에서 Azure 쌍을 이루는 지역에 데이터를 저장 하는 데 사용 하는 필수 Azure 리소스를 구성 하 여이 기능을 수동으로 구성할 수 있습니다. 활성-수동 방법을 사용 하 여 복구를 사용 하도록 설정 합니다.

다음 섹션에서는 Azure FarmBeats에서 데이터 복구를 구성 하는 방법에 대 한 정보를 제공 합니다.

- [데이터 중복성 사용](#enable-data-redundancy)
- [온라인 백업에서 서비스 복원](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>데이터 중복성 사용

FarmBeats는 **azure storage**, **Cosmos DB** 및 **Time Series Insights**세 가지 azure 자사 서비스에 데이터를 저장 합니다. 다음 단계를 사용 하 여 쌍을 이루는 Azure 지역에 이러한 서비스에 대 한 데이터 중복성을 사용 하도록 설정 합니다.

1.  **Azure Storage** -이 지침에 따라 FarmBeats 배포의 각 저장소 계정에 대해 데이터 중복성을 사용 하도록 설정 합니다.
2.  **Azure Cosmos DB** -이 지침에 따라 FarmBeats 배포 Cosmos DB 계정에 대 한 데이터 중복성을 사용 하도록 설정 합니다.
3.  **Tsi (Azure Time Series Insights)** -tsi는 현재 데이터 중복성을 제공 하지 않습니다. Time Series Insights 데이터를 복구 하려면 센서/날씨 파트너로 이동 하 여 데이터를 다시 FarmBeats 배포에 푸시합니다.

## <a name="restore-service-from-online-backup"></a>온라인 백업에서 서비스 복원

FarmBeats 배포에 대해 위에서 언급 한 각 데이터 저장소에 대 한 장애 조치 (failover) 및 복구 된 데이터를 초기화할 수 있습니다. Azure storage 및 Cosmos DB에 대 한 데이터를 복구한 후에는 Azure 쌍을 이루는 지역에 또 다른 FarmBeats 배포를 만든 후 다음 단계를 사용 하 여 복원 된 데이터 저장소의 데이터 (예: Azure Storage 및 Cosmos DB)를 사용 하도록 새 배포를 구성 합니다.

1. [Cosmos DB 구성](#configure-cosmos-db)
2. [저장소 계정 구성](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB 구성

복원 된 Cosmos DB의 선택 키를 복사 하 고 새 FarmBeats Datahub Key Vault를 업데이트 합니다.


  ![재해 복구](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 복원 된 Cosmos DB의 URL을 복사 하 고 새 FarmBeats Datahub App Service 구성에서 업데이트 합니다. 이제 새 FarmBeats 배포에서 Cosmos DB 계정을 삭제할 수 있습니다.

  ![재해 복구](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>저장소 계정 구성

복원 된 저장소 계정의 액세스 키를 복사 하 고 새 FarmBeats Datahub Key Vault에서 업데이트 합니다.

![재해 복구](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 새 FarmBeats Batch VM 구성 파일에서 저장소 계정 이름을 업데이트 해야 합니다.

![재해 복구](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

마찬가지로, 액셀러레이터 저장소 계정에 대해 데이터 복구를 사용 하도록 설정한 경우 2 단계를 수행 하 여 새 FarmBeats 인스턴스에서 액셀러레이터 저장소 계정 액세스 키 및 이름을 업데이트 합니다.
