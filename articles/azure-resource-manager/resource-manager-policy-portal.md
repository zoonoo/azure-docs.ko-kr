---
title: "리소스 정책을 위한 Azure Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 Resource Manager 정책을 만들고 관리하는 방법을 설명합니다. 구독 또는 리소스 그룹에 정책을 적용할 수 있습니다."
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
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Azure Portal을 사용하여 리소스 정책 할당 및 관리
Azure Portal에서는 리소스 그룹 및 구독에 리소스 정책을 할당할 수 있습니다. 사용자 인터페이스를 사용하면 할당하려는 정책을 쉽게 선택하고, 해당 정책의 매개 변수 값을 지정하여 정책 설정을 사용자 지정할 수 있습니다. 

포털을 통해 정책을 할당하려면 정책 정의가 구독에 이미 있어야 합니다. 구독에는 리소스 그룹 또는 구독에 할당할 수 있는 몇 가지 기본 제공 정책 정의가 있습니다. 이러한 기본 제공 정책 및 포털을 사용하여 정책을 할당할 때 정의한 사용자 지정 정책이 표시됩니다. 정책에 대한 설명 및 사용자 지정된 정책을 정의하는 방법은 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.

정책은 모든 자식 리소스에 의해 상속됩니다. 이에 따라 리소스 그룹에 정책을 적용하면 해당 리소스 그룹의 모든 리소스에 해당 정책을 적용할 수 있습니다. 이 문서에서 용어 **범위**는 정책이 할당된 리소스 그룹 또는 구독을 나타냅니다. 

리소스(PUT 및 PATCH 작업)를 만들고 업데이트할 때 정책이 평가됩니다.

## <a name="assign-a-policy"></a>정책 할당

1. 리소스 그룹 또는 구독에 정책을 할당하려면 해당 리소스 그룹 또는 구독을 선택합니다. 설정에서 **정책**을 선택합니다.

   ![정책 선택](./media/resource-manager-policy-portal/select-policies.png)

2. 이 범위에 대한 정책 할당을 만들려면 **할당 추가**를 선택합니다.

   ![할당 추가](./media/resource-manager-policy-portal/add-assignment.png)

3. 할당하려는 정책을 선택합니다. 구독에 정책 정의를 추가하지 않은 경우에도 할당에 사용할 수 있는 기본 제공 정책이 표시됩니다. 이러한 기본 제공 정책은 여러 일반적인 시나리오에 사용할 수 있습니다.

   ![정의 선택](./media/resource-manager-policy-portal/select-definition.png)

4. 정책을 선택한 후 정책에 대한 설명과 해당 정책에 대한 매개 변수를 볼 수 있습니다. 예를 들어 다음 그림에서는 사용 가능한 위치를 제한하는 정책에 필요한 **허용 위치** 매개 변수를 보여 줍니다.

   ![매개 변수 표시](./media/resource-manager-policy-portal/show-parameters.png)

5. 사용자 인터페이스를 통해 정책 매개 변수(예: 배포에 사용할 수 있는 위치)에 지정할 값을 선택합니다.

   ![매개 변수 값 선택](./media/resource-manager-policy-portal/select-parameters.png)

6. 다른 매개 변수의 값을 제공합니다. 범위는 정책 할당을 시작할 때 선택한 블레이드에 따라 자동으로 할당됩니다. 완료되면 **확인** 을 선택합니다.

   ![매개 변수 정의](./media/resource-manager-policy-portal/define-parameters.png)

  지정된 범위에 정책을 할당했습니다.

## <a name="view-policy-assignments"></a>정책 할당 보기

정책을 할당하면 해당 범위에 대한 정책 목록에 표시됩니다. **세부 정보** 탭에 정책 할당의 요약이 표시됩니다.

![세부 정보 표시](./media/resource-manager-policy-portal/show-details.png)

**할당 규칙** 탭에 정책 정의 대한 JSON이 표시됩니다.

![할당 규칙 표시](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>기존 정책 할당 변경

정책을 변경하려면 **할당 편집** 또는 **삭제**를 선택합니다.

![할당 편집 또는 삭제](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>사용자 지정 정책 할당

구독에서 사용자 지정 정책을 정의하면 해당 정책은 포털을 통해 할당에 사용할 수 있습니다. 이러한 정책은 앞에 **[Custom]**이 표시됩니다.

![사용자 지정 정책](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>다음 단계
* 정책 정의를 위한 JSON 구문에 대해 알아보려면 [리소스 정책 개요](resource-manager-policy.md)를 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](resource-manager-subscription-governance.md)를 참조하세요.
* 정책 스키마는 [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json)에 게시되어 있습니다. 

