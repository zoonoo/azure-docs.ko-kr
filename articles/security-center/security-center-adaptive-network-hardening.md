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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: e6bb3389fe035b1ccfbefaca788a40530581ac7a
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851068"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure Security Center의 적응 네트워크 강화
Security Center에서 적응 네트워크 강화를 구성 하는 방법에 대해 알아봅니다.

## <a name="availability"></a>가용성
|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md) 필요|
|필요한 역할 및 권한:|머신의 NSG에 대한 쓰기 권한|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="what-is-adaptive-network-hardening"></a>적응 네트워크 강화 란?
[NSG (네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/security-overview) 를 적용 하 여 리소스로 들어오고 나가는 트래픽을 필터링 하 여 네트워크 보안 상태를 향상 시킵니다. 그러나 NSG를 통과하는 실제 트래픽이 정의된 NSG 규칙의 하위 집합인 경우도 있을 수 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화하여 보안 상태를 더욱 개선할 수 있습니다.

적응 네트워크 강화는 NSG 규칙을 강화 하기 위한 권장 사항을 제공 합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상 지표를 고려하는 기계 학습 알고리즘을 사용한 후 특정 IP/포트 튜플의 트래픽만 허용하는 권장 사항을 제공합니다.

예를 들어 기존 NSG 규칙은 포트 22에서 140.20.30.10/24의 트래픽을 허용 하는 것 이라고 가정 합니다. 트래픽 분석에 따라 적응 네트워크 보안 강화는 140.23.30.10/29의 트래픽을 허용 하도록 범위를 축소 하 고 해당 포트에 대 한 다른 모든 트래픽을 거부 하는 것이 좋습니다.

>[!Note]
> 적응 네트워크 강화 권장 사항은 다음의 특정 포트 (UDP 및 TCP 모두)에서 지원 됩니다. 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>경고 및 규칙 강화 보기 및 관리

1. Security Center의 메뉴에서 **Azure Defender** 대시보드를 열고 적응 네트워크 강화 타일 (1) 또는 적응 네트워크 강화 (2)와 관련 된 insights 패널 항목을 선택 합니다. 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="적응 네트워크 강화 도구 액세스" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Insights 패널에는 현재 적응 네트워크 강화로 방어 Vm의 백분율이 표시 됩니다. 

1. 적응 네트워크 강화 권장 사항에 대 한 세부 정보 페이지는 네트워크 Vm이 세 개의 탭으로 그룹화 된 **인터넷 연결 virtual machines** 권장 사항에 적용 됩니다.
   * **비정상 리소스**: 적응 네트워크 강화 알고리즘을 실행 하 여 현재 권장 사항 및 경고가 트리거된 vm입니다. 
   * **정상 리소스**: 경고 및 권장 사항이 없는 vm입니다.
   * **검색 되지 않은 리소스**: 적응 네트워크 강화 알고리즘을 실행할 수 없는 vm은 다음 이유 중 하나로 인해 실행할 수 없습니다.
      * **Vm은 클래식 vm**입니다. Azure Resource Manager vm만 지원 됩니다.
      * **충분 한 데이터를 사용할 수 없습니다**. 정확한 트래픽 강화 권장 사항을 생성 하기 위해 Security Center에는 최소 30 일의 트래픽 데이터가 필요 합니다.
      * **VM은 Azure defender에 의해 보호 되지 않습니다**. [서버에 대해 azure defender](defender-for-servers-introduction.md) 로 보호 되는 vm만이 기능을 사용할 수 있습니다.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="적응 네트워크 강화 도구 액세스":::

1. **비정상 리소스** 탭에서 VM을 선택 하 여 경고를 확인 하 고 권장 강화 규칙을 적용 합니다.

    - **규칙** 탭에는 적응 네트워크 강화에서 추가 하는 것이 권장 되는 규칙이 나열 됩니다.
    - **경고** 탭에는 리소스로 이동 하 고, 권장 규칙에서 허용 되는 IP 범위 내에 없는 트래픽으로 인해 생성 된 경고가 나열 됩니다.

1. 필요에 따라 규칙을 편집 합니다.

    - [규칙 수정](#modify-rule)
    - [규칙 삭제](#delete-rule) 
    - [규칙 추가](#add-rule)

3. NSG에 적용 하려는 규칙을 선택 하 고 **적용**을 클릭 합니다.

    > [!TIP]
    > 허용 되는 원본 IP 범위가 ' 없음 '으로 표시 되는 경우 권장 되는 규칙이 *거부* 규칙 임을 의미 합니다. 그렇지 않은 경우 *허용* 규칙입니다.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="적응 네트워크 강화 도구 액세스":::

      > [!NOTE]
      > 적용 된 규칙이 VM을 보호 하는 NSG에 추가 됩니다. VM은 해당 NIC 또는 VM이 상주 하는 서브넷에 연결 된 NSG에 의해 보호 될 수 있습니다.

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

   ![S 규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. **규칙 편집** 창에서 변경 하려는 세부 정보를 업데이트 하 고 **저장**을 클릭 합니다.

   > [!NOTE]
   > **저장**을 클릭 하면 규칙이 성공적으로 변경 된 것입니다. *그러나 NSG에 적용 하지 않았습니다.* 이를 적용 하려면 목록에서 규칙을 선택 하 고 다음 단계에 설명 된 대로 **적용** 을 선택 해야 합니다.

   ![저장 선택](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

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

    ![규칙 삭제](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)