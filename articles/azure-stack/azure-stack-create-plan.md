---
title: Azure Stack에서 계획 만들기 | Microsoft Docs
description: 클라우드 관리자는 가상 컴퓨터를 프로 비전 하는 구독자 수 있는 계획을 만듭니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 7a3a87c70402374bea6357d1b89a19938ca08868
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54243528"
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack에서 계획 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

[계획](azure-stack-key-features.md)은 하나 이상의 서비스에 대한 그룹화입니다. 공급자 사용자에 게 제공할 계획을 만들 수 있습니다. 차례로 사용자 계획 및 포함 하는 서비스를 사용 하 여 제품을 구독 합니다. 이 예제에서는 계산, 네트워크 및 storage 리소스 공급자를 포함 하는 계획을 만드는 방법을 보여 줍니다. 이 계획 구독자가 가상 머신을 프로 비전 하는 기능을 제공 합니다.

1. 에 로그인 합니다 [Azure Stack 관리자 포털](https://adminportal.local.azurestack.external)합니다.

2. 계획 및 사용자가 구독할 수 있는 제품을 만들려면 선택 **+ 리소스 만들기**, 한 다음 **제공 + 계획**, 한 다음 **계획**.
  
   ![계획 선택](media/azure-stack-create-plan/select-plan.png)

3. 아래 **새 계획**, 입력을 **표시 이름** 와 **리소스 이름**합니다. 표시 이름은 사용자가 볼 수 있는 계획의 친숙 한 이름이입니다. 관리자만 관리자는 Azure 리소스 관리자 리소스로 계획 작업에 사용할 리소스 이름을 볼 수 있습니다.

   ![세부 정보를 지정 합니다.](media/azure-stack-create-plan/plan-name.png)

4. 새 **리소스 그룹**, 또는 기존 계획에 대 한 컨테이너를 선택 합니다.

   ![리소스 그룹을 지정 합니다.](media/azure-stack-create-plan/resource-group.png)

5. 선택 **Services** 에 대 한 확인란을 선택 하 고 **Microsoft.Compute**합니다 **Microsoft.Network**, 및 **Microsoft.Storage**합니다. 그런 다음 선택 **선택** 구성을 저장 합니다. 확인란에는 각 옵션 위로 마우스를 가져가면 표시 합니다.
  
   ![서비스 선택](media/azure-stack-create-plan/services.png)

6. 선택 **할당량**를 **(로컬) Microsoft.Storage**를 선택한 다음 기본 할당량 또는 선택 **새 할당량 만들기** 사용자 지정된 할당량 만들기.
  
   ![할당량](media/azure-stack-create-plan/quotas.png)

7. 새 할당량을 만들 경우 입력을 **이름을** 할당량에 대 한 > 할당량 값을 지정 > 선택 **확인**합니다. 합니다 **만들기 할당량** 대화 상자를 닫습니다.

   ![새 할당량](media/azure-stack-create-plan/new-quota.png)

   만든 새 할당량을 선택 합니다. 할당량을 선택 하 고 할당 하 고 선택 대화 상자를 닫습니다.
  
   ![할당량 할당](media/azure-stack-create-plan/assign-quota.png)

8. 6 및 7 만들기에 대 한 할당량을 할당 하는 단계를 반복 **(로컬) Microsoft.Network** 하 고 **Microsoft.Compute (로컬)** 합니다. 세 가지 서비스 모두에 할당 된 할당량 경우 다음 예제와 같은 모양이 됩니다.

   ![전체 할당량 할당](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 아래 **할당량**, 선택 **확인**를 선택한 후 **새 계획**를 선택 **만들기** 계획을 만들 합니다.

    ![계획 만들기](media/azure-stack-create-plan/create.png)

10. 새 계획을 보려면 선택 **모든 리소스**를 계획 한 다음 검색 하 고 해당 이름을 선택 합니다. 사용 하 여 리소스 목록이 긴 경우 **검색** 이름별 계획을 찾으려고 합니다.

   ![계획 검토](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>다음 단계

* [제안 만들기](azure-stack-create-offer.md)
