---
title: 팀 데이터 과학 프로세스 프로젝트의 진행 상황 추적
description: 데이터 과학 그룹 관리자, 팀 리드 및 프로젝트 리드가 데이터 과학 프로젝트의 진행 상황을 추적하는 방법.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8cf1e5a4d97b882e7a8d0c81041bbcde709760d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864199"
---
# <a name="track-the-progress-of-data-science-projects"></a>데이터 과학 프로젝트의 진행 상황 추적

데이터 과학 그룹 관리자, 팀 리드 및 프로젝트 리드는 프로젝트의 진행 상황을 추적할 수 있습니다.  관리자는 어떤 작업이 수행되었는지, 누가 작업을 수행했는지, 어떤 작업이 남아 있는지 알고 싶어합니다.   기대치관리는 성공의 중요한 요소입니다.

## <a name="azure-devops-dashboards"></a>Azure DevOps 대시보드

Azure DevOps를 사용하는 경우 대시보드를 빌드하여 지정된 Agile 프로젝트와 관련된 활동 및 작업 항목을 추적할 수 있습니다. 대시보드에 대한 자세한 내용은 [대시보드, 보고서 및 위젯을](/azure/devops/report/dashboards/)참조하십시오.

Azure DevOps에서 대시보드 및 위젯을 만들고 사용자 지정하는 방법에 대한 지침은 다음 빠른 시작을 참조하세요.

- [대시보드 추가 및 관리](/azure/devops/report/dashboards/dashboards)
- [대시보드에 위젯 추가](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>예제 대시보드

다음은 관련 리포지토리에 대한 커밋 수를 포함하여 Agile 데이터 과학 프로젝트의 스프린트 활동을 추적하는 간단한 예제 대시보드입니다. 

- **카운트다운** 타일에는 현재 스프린트에 남아 있는 일 수가 표시됩니다. 

- 두 **코드 타일에는** 지난 7일 동안의 두 프로젝트 리포지토리의 커밋 수가 표시됩니다. 

- **TDSP 고객 프로젝트에 대 한 작업 항목은** 모든 작업 항목 및 해당 상태에 대 한 쿼리의 결과 표시 합니다. 

- **누적 흐름 다이어그램(CFD)에는** 닫힌 작업 및 활성 작업 항목 수가 표시됩니다.

- **번다운 차트는** 스프린트의 남은 시간에 대해 완료할 작업을 여전히 보여 주었습니다.

- **번업 차트는** 스프린트의 총 작업량과 비교하여 완료된 작업을 표시합니다.

![대시보드](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>다음 단계

[팀 데이터 과학 프로세스를 실행하는 연습에는](walkthroughs.md) 모든 프로세스 단계를 보여 주는 연습이 나열됩니다. 연결된 시나리오는 클라우드 및 온-프레미스 리소스를 지능형 응용 프로그램으로 관리하는 방법을 보여 줍니다. 
