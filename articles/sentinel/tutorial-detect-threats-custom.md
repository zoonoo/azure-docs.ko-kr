---
title: Azure 센티널을 사용 하 여 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙 만들기 | Microsoft Docs
description: 이 자습서를 사용 하 여 Azure 센티널에서 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙을 만드는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 5d4c549eaded78c69d3e7fa7173b5ad9b1d82f2f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333138"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>자습서: 의심 스러운 위협을 검색 하는 사용자 지정 분석 규칙 만들기

 [데이터 원본](quickstart-onboard.md) 을 Azure 센티널에 연결한 후 사용자 환경에서 특정 기준을 검색 하 고 조건이 일치 하는 경우 조사를 수행할 수 있도록 인시던트를 생성할 수 있는 사용자 지정 규칙을 만들 수 있습니다. 이 자습서에서는 사용자 지정 규칙을 만들어 Azure 센티널에서 위협을 검색 하는 방법을 안내 합니다.

이 자습서는 Azure 센티널로 위협을 검색 하는 데 도움이 됩니다.
> [!div class="checklist"]
> * 분석 규칙 만들기
> * 위협 응답 자동화

## <a name="create-custom-analytic-rules"></a>사용자 지정 분석 규칙 만들기

사용자 환경에서 의심 스러운 위협 및 비정상 유형을 검색 하는 데 도움이 되는 사용자 지정 분석 규칙을 만들 수 있습니다. 규칙은 사용자에 게 즉시 알림이 표시 되도록 하 여 위협을 심사 하 고 조사 하 고 수정할 수 있습니다.

1. Azure Sentinel 아래의 Azure Portal에서 **Analytics**를 선택합니다.

1. 상단 메뉴 모음에서 **+ 만들기** 를 선택 하 고 **예약 된 쿼리 규칙**을 선택 합니다. 그러면 **사용자 지정 규칙 만들기 마법사**가 열립니다.

    ![예약 된 쿼리 만들기](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. **일반** 탭에서 설명이 포함 된 이름 및 설명을 입력 합니다. 필요에 따라 **경고 심각도** 를 설정 합니다. 규칙을 만들 때 사용 하도록 설정할 수 있습니다. 그러면 규칙을 만든 후 즉시 실행 됩니다. 사용 하지 않도록 설정 된 경우 규칙이 **활성 규칙** 탭에 추가 되 고 필요할 때 해당 규칙에서 사용 하도록 설정할 수 있습니다.

    ![사용자 지정 분석 규칙 만들기 시작](media/tutorial-detect-threats-custom/general-tab.png)

1. **설정** 탭에서 직접 쿼리를 작성 하거나 Log Analytics 쿼리를 만든 다음 **검색 쿼리** 필드에 붙여 넣을 수 있습니다. 쿼리를 변경 하 고 구성할 때 Azure 센티널은 **결과 미리 보기** 창에서 오른쪽에 있는 쿼리 결과를 시뮬레이션 합니다. 이렇게 하면 사용자가 만드는 경고에 대해 특정 시간 간격 동안 생성 되는 데이터의 양을 파악할 수 있습니다. 양은 **쿼리 실행** 에 대해 설정 하는 것과 마지막의 **조회 데이터**에 따라 달라 집니다. 평균적으로 경고가 너무 자주 발생 하는 경우 평균 기준선을 초과 하도록 결과 수를 높게 설정할 수 있습니다.

   ![Azure 센티널에서 쿼리 만들기](media/tutorial-detect-threats-custom/settings-tab.png)

   Azure 활동에서 비정상 리소스를 만들 때 경고를 표시 하는 샘플 쿼리는 다음과 같습니다.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > 쿼리 길이는 1 ~ 007e; 1000 자 여야 하며 "search \*" 또는 "union \*"을 포함할 수 없습니다.

    1. **쿼리 일정**에서 다음 매개 변수를 설정 합니다.

        1.  쿼리가 실행 되 **는 빈도 (** 5 분 마다, 하루에 한 번)를 설정 하려면 **매번 쿼리 실행** 을 설정 합니다.

        1.  쿼리가 실행 되는 데이터의 양에 대 한 기간을 제어 하기 위해 **last에서 조회 데이터** 를 설정 합니다. 예를 들어 60 분의 데이터에서 매시간 실행 될 수 있습니다.

        1. 경고가 발생 한 후에 한 번만 경고를 표시 하려는 경우 **경고를 생성 한 후 쿼리 실행을 중지** 하도록 Azure 센티널을 설정할 수 있습니다. 쿼리가 실행을 중지 해야 하는 기간 (최대 24 시간)을 설정 해야 합니다.

    1. 경고 **트리거**아래에서 경고 트리거 조건을 정의 합니다. **엔터티 매핑**에서 쿼리의 열을 Azure 센티널에서 인식 하는 엔터티 필드에 매핑할 수 있습니다. 각 필드에 대해 Log Analytics에서 만든 쿼리의 관련 열을 적절 한 엔터티 필드에 매핑합니다. 각 엔터티에는 여러 필드 (예: SID 및 GUID)가 포함 됩니다. 상위 수준 엔터티 뿐만 아니라 모든 필드에 따라 엔터티를 매핑할 수 있습니다.

1.  **응답 자동화** 탭에서 사용자 지정 규칙에 의해 경고가 생성 될 때 자동으로 실행 하려는 플레이 북를 선택 합니다. 플레이 북을 만들고 자동화 하는 방법에 대 한 자세한 내용은 [위협에 대응](tutorial-respond-threats-playbook.md)을 참조 하세요.

    ![Azure 센티널에서 위협에 대 한 응답 자동화](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. **검토** 를 선택 하 여 새 경고 규칙에 대 한 모든 설정을 검토 한 후 **만들기를 선택 하 여 경고 규칙을 초기화**합니다.

   ![사용자 지정 쿼리 검토](media/tutorial-detect-threats-custom/review-tab.png)

1.  경고가 생성 되 면 **활성 규칙**의 테이블에 사용자 지정 규칙이 추가 됩니다. 이 목록에서 각 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다.

1.  만든 경고 규칙의 결과를 보려면 **인시던트 페이지로 이동 하 여** 인시던트를 조사 하 고, [인시던트를 조사](tutorial-investigate-cases.md)하 고, 위협을 수정할 수 있습니다.


> [!NOTE]
> Azure 센티널에서 생성 된 경고는 [Microsoft Graph 보안](https://aka.ms/securitygraphdocs)을 통해 사용할 수 있습니다. 자세한 내용은 [Microsoft Graph 보안 경고 설명서](https://aka.ms/graphsecurityreferencebetadocs)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 센티널을 사용 하 여 위협 감지를 시작 하는 방법을 배웠습니다.

위협에 대 한 응답을 자동화 하는 방법을 알아보려면 [Azure 센티널에서 자동화 된 위협 응답을 설정](tutorial-respond-threats-playbook.md)합니다.

