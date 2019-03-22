---
title: 사용자가 Azure Sentinel 미리 보기의 사용자 분석을 사용 하 여 조사 | Microsoft Docs
description: Azure Sentinel의 사용자 분석을 사용 하 여 사용자를 조사 하는 방법에 알아봅니다.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246324"
---
# <a name="investigate-users-with-user-analytics"></a>사용자 분석을 사용 하 여 사용자를 조사 합니다.

> [!IMPORTANT]
> Azure Sentinel 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

사용자는 밀접 하 게 모니터링 하려는 엔터티입니다. 를 사용자에 대 한 정보를 가져올 수 있도록 하려면 Azure Sentinel Azure Advanced Threat Protection을 사용 하 여 통합 원활 하 게 합니다. 이 통합을 사용 하 여 사용자 동작 분석 및 조사 해야 먼저 Azure Sentinel 및 Microsoft 365 경고 및 의심 스러운 활동 패턴에 따른 사용자 우선 순위를 지정할 수 있습니다.

Azure Sentinel은 Azure Active Directory의 모든 사용자에 대 한 포괄적인 사용자 분석 및 위험 분석을 사용 하도록 설정 하려면 Microsoft 365에서 analytics 사용 하 여 사용자 데이터를 강화 합니다. 

## <a name="how-it-works"></a>작동 방법

1. 기준의 경우 일치 하는 분석 규칙 감지 되 면 Azure ATP에 경고를 보내는 Azure Sentinel 보안.
1. Azure ATP는 사용자 엔터티는 경고 관련 된를 확인 하 고 해당 사용자에 대 한 조사 우선 순위를 계산 합니다.
3. Azure ATP 다시 Azure Sentinel에 대 한 분석 규칙에서 데이터를 사용 하 여 서는 후에 다음 사용자의 점수를 계산 합니다.


## <a name="prerequisites"></a>필수 조건

- Azure Advanced Threat Protection 라이선스를 
- 기능을 사용 하려면 Azure Sentinel 설치 된 테 넌 트에서 전역 관리자 권한이 필요

> [!NOTE]
> - 각 Azure ATP 테 넌 트에 대 한 Azure Sentinel 인스턴스 하나를 연결할 수 있습니다.
> - 사용자 분석은 현재 Azure Active Directory 사용자에 대해서만 사용할 수 있습니다. 

## <a name="enable-user-analytics"></a>사용자 분석을 사용 하도록 설정

1. 이동 포털에서 Azure Sentinel의 사용자 분석을 사용 하도록 합니다 **사용자 분석** 페이지 및 클릭 **사용 하도록 설정**합니다. Azure atp 작업 영역에 대 한 정보가 전송 됩니다.

1. 그런 다음 변수를 Azure ATP 합니다. 아래 **보안 확장 프로그램** 에 **Microsoft Azure Sentinel** 탭을 클릭를 **+ plus** 를 추가 하 여 다음 작업 영역을 선택 합니다. 
1. **Connect**를 클릭합니다.

## <a name="investigate-a-user"></a>사용자를 조사 합니다.

1. Azure Sentinel 메뉴에서 **사용자 분석**, 조사 우선 순위에 따라 사용자의 목록을 검토 합니다. 점수는 경고 Sentinel Azure 및 Microsoft 365 경고를 기반으로 합니다.

2. 조사 하려는 사용자를 검색 합니다. 사용자의 사용자 페이지로 이동 하려면 클릭 합니다. 타임 라인에 시간이 지남에 따라 사용자의 작업 및 경고를 검토 합니다. Microsoft 365 및 Azure Sentinel의 모든 활동을 볼 수 있습니다. 사용자 페이지 내에서 사용자로 드릴 다운 하 여 연결할 수도 있습니다.
      
    ![사용자](./media/user-analytics/user-investigation.png)

 
3. 제대로 작동 하려면이 위해 적절히 설정 해야 합니다 [사용자 지정 경고 규칙](tutorial-detect-threats.md) 올바른 사용자 식별자를 매핑할 합니다 **계정** 엔터티.

    - Windows 이벤트에 대 한 매핑 **계정** 에 **SID**
    - (Azure 활동을 포함 하는 많은 로그에서 찾을 수 있습니다)는 azure AD 데이터 또는 Office 365 데이터를 매핑하는 **계정** 속성을 합니다 **GUID**, 또는 **사용자 계정 이름**. 

   ![쿼리](./media/user-analytics/query-window.png)



예:  
> [!NOTE]
> Azure 활동의 활동 로그에 호출자에 게 엔터티는 UPN을 포함합니다.

1. 이 쿼리는 비정상적인 많은 리소스 또는 Azure 활동 로그에서 배포 작업 만들기에 대 한 검색합니다.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. 사용자 지정 경고 규칙에 매핑하는 **계정** 속성을 **호출자**합니다.
   
   ![쿼리](./media/user-analytics/query-window.png)

3. 사용자 조사 창에서 사용자를 조사 합니다. 사용자를 조사 하는 방법에 대 한 도움말을 참조 하세요. [자습서: 사용자 조사](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user)합니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 위협 인텔리전스 공급자에 연결 하는 방법을 알아보았습니다. Azure에서 감시 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.

- Azure Sentinel 사용을 시작 하려면 Microsoft Azure를 구독을 해야 합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- 에 대해 알아봅니다 하는 방법 [온 보 딩 데이터를 Azure Sentinel](quickstart-onboard.md), 및 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
