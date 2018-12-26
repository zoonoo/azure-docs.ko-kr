---
title: 이 문서에서는 Azure Stack 제품 및 계획을 업데이트 하는 방법을 알아봅니다 | Microsoft Docs
description: 이 문서에서는 기존 Azure Stack 제품 및 계획 보기 및 수정 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 07/30/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: f6b85d2197a4db9cdfc526e5857e4b6ce707beb2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076794"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack 추가 요금제

Azure Stack 운영자를 수정 하려면 추가 기능 계획을 만들를 [ *기본 계획* ](azure-stack-create-plan.md) 추가 서비스를 제공 하거나 확장 하려는 경우 *컴퓨터*, *저장소* , 또는 *네트워크* 할당량이 기본 계획 초기 제안 초과 합니다. 추가 요금제는 기본 계획을 수정 하 고는 사용자 구독을 선택할 수 있는 선택적 확장. 

단일 계획에서 결합 하는 모든 경우 최적의 경우가 있습니다. 다른 자료를 계획 하 고 다음 추가 요금제를 사용 하 여 추가 서비스를 제공 해야 할 수 있습니다. 예를 들어 기본 계획의 일부로 추가 요금제로 처리 하는 모든 PaaS 서비스를 사용 하 여 IaaS 서비스를 제공 하도록 결정할 수 있습니다.

추가 요금제를 사용 하는 이유는 리소스 사용에 주의 해야 하는 사용자를 돕는 것입니다. 이렇게 하려면 (필요한 서비스)에 따라 비교적 작은 할당량을 포함 하는 기본 계획을 사용 하 여 시작할 수 있습니다. 그런 다음 사용자 용량에 도달 하면, 이러한는 경고는 사용한 자신의 할당 된 계획에 따라 리소스를 할당 하는. 여기에서 사용자는 추가 리소스를 제공 하는 추가 요금제를 선택한 수 없습니다.

> [!NOTE]
> 할당량을 확장 하는 추가 요금제를 사용 하지 않으려는 경우에 수도 있습니다 [할당량의 원래 구성 편집](azure-stack-quota-types.md#to-edit-a-quota)합니다. 

기존 제품 구독에는 추가 요금제를 추가 하는 사용자, 추가 리소스를 표시 한 시간 걸릴 수 있습니다. 

## <a name="create-an-add-on-plan"></a>추가 기능 계획 만들기
추가 요금제는 기존 제품을 수정 하 여 생성 됩니다.

1. 클라우드 관리자는 Azure Stack 관리자 포털에 로그인 합니다.
2. 하는 데 동일한 단계를 수행 [기본 계획을 새로 만들려면](azure-stack-create-plan.md) 된 이전에 제공 되는 서비스를 제공 합니다. 새 계획을 만듭니다. 이 예제에서는 키 자격 증명 모음 (Microsoft.KeyVault) 서비스를 새 계획에 포함 됩니다.
3. 관리자 포털에서 클릭 **제공** 한 다음 추가 기능 계획으로 업데이트 하는 제품을 선택 합니다.

   ![](media/create-add-on-plan/1.PNG)

4.  선택한 제품 속성의 아래쪽으로 스크롤하여 **추가 기능 계획**합니다. **추가**를 클릭합니다.
   
    ![](media/create-add-on-plan/2.PNG)

5. 추가할 계획을 선택 합니다. 이 예제에서는 계획 이라고 **키 자격 증명 모음 계획**합니다. 계획을 선택한 후 클릭 **선택** 계획 제품에 추가 합니다. 계획 제품에 성공적으로 추가 된 알림을 받게 됩니다.
   
    ![](media/create-add-on-plan/3.PNG)

6. 새 추가 기능 계획을 확인 하는 제품에 포함 된 계획이 나열 된 추가 기능 목록을 검토 합니다.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>다음 단계
[제안 만들기](azure-stack-create-offer.md)