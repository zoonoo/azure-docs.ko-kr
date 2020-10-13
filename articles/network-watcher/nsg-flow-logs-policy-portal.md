---
title: 빠른 시작-Azure Policy를 사용 하 여 NSG 흐름 로그 배포 및 관리
titleSuffix: Azure Network Watcher
description: 이 문서에서는 기본 제공 정책을 사용 하 여 NSG 흐름 로그의 배포를 관리 하는 방법을 설명 합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: a603f55bd5bff7b2ed68623e9c712faaf8ac766f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877030"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>빠른 시작: Azure Policy을 사용 하 여 NSG 흐름 로그 배포 및 관리 

## <a name="overview"></a>개요
Azure Policy를 사용하면 조직의 표준을 적용하고 규정 준수를 규모에 맞게 평가할 수 있습니다. Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다. 이 문서에서는 NSG 흐름 로그에 사용할 수 있는 두 가지 기본 제공 정책을 사용 하 여 흐름 로그 설정을 관리할 것입니다. 첫 번째 정책은 흐름 로그가 없는 모든 NSGs에 플래그를 설정 합니다. 두 번째 정책은 흐름 로그를 사용 하지 않고 NSGs에 대 한 흐름 로그를 자동으로 배포 합니다. 

처음으로 Azure 정책을 만드는 경우 다음을 읽을 수 있습니다. 
- [Azure Policy 개요](https://docs.microsoft.com/azure/governance/policy/overview) 
- [정책을 만드는 방법에 대 한 자습서](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal#create-a-policy-assignment)입니다.


## <a name="locate-the-policies"></a>정책 찾기
1. Azure Portal – [portal.azure.com](https://portal.azure.com) 로 이동 합니다. 

위쪽 검색 표시줄 ![ 정책 홈 페이지에서 정책을 검색 하 여 Azure Policy 페이지로 이동 합니다.](./media/network-watcher-builtin-policy/1_policy-search.png)

2. 왼쪽 창에서 **할당** 탭으로 이동

![할당 탭](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. **정책 할당** 단추를 클릭 합니다. 

![정책 할당 단추](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. "정책 정의"에서 세 개의 점 메뉴를 클릭 하 여 사용 가능한 정책을 확인 합니다.

5. 유형 필터를 사용 하 고 "기본 제공"을 선택 합니다. 그런 다음 "흐름 로그"를 검색 합니다.

흐름 로그에 대 한 두 가지 기본 제공 정책 목록이 표시 됩니다. ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. 할당 하려는 정책 선택

- *"모든 네트워크 보안 그룹에 대 한 흐름 로그는 구성 해야 함"* 은 비준수 nsgs에 플래그를 지정 하는 감사 정책입니다 .이 정책은 흐름 로깅이 사용 되지 않는 nsgs입니다.
- *"대상 네트워크 보안 그룹을 사용 하 여 흐름 로그 리소스 배포"* 는 배포 작업을 사용 하는 정책이 며 흐름 로그 없이 모든 nsgs에 대 한 흐름 로그를 사용 하도록 설정 합니다.

각 정책에 대 한 별도의 지침은 다음과 같습니다.  

## <a name="audit-policy"></a>감사 정책 

### <a name="how-the-policy-works"></a>정책의 작동 원리

정책은 "networkSecurityGroups/" 유형의 기존 ARM 개체를 모두 확인 합니다. 즉, 지정 된 범위의 모든 NSGs를 확인 하 고 NSGS의 흐름 로그 속성을 통해 연결 된 흐름 로그의 존재 여부를 확인 합니다. 속성이 없으면 NSG에 플래그가 지정 됩니다.

정책의 전체 정의를 보려는 경우 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) 을 방문 하 여 "흐름 로그"를 검색 하 여 정책을 찾을 수 있습니다.

### <a name="assignment"></a>할당

1. 정책 세부 정보를 입력 합니다.

- 범위: 구독은 일반적인 선택 이며 관리 그룹 또는 리소스 그룹을 사용자와 관련 하 여 선택할 수도 있습니다.  
- 정책 정의: "정책 찾기" 섹션에 표시 된 대로 선택 해야 합니다.
- AssignmentName: 설명이 포함 된 이름을 선택 합니다. 

2. "검토 + 만들기"를 클릭 하 여 할당을 검토 합니다.

정책에는 매개 변수가 필요 하지 않습니다. 감사 정책을 할당할 때 "수정" 탭에 세부 정보를 입력할 필요가 없습니다.  

![감사 정책 검토](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>결과

결과를 확인 하려면 호환성 탭을 열고 할당의 이름을 검색 합니다.
정책이 실행 되 면 다음 스크린샷에 유사한 내용이 표시 됩니다. 정책이 실행 되지 않은 경우 일정 시간 동안 기다립니다. 

![감사 정책 결과](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>배포-존재 하지 않는 정책 

### <a name="policy-structure"></a>정책 구조

정책은 "networkSecurityGroups/" 유형의 기존 ARM 개체를 모두 확인 합니다. 즉, 지정 된 범위의 모든 NSGs를 확인 하 고 NSGS의 흐름 로그 속성을 통해 연결 된 흐름 로그의 존재 여부를 확인 합니다. 속성이 없으면 정책은 흐름 로그를 배포 합니다. 

정책의 전체 정의를 보려는 경우 [정의 탭](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) 을 방문 하 여 "흐름 로그"를 검색 하 여 정책을 찾을 수 있습니다. 

### <a name="assignment"></a>할당

1. 정책 세부 정보를 입력 합니다.

- 범위: 구독은 일반적인 선택 이며 관리 그룹 또는 리소스 그룹을 사용자와 관련 하 여 선택할 수도 있습니다.  
- 정책 정의: "정책 찾기" 섹션에 표시 된 대로 선택 해야 합니다.
- AssignmentName: 설명이 포함 된 이름을 선택 합니다. 

2. 정책 매개 변수 추가 

Network Watcher 서비스는 지역 서비스입니다. 이러한 매개 변수를 통해 실행 되는 흐름 로그를 배포 하는 정책 작업을 수행할 수 있습니다. 
- NSG 지역: 정책을 대상으로 하는 Azure 지역
- 저장소 ID: 저장소 계정의 전체 리소스 ID입니다. 참고:이 저장소 계정은 NSG와 동일한 지역에 있어야 합니다. 
- Network 감시자 RG: Network Watcher 리소스를 포함 하는 리소스 그룹의 이름입니다. 이름을 변경 하지 않은 경우 기본값인 ' NetworkWatcherRG '를 입력할 수 있습니다.
- Network Watcher 이름: 지역 네트워크 감시자 서비스의 이름입니다. 형식: NetworkWatcher_RegionName. 예: NetworkWatcher_centralus. 전체 목록을 참조하세요.

![식사 정책 매개 변수](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 재구성 정보 추가

- 정책이 기존 리소스에 영향을 주려면 "재구성 작업 만들기"에 표시를 선택 합니다. 
- "관리 Id 만들기"가 이미 선택 되어 있어야 합니다.
- 관리 Id에 대해 이전과 동일한 위치를 선택 합니다. 
- 이 정책을 사용 하려면 참가자 또는 소유자 권한이 있어야 합니다. 이러한 권한이 있는 경우 오류가 표시 되지 않습니다.

![식사 정책 수정](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. "검토 + 만들기"를 클릭 하 여 할당을 검토 하 고 다음 스크린샷에서 유사한 내용이 표시 되어야 합니다.

![식사 정책 검토](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>결과

결과를 확인 하려면 호환성 탭을 열고 할당의 이름을 검색 합니다.
정책에 따라 다음과 같은 스크린샷이 표시 됩니다. 정책이 실행 되지 않은 경우 일정 시간 동안 기다립니다.

![식사 정책 결과](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>다음 단계 

-   이 [자습서](https://docs.microsoft.com/azure/network-watcher/quickstart-configure-network-security-group-flow-logs-from-arm-template) 에서는 ARM 템플릿을 사용 하 여 흐름 로그 및 트래픽 분석을 배포 하는 방법에 대해 자세히 살펴보겠습니다.
-   [Network Watcher](https://docs.microsoft.com/azure/network-watcher/) 에 대 한 자세한 정보
