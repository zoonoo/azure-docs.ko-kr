---
title: 팀 데이터 과학 프로세스 프로젝트의 진행률 추적
description: 데이터 과학 그룹 관리자, 팀 리더 및 프로젝트 리더가 데이터 과학 프로젝트의 진행률을 추적할 수 있는 방법을 설명 합니다.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244083"
---
# <a name="track-the-progress-of-data-science-projects"></a>데이터 과학 프로젝트의 진행률 추적

데이터 과학 그룹 관리자, 팀 리더 및 프로젝트 책임자는 수행 된 작업, 작업의 수행 및 남은 작업 등 프로젝트의 진행 상황을 추적할 수 있습니다. 

## <a name="azure-devops-dashboards"></a>Azure DevOps 대시보드

Azure DevOps를 사용 하는 경우 대시보드를 빌드하여 지정 된 Agile 프로젝트와 관련 된 작업 및 작업 항목을 추적할 수 있습니다. 대시보드에 대 한 자세한 내용은 [대시보드, 보고서 및 위젯](/azure/devops/report/dashboards/)을 참조 하세요.

Azure DevOps에서 대시보드 및 위젯을 만들고 사용자 지정 하는 방법에 대 한 지침은 다음 퀵 스타트를 참조 하세요.

- [대시보드 추가 및 관리](/azure/devops/report/dashboards/dashboards)
- [대시보드에 위젯 추가](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>예제 대시보드

다음은 연결 된 리포지토리에 대 한 커밋 수를 비롯 하 여 Agile 데이터 과학 프로젝트의 스 프린트 작업을 추적 하는 간단한 예제 대시보드입니다. 

- **카운트다운** 타일에는 현재 스 프린트에 남아 있는 일 수가 표시 됩니다. 

- 두 **코드 타일** 에는 지난 7 일간의 두 프로젝트 리포지토리에서 커밋 수가 표시 됩니다. 

- **TDSP Customer 프로젝트에 대 한 작업 항목** 은 모든 작업 항목 및 해당 상태에 대 한 쿼리 결과를 보여 줍니다. 

- Cfd ( **누적 흐름 다이어그램** )는 닫힌 작업과 활성 작업 항목의 수를 표시 합니다.

- **번 다운 (burndown) 차트** 에는 스 프린트에서 남은 시간에 대해 완료 해야 하는 작업이 표시 됩니다.

- **Burnup 차트** 는 완료 된 작업을 스 프린트의 총 작업량에 비해 보여 줍니다.

![대시보드](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>다음 단계

[팀 데이터 과학 프로세스를 실행](walkthroughs.md) 하는 연습에는 링크 및 미리 보기 설명과 함께 특정 시나리오에 대 한 프로세스의 모든 단계를 보여 주는 연습이 나열 되어 있습니다. 연결 된 시나리오에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합 하 여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 
