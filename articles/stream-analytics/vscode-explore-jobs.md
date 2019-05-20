---
title: Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 작업 탐색
description: 이 문서에서는 Azure Stream Analytics 작업을 로컬 프로젝트, 작업 목록 보기 작업 엔터티를 내보내는 방법을 보여 줍니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827802"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 살펴보기

Azure Stream Analytics Visual Studio Code 확장에 대 한 Stream Analytics 작업을 관리 하기 위한 개발자는 간단한 환경을 제공 합니다. Windows, Mac 및 Linux에서 사용할 수 있습니다. Azure Stream Analytics 확장을 사용 하 여 다음을 수행할 수 있습니다.

- [만들](quick-create-vs-code.md), 시작 및 중지 작업
- 로컬 프로젝트에 기존 작업 내보내기
- 작업 엔터티를 살펴보고 작업 나열

## <a name="export-a-job-to-a-local-project"></a>로컬 프로젝트 작업을 내보내려면

작업을 로컬 프로젝트 내보내려면에서 내보내려는 작업을 찾을 합니다 **Stream Analytics 탐색기** Visual Studio Code에서. 그런 다음 프로젝트에 대 한 폴더를 선택 합니다. 프로젝트를 선택 하면 폴더를 내보내고 계속 Visual Studio Code에서 작업을 관리할 수 있습니다. Visual Studio Code를 사용 하 여 Stream Analytics 작업을 관리 하는 방법은 Visual Studio Code를 참조 하세요 [퀵 스타트](quick-create-vs-code.md)합니다.

![Visual Studio Code에서 ASA 작업 내보내기](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>작업을 나열 하 고 작업 엔터티를 보려면

Visual Studio에서 작업 보기를 사용하여 Azure Stream Analytics 작업과 상호 작용할 수 있습니다.


1. 클릭 합니다 **Azure** Visual Studio 코드 작업 표시줄의 아이콘을 펼친 다음 **Stream Analytics 노드**합니다. 작업 구독 아래에 나타나야 합니다.

   ![열기 Stream Analytics 탐색기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업 노드를 열고 작업 쿼리, 구성, 입력, 출력 및 함수를 볼 수 있습니다. 

3. 작업 노드를 마우스 오른쪽 단추로 클릭 하 고 선택 합니다 **포털에서 작업 보기 열기** Azure portal에서 작업 보기를 열려면 노드를 합니다.

   ![포털에서 열기 작업 보기](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code (미리 보기)에서 클라우드 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
