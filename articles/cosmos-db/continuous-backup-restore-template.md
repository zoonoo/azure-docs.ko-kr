---
title: ARM 템플릿을 사용 하 여 Azure Cosmos DB에서 연속 백업 및 지정 시간 복원을 구성 합니다.
description: 연속 백업으로 계정을 프로 비전 하 고 Azure Resource Manager 템플릿을 사용 하 여 데이터를 복원 하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527553"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>연속 백업 및 지정 시간 복원 구성 및 관리-Azure Resource Manager 템플릿 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB의 지정 시간 복원 기능을 사용 하면 삭제 된 계정, 데이터베이스 또는 컨테이너를 복원 하거나 백업이 있던 모든 지역으로 복원 하기 위해 컨테이너 내에서 실수로 인 한 변경 으로부터 복구 하는 데 도움이 됩니다. 연속 백업 모드를 사용 하면 지난 30 일 이내에 모든 시점으로 복원할 수 있습니다.

이 문서에서는 연속 백업으로 계정을 프로 비전 하 고 Azure Resource Manager 템플릿을 사용 하 여 데이터를 복원 하는 방법을 설명 합니다.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>연속 백업으로 계정 프로 비전

Azure Resource Manager 템플릿을 사용 하 여 연속 모드로 Azure Cosmos DB 계정을 배포할 수 있습니다. 계정을 프로 비전 하도록 템플릿을 정의 하는 경우 다음 예제와 같이 "backupPolicy" 매개 변수를 포함 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

그런 다음 Azure PowerShell 또는 CLI를 사용 하 여 템플릿을 배포 합니다. 다음 예제에서는 CLI 명령을 사용 하 여 템플릿을 배포 하는 방법을 보여 줍니다.

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>리소스 관리자 템플릿을 사용 하 여 복원

리소스 관리자 템플릿을 사용 하 여 계정을 복원할 수도 있습니다. 템플릿을 정의 하는 경우 다음 매개 변수를 포함 합니다.

* "CreateMode" 매개 변수를 "Restore"로 설정 합니다.
* "RestoreParameters"를 정의 합니다. "restoreSource" 값은 `az cosmosdb restorable-database-account list` 원본 계정에 대 한 명령의 출력에서 추출 됩니다. 계정 이름에 대 한 인스턴스 ID 특성을 사용 하 여 복원을 수행 합니다.
* "RestoreMode" 매개 변수를 "PointInTime"으로 설정 하 고 "restoreTimestampInUtc" 값을 구성 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

그런 다음 Azure PowerShell 또는 CLI를 사용 하 여 템플릿을 배포 합니다. 다음 예제에서는 CLI 명령을 사용 하 여 템플릿을 배포 하는 방법을 보여 줍니다.  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>다음 단계

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)또는 [Azure Portal](continuous-backup-restore-portal.md)를 사용 하 여 연속 백업을 구성 하 고 관리 합니다.
* [연속 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md)
* 연속 백업 모드를 사용 하 여 데이터를 복원 하는 데 필요한 [권한을 관리](continuous-backup-restore-permissions.md) 합니다.