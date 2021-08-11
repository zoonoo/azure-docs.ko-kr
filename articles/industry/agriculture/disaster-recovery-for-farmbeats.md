---
title: FarmBeats 재해 복구
description: 이 문서에서는 데이터 복구를 통해 데이터 손실을 방지하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 04/13/2020
ms.author: riyazp
ms.openlocfilehash: 132e084f8ceaa9fbfacd7609a4c4ae895a4e5cc6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751394"
---
# <a name="disaster-recovery-for-farmbeats"></a>FarmBeats 재해 복구

데이터 복구는 Azure 지역 축소와 같은 이벤트 발생 시 데이터 손실을 방지합니다. 이러한 이벤트 발생 시 장애 조치(failover)를 시작하고 FarmBeats 배포에 저장된 데이터를 복구할 수 있습니다.

데이터 복구는 Azure FarmBeats의 기본 기능이 아닙니다. FarmBeats에서 Azure 쌍을 이루는 지역에 데이터를 저장하는 데 사용하는 필수 Azure 리소스를 구성하여 이 기능을 수동으로 구성할 수 있습니다. 활성 - 수동 접근 방식을 사용하여 복구를 사용하도록 설정합니다.

다음 섹션에서는 Azure FarmBeats에서 데이터 복구를 구성하는 방법에 대한 정보를 제공합니다.

- [데이터 중복성 사용](#enable-data-redundancy)
- [온라인 백업에서 서비스 복원](#restore-service-from-online-backup)


## <a name="enable-data-redundancy"></a>데이터 중복성 사용

FarmBeats는 **Azure storage**, **Cosmos DB** 및 **Time Series Insights**, 이렇게 3개의 Azure 자사 서비스에 데이터를 저장합니다. 다음 단계를 사용하여 쌍을 이루는 Azure 지역에 이러한 서비스에 데이터 중복성을 사용하도록 설정합니다.

1.  **Azure Storage** - 이 지침에 따라 FarmBeats 배포의 각 스토리지 계정에 데이터 중복성을 사용하도록 설정합니다.
2.  **Azure Cosmos DB** - 이 지침에 따라 FarmBeats 배포의 Cosmos DB 계정에 데이터 중복성을 사용하도록 설정합니다.
3.  **Azure TSI(Time Series Insights)** - TSI는 현재 데이터 중복성을 제공하지 않습니다. Time Series Insights 데이터를 복구하려면 센서/날씨 파트너로 이동하고 데이터를 FarmBeats 배포로 다시 푸시합니다.

## <a name="restore-service-from-online-backup"></a>온라인 백업에서 서비스 복원

FarmBeats 배포를 위해 위에서 언급한 데이터 저장소별로 장애 조치(failover)를 시작하고 저장된 데이터를 복구할 수 있습니다. Azure Storage 및 Cosmos DB에 대한 데이터를 복구한 후에는 Azure 쌍을 이루는 지역에 다른 FarmBeats 배포를 만든 다음, 아래 단계를 사용하여 복원된 데이터 저장소(예: Azure Storage 및 Cosmos DB)의 데이터를 사용하도록 새 배포를 구성합니다.

1. [Cosmos DB 구성](#configure-cosmos-db)
2. [스토리지 계정 구성](#configure-storage-account)


### <a name="configure-cosmos-db"></a>Cosmos DB 구성

복원된 Cosmos DB 액세스 키를 복사하고 새 FarmBeats Datahub Key Vault를 업데이트합니다.


  ![액세스 키의 복사본을 얻을 수 있는 위치가 강조 표시된 스크린샷.](./media/disaster-recovery-for-farmbeats/key-vault-secrets.png)

> [!NOTE]
> 복원된 Cosmos DB의 URL을 복사하고 새 FarmBeats Datahub App Service 구성에서 이를 업데이트합니다. 이제 새 FarmBeats 배포에서 Cosmos DB 계정을 삭제할 수 있습니다.

  ![복원된 Cosmos DB의 URL을 복사할 위치를 보여주는 스크린샷.](./media/disaster-recovery-for-farmbeats/configuration.png)

### <a name="configure-storage-account"></a>스토리지 계정 구성

복원된 스토리지 계정의 액세스 키를 복사하고 새 FarmBeats Datahub Key Vault에서 이를 업데이트합니다.

![복원된 스토리지 계정의 액세스 키를 복사할 위치를 보여주는 스크린샷.](./media/disaster-recovery-for-farmbeats/key-vault-7-secrets.png)

>[!NOTE]
> 새 FarmBeats Batch VM 구성 파일에서 스토리지 계정 이름을 업데이트해야 합니다.

![재해 복구](./media/disaster-recovery-for-farmbeats/batch-prep-files.png)

마찬가지로 Accelerator 스토리지 계정에 대한 데이터 복구를 사용하도록 설정한 경우 2단계에 따라 새 FarmBeats 인스턴스에서 가속기 스토리지 계정 액세스 키와 이름을 업데이트합니다.
