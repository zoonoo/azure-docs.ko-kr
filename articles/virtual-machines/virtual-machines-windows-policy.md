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
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: 83470aaf607a666a63bae5b9897d0b273e327377


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Windows VM에 보안 및 정책 적용
조직은 정책을 사용하여 엔터프라이즈 전체에 다양한 규칙을 적용할 수 있습니다. 원하는 동작을 적용하여 조직의 성공에 기여함과 동시에 위험을 완화할 수 있습니다. 이 문서에서는 Azure Resource Manager 정책을 사용하여 조직의 가상 컴퓨터에 대해 원하는 동작을 정의하는 방법을 설명합니다.

이 작업을 수행하는 개략적인 단계는 다음과 같습니다.

1. Azure Resource Manager 정책 101
2. 가상 컴퓨터에 대한 정책 정의
3. 정책 만들기
4. 정책 적용

## <a name="azure-resource-manager-policy-101"></a>Azure Resource Manager 정책 101
Azure Resource Manager 정책을 시작하려면 아래 문서를 읽은 다음 문서에 있는 단계를 계속하는 것이 좋습니다. 아래 문서에서 정책의 기본 정의 및 구조, 정책 평가 방법을 설명하고 정책 정의의 다양한 예제를 제공합니다.

* [정책을 사용하여 리소스 및 컨트롤 액세스 관리](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>가상 컴퓨터에 대한 정책 정의
엔터프라이즈에 대한 일반적인 시나리오 중 하나는 LOB 응용 프로그램과 호환되도록 테스트된 특정 운영 체제에서 사용자에게만 가상 컴퓨터를 만들 수 있도록 하는 것일 수 있습니다. Azure Resource Manager 정책을 사용하여 몇 단계만으로 이 작업을 수행할 수 있습니다.
이 정책 예제에서는 Windows Server 2012 R2 Datacenter 가상 컴퓨터만 만드는 것을 허용합니다. 정책 정의는 아래와 같습니다.

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

위의 정책은 아래 변경으로 가상 컴퓨터 배포에 모든 Windows Server Datacenter 이미지를 사용할 수 있도록 하는 시나리오로 쉽게 수정할 수 있습니다.

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>가상 컴퓨터 속성 필드
아래 표에서 정책 정의의 필드로 사용할 수 있는 가상 컴퓨터 속성에 대해 설명합니다. 정책 필드에 대한 자세한 내용은 아래 문서를 참조하세요.

* [필드 및 소스](../azure-resource-manager/resource-manager-policy.md#conditions)

| 필드 이름 | 설명 |
| --- | --- |
| imagePublisher |이미지 게시자 지정 |
| imageOffer |선택된 이미지 게시자에 대한 제품 지정 |
| imageSku |선택한 제품에 대한 SKU 지정 |
| imageVersion |선택한 SKU에 대한 이미지 버전 지정 |

## <a name="create-the-policy"></a>정책 만들기
정책은 REST API를 직접 사용하거나 PowerShell cmdlet을 사용하여 쉽게 만들 수 있습니다. 정책 만들기에 대한 자세한 내용은 아래 문서를 참조하세요.

* [정책 만들기](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)

## <a name="apply-the-policy"></a>정책 적용
정책을 만든 후 정의된 범위에 적용해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수도 있습니다. 정책 적용에 대한 자세한 내용은 아래 문서를 참조하세요.

* [정책 만들기](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)



<!--HONumber=Jan17_HO4-->


