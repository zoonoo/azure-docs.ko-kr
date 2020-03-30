---
title: Azure 보안 센터의 적응형 네트워크 강화 | 마이크로 소프트 문서
description: 실제 트래픽 패턴을 사용하여 NSG(네트워크 보안 그룹) 규칙을 강화하고 보안 상태를 더욱 개선하는 방법을 알아봅니다.
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
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385081"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Azure 보안 센터의 적응형 네트워크 강화
Azure 보안 센터에서 적응형 네트워크 강화를 구성하는 방법에 대해 알아봅니다.

## <a name="what-is-adaptive-network-hardening"></a>적응형 네트워크 강화란 무엇입니까?
[NSG(네트워크 보안 그룹)를](https://docs.microsoft.com/azure/virtual-network/security-overview) 적용하여 리소스에서 트래픽을 필터링하면 네트워크 보안 태세가 향상됩니다. 그러나 NSG를 통해 흐르는 실제 트래픽이 정의된 NSG 규칙의 하위 집합인 경우도 있을 수 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화하여 보안 상태를 더욱 개선할 수 있습니다.

적응형 네트워크 강화는 NSG 규칙을 더욱 강화하기 위한 권장 사항을 제공합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상 지표를 고려한 기계 학습 알고리즘을 사용한 다음 특정 IP/포트 tuples에서만 트래픽을 허용하는 권장 사항을 제공합니다.

예를 들어 기존 NSG 규칙은 포트 22에서 140.20.30.10/24의 트래픽을 허용하는 것이라고 가정해 보겠습니다. 분석에 기반한 적응형 네트워크 Hardening의 권장 사항은 범위를 좁혀 140.23.30.10/29에서 트래픽을 허용하는 것이며, 이는 더 좁은 IP 범위이며 해당 포트에 대한 다른 모든 트래픽을 거부하는 것입니다.

>[!TIP]
> 적응형 네트워크 강화 권장 사항은 UDP 및 TCP 모두에 대해 다음과 같은 특정 포트에서만 지원됩니다( 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 143, 143, 1434, 290, 290, 290, 290, 290, 290, 135, 135, 137, 138, 139, 161, 162 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![네트워크 강화 보기](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>적응형 네트워크 강화 경고 및 규칙 보기

1. 보안 센터에서 **네트워킹** -> **적응형 네트워크 강화를 선택합니다.** 네트워크 VM은 세 개의 별도 탭 아래에 나열됩니다.
   * **비정상 리소스**: 현재 적응 형 네트워크 강화 알고리즘을 실행하여 트리거 된 권장 사항 및 경고가있는 VM입니다. 
   * **정상 리소스**: 경고 및 권장 사항이 없는 VM.
   * **스캔되지 않은 리소스**: 다음 이유 중 하나로 인해 적응 네트워크 강화 알고리즘을 실행할 수 없는 VM:
      * **VM은 클래식 VM입니다:** Azure 리소스 관리자 VM만 지원됩니다.
      * **사용할 수 있는 데이터가 충분하지 않음:** 정확한 트래픽 강화 권장 사항을 생성하기 위해 보안 센터는 최소 30일 이상의 트래픽 데이터가 필요합니다.
      * **VM은 ASC 표준에 의해 보호되지 않습니다:** 보안 센터의 표준 가격 책정 계층으로 설정된 VM만 이 기능을 사용할 수 있습니다.

     ![비정상 리소스](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. **비정상 리소스** 탭에서 VM을 선택하여 경고 및 적용할 권장 강화 규칙을 봅니다.

    ![경고 강화](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>적응형 네트워크 강화 권장 규칙 검토 및 적용

1. **비정상 리소스** 탭에서 VM을 선택합니다. 경고 및 권장 강화 규칙이 나열됩니다.

     ![규칙 강화](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > **규칙** 탭에는 적응형 네트워크 강화가 추가하는 것이 권장하는 규칙이 나열되어 있습니다. **경고** 탭에는 권장 규칙에서 허용되는 IP 범위 내에 있지 않은 리소스로 흐르는 트래픽으로 인해 생성된 경고가 나열됩니다.

2. 규칙의 매개 변수 중 일부를 변경하려면 [규칙 수정에](#modify-rule)설명된 대로 규칙을 수정할 수 있습니다.
   > [!NOTE]
   > 규칙을 [삭제하거나](#delete-rule) [추가할](#add-rule) 수도 있습니다.

3. NSG에 적용할 규칙을 선택하고 **적용을**클릭합니다.

      > [!NOTE]
      > 적용된 규칙은 VM을 보호하는 NSG에 추가됩니다. (VM은 NIC에 연결된 NSG 또는 VM이 상주하는 서브넷 또는 둘 다에 의해 보호될 수 있습니다.)

    ![규칙 적용](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>규칙 <a name ="modify-rule"> </a> 수정

권장된 규칙의 매개 변수를 수정할 수 있습니다. 예를 들어 권장 IP 범위를 변경할 수 있습니다.

적응형 네트워크 강화 규칙을 수정하기 위한 몇 가지 중요한 지침:

* "허용" 규칙의 매개변수만 수정할 수 있습니다. 
* "허용" 규칙을 "거부" 규칙으로 변경할 수 없습니다. 

  > [!NOTE]
  > "거부" 규칙을 만들고 수정하는 작업은 NSG에서 직접 수행됩니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)참조하십시오.

* **모든 트래픽 거부** 규칙은 여기에 나열된 유일한 "거부" 규칙 유형이며 수정할 수 없습니다. 그러나 삭제할 수 [있습니다(규칙 삭제](#delete-rule)참조).
  > [!NOTE]
  > 모든 **트래픽 거부** 규칙은 알고리즘을 실행한 결과 보안 센터에서 기존 NSG 구성에 따라 허용해야 하는 트래픽을 식별하지 않는 경우 권장됩니다. 따라서 권장되는 규칙은 지정된 포트에 대한 모든 트래픽을 거부하는 것입니다. 이러한 유형의 규칙의 이름은 *"시스템 생성"으로*표시됩니다. 이 규칙을 적용한 후 NSG의 실제 이름은 프로토콜, 트래픽 방향, "DENY" 및 난수로 구성된 문자열이 됩니다.

*적응형 네트워크 강화 규칙을 수정하려면 다음을 수행하십시오.*

1. 규칙 탭에서 규칙의 매개 변수 중 일부를 수정하려면 **규칙** 행 끝에 있는 세 개의 점(...)을 클릭하고 **편집을**클릭합니다.

   ![규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. 규칙 **편집** 창에서 변경하려는 세부 정보를 업데이트하고 **저장을**클릭합니다.

   > [!NOTE]
   > **저장을**클릭한 후 규칙을 성공적으로 변경했습니다. *그러나 NSG에 적용 하지 않았습니다.* 이를 적용하려면 목록에서 규칙을 선택하고 **적용을** 클릭해야 합니다(다음 단계에서 설명된 대로).

   ![규칙 편집](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. 업데이트된 규칙을 적용하려면 목록에서 업데이트된 규칙을 선택하고 **적용을**클릭합니다.

    ![적용 규칙](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>새 규칙 <a name ="add-rule"> </a> 추가

보안 센터에서 권장하지 않은 "허용" 규칙을 추가할 수 있습니다.

> [!NOTE]
> 여기서는 "허용" 규칙만 추가할 수 있습니다. "거부" 규칙을 추가하려면 NSG에서 직접 규칙을 추가할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)참조하십시오.

*적응형 네트워크 강화 규칙을 추가하려면 다음을 수행하십시오.*

1. 왼쪽 상단 모서리에 있는 **규칙 추가를** 클릭합니다.

   ![규칙 추가](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. 새 **규칙** 창에서 세부 정보를 입력하고 **추가 를**클릭합니다.

   > [!NOTE]
   > **추가를**클릭한 후 규칙을 성공적으로 추가했으며 다른 권장 규칙과 함께 나열됩니다. 그러나 NSG에 적용 하지 않았습니다. 활성화하려면 목록에서 규칙을 선택하고 **적용을** 클릭해야 합니다(다음 단계에서 설명한 대로).

3. 목록에서 새 규칙을 적용하려면 새 규칙을 선택하고 **적용을**클릭합니다.

    ![적용 규칙](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>규칙 <a name ="delete-rule"> </a> 삭제

필요한 경우 현재 세션에 권장되는 규칙을 삭제할 수 있습니다. 예를 들어 제안된 규칙을 적용하면 합법적인 트래픽이 차단될 수 있다고 판단할 수 있습니다.

*현재 세션에 대한 적응형 네트워크 강화 규칙을 삭제하려면 다음을 수행하십시오.*

1. **규칙** 탭에서 규칙 행 끝에 있는 세 개의 점(...)을 클릭하고 **삭제를**클릭합니다.  

    ![규칙 강화](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)