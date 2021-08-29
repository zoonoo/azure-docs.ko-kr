---
title: '자습서: 파이프라인과 통합 시작'
description: 이 자습서에서는 Synapse Studio를 사용하여 파이프라인과 활동을 통합하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 71ca2dd856b18424d79a20db8a4c5552b2e98c53
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822939"
---
# <a name="integrate-with-pipelines"></a>파이프라인과 통합

이 자습서에서는 Synapse Studio를 사용하여 파이프라인과 활동을 통합하는 방법에 대해 알아봅니다. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>파이프라인 만들기 및 Notebook 작업 추가

1. Synapse Studio에서 **통합** 허브로 이동합니다.
1. **+**  > **파이프라인** 을 선택하여 새 파이프라인을 만듭니다. 새 파이프라인 개체를 클릭하여 파이프라인 디자이너를 엽니다.
1. **작업** 에서 **Synapse** 폴더를 확장하고 **Notebook** 개체를 디자이너로 끌어옵니다.
1. Notebook 작업 속성의 **설정** 탭을 선택합니다. 드롭다운 목록을 사용하여 현재 Synapse 작업 영역에서 Notebook을 선택합니다.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>매시간 실행되도록 파이프라인 예약

1. 파이프라인에서 **트리거 추가** > **새로 만들기/편집** 을 차례로 선택합니다.
1. **트리거 선택** 에서 **새로 만들기** 를 선택하고 **반복** 을 "1시간마다"로 설정합니다.
1. **확인** 을 선택합니다. 
1. **모두 게시** 를 선택합니다. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>파이프라인이 즉시 실행되도록 강제 적용

파이프라인이 게시되면 한 시간이 경과할 때까지 기다리지 않고 즉시 실행할 수 있습니다.

1. 파이프라인을 엽니다.
1. **트리거 추가** > **지금 트리거** 를 클릭합니다.
1. **확인** 을 선택합니다. 

## <a name="monitor-pipeline-execution"></a>파이프라인 실행 모니터링

1. **모니터** 허브로 이동합니다.
1. **파이프라인 실행** 을 선택하여 파이프라인 실행 진행률을 모니터링합니다.
1. 이 보기에서는 표 형식 **목록** 간에 전환하여 그래픽 **Gantt** 차트를 표시할 수 있습니다. 
1. 파이프라인 이름을 클릭하여 해당 파이프라인의 활동 상태를 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Power BI를 사용하여 데이터 시각화](get-started-visualize-power-bi.md)
