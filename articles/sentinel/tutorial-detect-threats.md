---
title: Azure 센티널 Preview를 사용 하 여 경고 조사 | Microsoft Docs
description: 이 자습서를 사용 하 여 Azure 센티널로 경고를 조사 하는 방법을 알아보세요.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780419"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>자습서: Azure 센티널 Preview를 사용 하 여 위협 감지

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[데이터 원본을](quickstart-onboard.md) Azure 센티널에 연결 하 고 나면 의심 스러운 상황이 발생 하면 알림이 표시 됩니다. 이 작업을 수행할 수 있도록 Azure 센티널을 사용 하면 사용자 환경에서 변칙 및 위협을 깊게 조사 하기 위해 할당 하 고 사용할 수 있는 인시던트를 생성 하는 고급 경고 규칙을 만들 수 있습니다. 

이 자습서는 Azure 센티널로 위협을 검색 하는 데 도움이 됩니다.
> [!div class="checklist"]
> * 검색 규칙 만들기
> * 위협 응답 자동화

## <a name="create-detection-rules"></a>검색 규칙 만들기

인시던트를 조사 하려면 먼저 검색 규칙을 만들어야 합니다. 

> [!NOTE]
> Azure 센티널에서 생성 된 경고는 [Microsoft Graph 보안](https://aka.ms/securitygraphdocs)을 통해 사용할 수 있습니다. 자세한 내용과 통합 파트너는 [Microsoft Graph 보안 경고 설명서](https://aka.ms/graphsecurityreferencebetadocs) 를 참조 하세요.

검색 규칙은 사용자 환경에서 즉시 확인할 수 있는 위협 및 비정상 유형을 기반으로 하며, 표시, 조사 및 재구성을 보장 합니다. 

1. Azure 센티널의 Azure Portal에서 **Analytics**를 선택 합니다.

   ![분석](./media/tutorial-detect-threats/alert-rules.png)

2. 상단 메뉴 모음에서 **+ 추가**를 클릭 합니다.  

   ![경고 규칙 만들기](./media/tutorial-detect-threats/create-alert-rule.png)

3. **경고 규칙 만들기**에서 설명이 포함 된 이름을 입력 하 고 필요에 따라 **심각도** 를 설정 합니다. 

4. Log Analytics에서 쿼리를 만든 다음 **경고 규칙 설정** 필드에 붙여넣습니다. Azure 활동에서 비정상 리소스를 만들 때 경고를 표시 하는 샘플 쿼리는 다음과 같습니다.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > 쿼리 길이는 1 ~ 007e; 1만 자 사이 여야 하며 "search *" 및 "union *"를 포함할 수 없습니다.


5. **엔터티 매핑** 섹션에서 **엔터티 형식** 아래의 필드를 사용 하 여 쿼리의 열을 Azure 센티널에서 인식 하는 엔터티 필드에 매핑합니다. 각 필드에 대해 Log Analytics에서 만든 쿼리의 관련 열을 적절 한 엔터티 필드에 매핑합니다. **속성**에서 관련 열 이름을 선택 합니다. 각 엔터티에는 여러 필드 (예: SID, GUID 등)가 포함 됩니다. 상위 수준 엔터티 뿐만 아니라 필드에 따라 엔터티를 매핑할 수 있습니다.

6. 경고 **트리거**아래에서 경고 트리거 조건을 정의 합니다. 경고를 트리거하는 조건을 정의 합니다. 

7. 쿼리가 실행 되는 빈도를 5 분 마다 자주 또는 하루에 한 번만 설정 합니다. 

8. 쿼리가 실행 되는 데이터의 양을 제어 하는 **기간** 을 설정 합니다. 예를 들어 60 분의 데이터에서 매시간 실행할 수 있습니다.

9. **억제**를 설정할 수도 있습니다. 제거는 동일한 인시던트에 대해 중복 경고가 트리거되지 않도록 하려는 경우에 유용 합니다. 이러한 방식으로 특정 기간 동안 경고가 트리거되는 것을 중지할 수 있습니다. 이를 통해 동일한 인시던트에 대 한 중복 경고를 방지할 수 있으며 일정 시간 동안 연속 된 경고를 표시 하지 않을 수 있습니다. 예를 들어 **경고 예약** **빈도가** 60 분으로 설정 되 고 **경고 예약 기간이** 2 시간으로 설정 되 고 쿼리 결과가 정의 된 임계값을 초과 경우 경고는 처음 검색 될 때 한 번만 트리거됩니다. 최근 60 분 동안 그리고 샘플링 된 데이터의 2 시간 동안 첫 번째 60 분이 될 때 다시 경고가 트리거되는 경우 경고 기간에 설정 된 시간에 대 한 비 표시 제거를 설정 하는 것이 좋습니다. 이 예제에서는 가장 최근 시간에 발생 한 이벤트에 대해서만 경고가 트리거되기 되도록 60 분의 제거를 설정 하는 것이 좋습니다.

8. 쿼리를 **경고 규칙 설정** 필드에 붙여넣은 후에는 경고에 대 한 특정 시간 간격 동안 생성 되는 데이터의 양을 이해할 수 있도록 **논리 경고 시뮬레이션** 에서 경고 시뮬레이션을 즉시 확인할 수 있습니다. 만들었습니다. **빈도** 및 **임계값**에 대해 설정한 내용에 따라 달라 집니다. 평균적으로 경고가 너무 자주 트리거되면 평균 기준선을 초과 하도록 결과 수를 높게 설정 하는 것이 좋습니다.

9. **만들기** 를 클릭 하 여 경고 규칙을 초기화 합니다. 경고가 생성 되 면 경고를 포함 하는 인시던트가 생성 됩니다. **보안 분석** 탭에서 정의 된 검색 규칙을 행으로 볼 수 있습니다. 또한 경고가 트리거되는 각 규칙에 대 한 일치 항목 수를 확인할 수 있습니다. 이 목록에서 각 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다. 각 경고에 대해 행의 끝에 있는 줄임표 (...)를 마우스 오른쪽 단추로 선택 하 여 편집, 사용 안 함, 복제, 일치 항목 표시 또는 규칙 삭제를 수행할 수도 있습니다. **분석** 페이지는 사용 하도록 설정한 템플릿 및 템플릿에 따라 만든 경고 규칙을 포함 하 여 모든 활성 경고 규칙의 갤러리입니다.

1. 경고 규칙의 결과는 인시던트 페이지에서 확인할 수 있으며 ,이 페이지에서 인시던트를 심사 하 고 [조사](tutorial-investigate-cases.md)하 고 위협을 수정할 수 있습니다.



## <a name="automate-threat-responses"></a>위협 응답 자동화

SIEM/SOC 팀은 정기적으로 보안 경고의 홍수에 휩쓸릴 수 있습니다. 생성되는 경고의 양이 너무 많으면 보안 관리자가 전부 처리할 수 없습니다. 이렇게 되면 많은 경고를 미처 조사할 수 없게 되어 조직이 파악하지 못한 공격에 취약한 상태로 노출됩니다. 

이러한 경고 중 상당수는 정의된 수정 작업으로 해결할 수 있는 되풀이 패턴을 따릅니다. Azure Sentinel은 이미 플레이북에서 수정 작업을 정의할 수 있습니다. 또한 플레이북 정의의 일부로 실시간 자동화를 설정하여 특정 보안 경고에 대해 정의된 응답을 완벽하게 자동화할 수 있습니다. 실시간 자동화를 사용하면 응답 팀은 반복되는 경고 유형에 대한 일상적인 응답을 완벽하게 자동화하여 워크로드를 대폭 줄일 수 있으므로 고유한 경고, 패턴 분석, 위협 요소 파악 등에 집중할 수 있습니다.

응답을 자동화하려면 다음을 수행합니다.

1. 응답을 자동화하려는 경고를 선택합니다.
1. Azure Sentinel 작업 영역 탐색 메뉴에서 **분석**을 선택합니다.
1. 자동화하려는 경고를 선택합니다. 
1. **경고 규칙 편집** 페이지의 **실시간 자동화**에서 이 경고 규칙과 일치할 때 실행할 **트리거된 플레이북**을 선택합니다.
1.           **저장**을 선택합니다.

   ![실시간 자동화](./media/tutorial-detect-threats/rt-configuration.png)


또한 플레이 **북 보기** 를 클릭 한 다음 실행할 플레이 북를 선택 하 여 경고 내에서 플레이 북를 실행 하 여 경고를 수동으로 수정할 수 있습니다. 새 플레이 북을 만들거나 기존 항목을 편집 하는 방법을 알아보려면 [Azure 센티널에서 플레이 북 작업](tutorial-respond-threats-playbook.md)을 참조 하세요.



## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 센티널을 사용 하 여 위협 감지를 시작 하는 방법을 배웠습니다. 

위협에 대 한 응답을 자동화 하는 방법, 자동화 된 플레이 [북을 사용 하 여 위협에 대응 하는 방법](tutorial-respond-threats-playbook.md)에 대해 알아보세요.
> [!div class="nextstepaction"]
> 위협에 [대응](tutorial-respond-threats-playbook.md) 하 여 위협에 대 한 응답을 자동화 합니다.

