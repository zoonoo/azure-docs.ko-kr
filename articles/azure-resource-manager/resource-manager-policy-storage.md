---
title: "저장소 계정에 대한 Azure 리소스 정책 | Microsoft Docs"
description: "저장소 계정 배포를 관리하기 위한 Azure Resource Manager 정책을 설명합니다."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="apply-resource-policies-to-storage-accounts"></a>저장소 계정에 리소스 정책 적용
이 항목에서는 Azure Storage 계정에 적용할 수 있는 몇 가지 [리소스 정책](resource-manager-policy.md)을 보여 줍니다. 이러한 정책을 통해 조직에 배포된 저장소 계정에 대한 일관성을 보장합니다. 

## <a name="define-permitted-storage-account-types"></a>허용되는 저장소 계정 유형 정의

다음 정책은 배포될 수 있는 [저장소 계정 유형](../storage/common/storage-redundancy.md)을 제한합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

허용되는 SKU를 수락하는 매개 변수를 사용하는 비슷한 정책 규칙은 기본 제공 정책 정의로 제공됩니다. 기본 제공 정책에는 `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`의 리소스 ID가 있습니다. 

## <a name="define-permitted-access-tier"></a>허용되는 액세스 계층 정의

다음 정책은 저장소 계정에 대해 지정될 수 있는 [액세스 계층](../storage/blobs/storage-blob-storage-tiers.md)의 유형을 지정합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>암호화를 사용하도록 설정해야 함

다음 정책에서는 모든 저장소 계정이 [저장소 계정 암호화](../storage/common/storage-service-encryption.md)를 사용하도록 설정해야 합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

또한 이 정책 규칙은 `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`의 리소스 ID를 사용하여 기본 제공 정책 정의로 제공됩니다.

## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 포털을 통해 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](resource-manager-policy-portal.md)를 참조하세요. REST API, PowerShell 또는 Azure CLI를 통해 정책을 할당하려면 [스크립트를 통해 정책 할당 및 관리](resource-manager-policy-create-assign.md)를 참조하세요. 
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.


