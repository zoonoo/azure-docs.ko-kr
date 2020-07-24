---
title: Azure Security Center의 적응 네트워크 강화 | Microsoft Docs
description: 실제 트래픽 패턴을 사용 하 여 NSG (네트워크 보안 그룹) 규칙을 강화 하 고 보안 상태를 강화 하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: d12df01fe1506f7f5ade9cce60ae7af0412e3010
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080814"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure Security Center의 적응 네트워크 강화
Azure Security Center에서 적응 네트워크 강화를 구성 하는 방법에 대해 알아봅니다.

## <a name="what-is-adaptive-network-hardening"></a>적응 네트워크 강화 란?
[NSG (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview) 를 적용 하 여 리소스로 들어오고 나가는 트래픽을 필터링 하 여 네트워크 보안 상태를 향상 시킵니다. 그러나 NSG를 통과 하는 실제 트래픽이 정의 된 NSG 규칙의 하위 집합인 경우도 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화 하 여 보안 상태를 더욱 향상 시킬 수 있습니다.

적응 네트워크 강화는 NSG 규칙을 강화 하기 위한 권장 사항을 제공 합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상에 대 한 요소에 해당 하는 기계 학습 알고리즘을 사용 하 고 특정 IP/포트 튜플의 트래픽만 허용 하는 권장 사항을 제공 합니다.

예를 들어 기존 NSG 규칙은 포트 22에서 140.20.30.10/24의 트래픽을 허용 하는 것 이라고 가정 합니다. 적응 네트워크 강화의 권장 사항은 분석에 따라 범위를 좁히고 140.23.30.10/29의 트래픽을 허용 하는 것입니다 .이는 더 좁은 IP 범위 이며 해당 포트에 대 한 다른 모든 트래픽을 거부 합니다.

>[!TIP]
> 적응 네트워크 강화 권장 사항은 다음의 특정 포트 (UDP 및 TCP 모두)에서 지원 됩니다. 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![네트워크 강화 보기](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>적응 네트워크 강화 경고 및 규칙 보기

1. Security Center에서 **네트워킹**  ->  **적응 네트워크 강화**를 선택 합니다. 네트워크 Vm은 3 개의 별도 탭에 나열 됩니다.
   * **비정상 리소스**: 적응 네트워크 강화 알고리즘을 실행 하 여 현재 권장 사항 및 경고가 트리거된 vm입니다. 
   * **정상 리소스**: 경고 및 권장 사항이 없는 vm입니다.
   * **검색 되지 않은 리소스**: 적응 네트워크 강화 알고리즘을 실행할 수 없는 vm은 다음 이유 중 하나로 인해 실행할 수 없습니다.
      * **Vm은 클래식 vm**입니다. Azure Resource Manager vm만 지원 됩니다.
      * **충분 한 데이터를 사용할 수 없습니다**. 정확한 트래픽 강화 권장 사항을 생성 하기 위해 Security Center에는 최소 30 일의 트래픽 데이터가 필요 합니다.
      * **VM은 ASC standard로 보호 되지 않습니다**. Security Center 표준 가격 책정 계층으로 설정 된 vm만이이 기능을 사용할 수 있습니다.

     ![비정상 리소스](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. **비정상 리소스** 탭에서 VM을 선택 하 여 경고를 확인 하 고 권장 강화 규칙을 적용 합니다.

    ![경고 강화](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>적응 네트워크 강화 권장 규칙 검토 및 적용

1. **비정상 리소스** 탭에서 VM을 선택 합니다. 경고 및 권장 강화 규칙이 나열 됩니다.

     ![규칙 강화](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **규칙** 탭에는 적응 네트워크 강화에서 추가 하는 것이 권장 되는 규칙이 나열 됩니다. **경고** 탭에는 리소스로 이동 하 고, 권장 규칙에서 허용 되는 IP 범위 내에 없는 트래픽으로 인해 생성 된 경고가 나열 됩니다.

2. 규칙의 일부 매개 변수를 변경 하려면 [규칙 수정](#modify-rule)에 설명 된 대로 규칙을 수정할 수 있습니다.
   > [!NOTE]
   > 규칙을 [삭제](#delete-rule) 하거나 [추가할](#add-rule) 수도 있습니다.

3. NSG에 적용 하려는 규칙을 선택 하 고 **적용**을 클릭 합니다.

      > [!NOTE]
      > 적용 된 규칙이 VM을 보호 하는 NSG에 추가 됩니다. VM은 해당 NIC 또는 VM이 상주 하는 서브넷에 연결 된 NSG에 의해 보호 될 수 있습니다.

    ![규칙 적용](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>규칙 <a name ="modify-rule"> </a> 수정

권장 되는 규칙의 매개 변수를 수정할 수 있습니다. 예를 들어 권장 되는 IP 범위를 변경할 수 있습니다.

적응 네트워크 강화 규칙을 수정 하기 위한 몇 가지 중요 한 지침은 다음과 같습니다.

* "허용" 규칙의 매개 변수만 수정할 수 있습니다. 
* "허용" 규칙을 "거부" 규칙이 되도록 변경할 수 없습니다. 

  > [!NOTE]
  > "거부" 규칙 만들기 및 수정은 NSG에서 직접 수행 됩니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)를 참조 하세요.

* **모든 트래픽 거부** 규칙은 여기에 나열 되어 있으며 수정할 수 없는 "거부" 규칙의 유일한 유형입니다. 그러나 삭제할 수는 있습니다 ( [규칙 삭제](#delete-rule)참조).
  > [!NOTE]
  > **모든 트래픽 거부** 규칙은 알고리즘을 실행 한 결과로 기존 nsg 구성에 따라 허용 되어야 하는 트래픽을 식별 하지 Security Center는 경우에 권장 됩니다. 따라서 권장 규칙은 지정 된 포트에 대 한 모든 트래픽을 거부 하는 것입니다. 이 유형의 규칙 이름은 "*시스템 생성*"으로 표시 됩니다. 이 규칙을 적용 한 후에는 NSG의 실제 이름이 프로토콜, 트래픽 방향, "거부" 및 임의 숫자로 구성 된 문자열이 됩니다.

*적응 네트워크 강화 규칙을 수정 하려면:*

1. 규칙의 일부 매개 변수를 수정 하려면 **규칙 탭에서** 규칙 행의 끝에 있는 점 세 개 (...)를 클릭 하 고 **편집**을 클릭 합니다.

   ![규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. **규칙 편집** 창에서 변경 하려는 세부 정보를 업데이트 하 고 **저장**을 클릭 합니다.

   > [!NOTE]
   > **저장**을 클릭 하면 규칙이 성공적으로 변경 된 것입니다. *그러나 NSG에 적용 하지 않았습니다.* 이를 적용 하려면 목록에서 규칙을 선택 하 고 **적용** 을 클릭 해야 합니다 (다음 단계에서 설명).

   ![규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 업데이트 된 규칙을 적용 하려면 목록에서 업데이트 된 규칙을 선택 하 고 **적용**을 클릭 합니다.

    ![규칙 적용](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>새 규칙 <a name ="add-rule"> </a> 추가

Security Center에서 권장 하지 않은 "허용" 규칙을 추가할 수 있습니다.

> [!NOTE]
> 여기에는 "허용" 규칙만 추가할 수 있습니다. "거부" 규칙을 추가 하려면 NSG에서 직접 수행할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)를 참조 하세요.

*적응 네트워크 강화 규칙을 추가 하려면:*

1. **규칙 추가** (왼쪽 위 모서리에 있음)를 클릭 합니다.

   ![규칙 추가](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. **새 규칙** 창에서 세부 정보를 입력 하 고 **추가**를 클릭 합니다.

   > [!NOTE]
   > **추가**를 클릭 하면 규칙을 성공적으로 추가 하 고 다른 권장 규칙과 함께 나열 됩니다. 그러나 NSG에는 적용 하지 않았습니다. 이를 활성화 하려면 목록에서 규칙을 선택 하 고 **적용** 을 클릭 해야 합니다 (다음 단계에서 설명).

3. 새 규칙을 적용 하려면 목록에서 새 규칙을 선택 하 고 **적용**을 클릭 합니다.

    ![규칙 적용](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>규칙 <a name ="delete-rule"> </a> 삭제

필요한 경우 현재 세션에 대 한 권장 규칙을 삭제할 수 있습니다. 예를 들어 제안 된 규칙을 적용 하면 합법적인 트래픽을 차단할 수 있음을 확인할 수 있습니다.

*현재 세션에 대 한 적응 네트워크 강화 규칙을 삭제 하려면 다음을 수행 합니다.*

1. **규칙** 탭에서 규칙 행의 끝에 있는 점 세 개 (...)를 클릭 하 고 **삭제**를 클릭 합니다.  

    ![규칙 강화](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)