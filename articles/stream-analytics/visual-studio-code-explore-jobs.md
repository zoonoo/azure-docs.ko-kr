---
title: 비주얼 스튜디오 코드에서 Azure 스트림 분석 작업 살펴보기
description: 이 문서에서는 Azure Stream Analytics 작업을 로컬 프로젝트로 내보내고 작업을 나열하고 작업 엔터티를 보는 방법을 보여 주었습니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479242"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>비주얼 스튜디오 코드(미리 보기)를 통해 Azure 스트림 분석 살펴보기

시각적 스튜디오 코드 확장을 위한 Azure 스트림 분석 기능을 통해 개발자는 스트림 분석 작업을 관리할 수 있는 간단한 환경을 제공합니다. 그것은 윈도우, 맥과 리눅스에서 사용할 수 있습니다. Azure 스트림 분석 확장을 사용하면 다음을 수행할 수 있습니다.

- 작업 [만들기,](quick-create-vs-code.md)시작 및 중지
- 기존 작업을 로컬 프로젝트로 내보내기
- 작업 나열 및 작업 엔터티 보기

## <a name="export-a-job-to-a-local-project"></a>로컬 프로젝트로 작업 내보내기

로컬 프로젝트로 작업을 내보내려면 Visual Studio 코드의 **분석 탐색기 스트림에서** 내보낼 작업을 찾습니다. 그런 다음 프로젝트의 폴더를 선택합니다. 프로젝트가 선택한 폴더로 내보내지며 Visual Studio 코드에서 작업을 계속 관리할 수 있습니다. Visual Studio 코드를 사용하여 스트림 분석 작업을 관리하는 방법에 대한 자세한 내용은 Visual Studio 코드 [빠른 시작을](quick-create-vs-code.md)참조하십시오.

![비주얼 스튜디오 코드에서 ASA 작업 내보내기](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>작업 목록 및 작업 엔터티 보기

Visual Studio에서 작업 보기를 사용하여 Azure Stream Analytics 작업과 상호 작용할 수 있습니다.


1. 시각적 스튜디오 코드 활동 모음에서 **Azure** 아이콘을 클릭한 다음 **스트림 분석 노드를**확장합니다. 작업은 구독 아래에 표시되어야 합니다.

   ![오픈 스트림 분석 탐색기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업 노드를 확장하면 작업 쿼리, 구성, 입력, 출력 및 함수를 열고 볼 수 있습니다. 

3. 작업 노드를 마우스 오른쪽 단추로 클릭하고 **포털 노드에서 작업 보기 열기를** 선택하여 Azure 포털에서 작업 보기를 엽니다.

   ![포털에서 작업 보기 열기](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code(미리 보기)에서 Azure Stream Analytics 클라우드 작업 만들기](quick-create-vs-code.md)
