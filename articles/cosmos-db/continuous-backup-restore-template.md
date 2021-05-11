---
title: ARM 템플릿을 사용하여 Azure Cosmos DB에서 지속적인 백업 및 특정 시점 복원을 구성합니다.
description: 지속적인 백업으로 계정을 프로비전하고 Azure Resource Manager 템플릿을 사용하여 데이터를 복원하는 방법을 알아봅니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381820"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>지속적인 백업 및 특정 시점 복원(미리 보기) 구성 및 관리 - Azure Resource Manager 템플릿 사용
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB에 대한 특정 시점 복원 기능(지속적인 백업 모드)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Cosmos DB의 특정 시점 복원 기능(미리 보기)을 사용하면 컨테이너 내에서 실수로 인한 변경을 복구하거나, 삭제된 계정, 데이터베이스 또는 컨테이너를 복원하거나, 백업이 있던 모든 지역으로 복원할 수 있습니다. 지속적인 백업 모드를 사용하면 지난 30일 이내의 모든 시점으로 복원할 수 있습니다.

이 문서에서는 지속적인 백업으로 계정을 프로비전하고 Azure Resource Manager 템플릿을 사용하여 데이터를 복원하는 방법을 설명합니다.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>지속적인 백업으로 계정 프로비전

Azure Resource Manager 템플릿을 사용하여 연속 모드로 Azure Cosmos DB 계정을 배포할 수 있습니다. 계정을 프로비전하기 위해 템플릿을 정의하는 경우 다음 예제와 같이 `backupPolicy` 매개 변수를 포함합니다.

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

다음으로 Azure PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다. 다음 예제에서는 CLI 명령을 사용하여 템플릿을 배포하는 방법을 보여줍니다.

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Resource Manager 템플릿을 사용하여 복원

Resource Manager 템플릿을 사용하여 계정을 복원할 수도 있습니다. 템플릿을 정의할 때 다음 매개 변수를 포함합니다.

* `createMode` 매개 변수를 *Restore* 로 설정합니다.
* `restoreParameters`를 정의합니다. `restoreSource` 값은 원본 계정에 대한 `az cosmosdb restorable-database-account list` 명령의 출력에서 추출됩니다. 계정 이름에 대한 인스턴스 ID 특성을 사용하여 복원을 수행합니다.
* `restoreMode` 매개 변수를 *PointInTime* 으로 설정하고 `restoreTimestampInUtc` 값을 구성합니다.

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

다음으로 Azure PowerShell 또는 CLI를 사용하여 템플릿을 배포합니다. 다음 예제에서는 CLI 명령을 사용하여 템플릿을 배포하는 방법을 보여줍니다.  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>다음 단계

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md) 또는 [Azure Portal](continuous-backup-restore-portal.md)을 사용하여 지속적인 백업 구성 및 관리.
* [지속적인 백업 모드의 리소스 모델](continuous-backup-restore-resource-model.md).
* 지속적인 백업 모드를 사용하여 데이터를 복원하는 데 필요한 [권한 관리](continuous-backup-restore-permissions.md).