---
title: "저장소에 대한 Azure Resource Manager 정책 | Microsoft Docs"
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>저장소 계정에 Azure 리소스 정책 적용
Azure Resource Manager 정책을 통해 조직 내에서 리소스가 배포되는 방식에 대한 일관적인 규칙을 정의합니다. 조직의 리소스를 관리하는 데 필요한 규칙을 조직 내 사용자가 위반하지 않도록 사용자 지정된 정책을 만듭니다. 이 항목은 Azure Storage 계정에 대한 규칙을 정의하는 몇 가지 정책을 보여줍니다. 정책에 대한 자세한 내용은 [리소스 정책을 사용하여 리소스 관리](resource-manager-policy.md)를 참조하세요.

이 항목의 예제는 정책 규칙에 하드 코드된 값을 보여줍니다. 하지만 매개 변수를 사용하여 정책을 할당할 때 사용되는 값을 전달할 수 있습니다. 자세한 내용은 [정책 매개 변수](resource-manager-policy.md#parameters)를 참조하세요.

## <a name="define-permitted-storage-account-types"></a>허용되는 저장소 계정 유형 정의

다음 정책은 배포될 수 있는 [저장소 계정 유형](../storage/storage-redundancy.md)을 제한합니다.

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

## <a name="define-permitted-access-tier"></a>허용되는 액세스 계층 정의

다음 정책은 저장소 계정에 대해 지정될 수 있는 [액세스 계층](../storage/storage-blob-storage-tiers.md)의 유형을 지정합니다.

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

다음 정책에서는 모든 저장소 계정이 [저장소 계정 암호화](../storage/storage-service-encryption.md)를 사용하도록 설정해야 합니다.

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

## <a name="create-and-assign-policies"></a>정책 만들기 및 할당

앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책을 만들어서 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 정책을 만들고 할당하는 예제는 [정책 만들기 및 할당](resource-manager-policy.md#create-and-assign-a-policy)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.




<!--HONumber=Jan17_HO3-->


