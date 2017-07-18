---
title: "Azure에서 Windows VM에 정책을 사용하여 보안 적용 | Microsoft Docs"
description: "Azure Resource Manager Windows 가상 컴퓨터에 정책을 적용하는 방법"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: kasing
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 9874a825ea81ebb191710ebd46dceb70c1f20e60
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Windows VM에 정책 적용
조직은 정책을 사용하여 엔터프라이즈 전체에 다양한 규칙을 적용할 수 있습니다. 원하는 동작을 적용하여 조직의 성공에 기여함과 동시에 위험을 완화할 수 있습니다. 이 문서에서는 Azure Resource Manager 정책을 사용하여 조직의 Virtual Machines에 대해 원하는 동작을 정의하는 방법을 설명합니다.

정책에 대한 소개는 [정책을 사용하여 리소스 및 컨트롤 액세스 관리](../../azure-resource-manager/resource-manager-policy.md)를 참조하세요.

## <a name="define-policy-for-permitted-virtual-machines"></a>허용된 Virtual Machines에 대한 정책 정의
조직에 대한 가상 컴퓨터가 응용 프로그램과 호환되는지 확인하기 위해 허용된 운영 체제를 제한할 수 있습니다. 다음 정책 예제에서는 Windows Server 2012 R2 Datacenter Virtual Machines만 만드는 것을 허용합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
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

와일드 카드를 사용하여 모든 Windows Server Datacenter 이미지를 허용하도록 이전 정책을 수정합니다.

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

정책 필드에 대한 자세한 내용은 [정책 별칭](../../azure-resource-manager/resource-manager-policy.md#aliases)을 참조하세요.

## <a name="define-policy-for-using-managed-disks"></a>관리 디스크 사용을 위한 정책 정의

관리 디스크 사용을 요구하려면 다음 정책을 사용합니다.

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 포털을 통해 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](../../azure-resource-manager/resource-manager-policy-portal.md)를 참조하세요. REST API, PowerShell 또는 Azure CLI를 통해 정책을 할당하려면 [스크립트를 통해 정책 할당 및 관리](../../azure-resource-manager/resource-manager-policy-create-assign.md)를 참조하세요.
* 리소스 정책에 대한 소개는 [리소스 정책 개요](../../azure-resource-manager/resource-manager-policy.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](../../azure-resource-manager/resource-manager-subscription-governance.md)를 참조하세요.

