---
title: 이 문서에서는 Azure 스택 혜택 및 계획을 업데이트 하는 방법을 배웁니다 | Microsoft Docs
description: 이 문서에서는 기존 Azure 스택 제공 및 계획 보기 및 수정 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238509"
---
# <a name="azure-stack-add-on-plans"></a>Azure 스택 추가 기능 계획
Azure 스택 연산자로 원하는 서비스와 사용자가 구독할 수에 대 한 적용 가능한 할당량 포함 하는 계획을 만듭니다. 이러한 [ *계획 기본* ](azure-stack-create-plan.md) 사용자에 게 제공 해야 하는 핵심 서비스를 포함 하 고 제안 당 한 개의 기본 계획을 하나만 지정할 수 있습니다. 제안을 수정 해야 하는 경우 사용할 수 있습니다 *추가 기능이 계획* 컴퓨터, 저장소, 확장 하는 계획을 수정할 수 있는 또는 초기 제공 하는 기본 계획 할당량을 네트워크입니다. 

하지만 단일 계획에 결합 하는 모든 경우에 따라 최적의 계획 및 추가 기능 계획을 사용 하는 추가 서비스를 제공 하는 기본을 사용할 하는 것이 좋습니다. 예를 들어, 모든 PaaS 서비스 계획 추가 기능으로 처리 된 기본 계획의 일환으로 IaaS 서비스를 제공 하도록 결정할 수 있습니다. Azure 스택 환경에서 리소스의 사용을 제어 하 계획을 사용할 수도 있습니다. 예를 들어 모든 사용자가 리소스 사용에 주의 해야 합니다 (필요한 서비스)에 따라 비교적 작은 기본 계획이 있을 수 있습니다 및 사용자가 용량에 도달 하는 대로 하는 경고를 발생 시킬 리소스 할당 사용 이미 했으며 자신의 할당 된 계획에 기반 합니다. 사용자는 여기에서 추가 리소스에 대 한 사용 가능한 추가 기능 계획을 선택할 수 있습니다. 

> [!NOTE]
> 사용자는 추가 기능이 계획에 추가 하는 기존 제공 구독 추가 리소스 표시 한 시간 걸릴 수입니다. 

## <a name="create-an-add-on-plan"></a>추가 기능 계획 만들기
기존 제안을 수정 하 여 추가 기능 계획 생성 됩니다.

1. 클라우드 관리자 권한으로 Azure 스택 관리자 포털에 로그인 합니다.
2. 에 사용 되는 동일한 단계에 따라 [새 기본 계획](azure-stack-create-plan.md) 이전에 제공 되지 않은 서비스를 제공 하는 새 계획을 만듭니다. 이 예제에서는 키 자격 증명 모음 (Microsoft.KeyVault) 서비스를 새 계획에 포함 됩니다.
3. 관리자 포털에서 클릭 **제공** 한 다음 제안 하는 추가 기능 계획으로 업데이트를 선택 합니다.

   ![](media/create-add-on-plan/1.PNG)

4.  제공 속성의 아래쪽으로 스크롤하여 선택 **추가 기능이 계획**합니다. **추가**를 클릭합니다.
   
    ![](media/create-add-on-plan/2.PNG)

5. 추가할 계획을 선택 합니다. 이 예제에서는 계획 이라고 **키 자격 증명 모음 계획**, 클릭 하 고 **선택** 제품에 계획을 추가 하려면. 계획 제품에 성공적으로 추가 된 알림을 받게 됩니다.
   
    ![](media/create-add-on-plan/3.PNG)

6. 새 추가 기능 계획 있는지 확인 하려면 제품에 포함 된 계획이 나열 된 추가 기능 목록을 검토 합니다.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>다음 단계
[제품 만들기](azure-stack-create-offer.md)