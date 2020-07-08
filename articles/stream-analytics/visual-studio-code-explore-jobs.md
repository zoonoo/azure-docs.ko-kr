---
title: Visual Studio Code에서 Azure Stream Analytics 작업 살펴보기
description: 이 문서에서는 Azure Stream Analytics 작업을 로컬 프로젝트로 내보내고 작업을 나열 하 고 작업 엔터티를 보는 방법을 보여 줍니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: how-to
ms.openlocfilehash: 00705e40ca17959701af325ed52a4c3754d35122
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039061"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code를 사용 하 여 Azure Stream Analytics 탐색 (미리 보기)

Visual Studio Code 확장에 대 한 Azure Stream Analytics는 개발자에 게 Stream Analytics 작업을 관리 하는 간단한 환경을 제공 합니다. Windows, Mac 및 Linux에서 사용할 수 있습니다. Azure Stream Analytics 확장을 사용 하면 다음을 수행할 수 있습니다.

- 작업 [만들기](quick-create-vs-code.md), 시작 및 중지
- 로컬 프로젝트로 기존 작업 내보내기
- 작업 나열 및 작업 엔터티 보기

## <a name="export-a-job-to-a-local-project"></a>로컬 프로젝트로 작업 내보내기

작업을 로컬 프로젝트로 내보내려면 Visual Studio Code의 **Stream Analytics 탐색기** 에서 내보내려는 작업을 찾습니다. 그런 다음 프로젝트에 대 한 폴더를 선택 합니다. 프로젝트를 선택한 폴더로 내보내고 Visual Studio Code에서 작업을 계속 관리할 수 있습니다. Visual Studio Code를 사용 하 여 Stream Analytics 작업을 관리 하는 방법에 대 한 자세한 내용은 Visual Studio Code [빠른](quick-create-vs-code.md)시작을 참조 하세요.

![Visual Studio Code에서이 작업 내보내기](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>작업 나열 및 작업 엔터티 보기

Visual Studio에서 작업 보기를 사용하여 Azure Stream Analytics 작업과 상호 작용할 수 있습니다.


1. Visual Studio Code 활동 표시줄에서 **Azure** 아이콘을 클릭 한 다음 **Stream Analytics 노드**를 확장 합니다. 사용자의 작업은 구독 아래에 표시 되어야 합니다.

   ![Stream Analytics 탐색기 열기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업 노드를 확장 하 고 작업 쿼리, 구성, 입력, 출력 및 함수를 열고 볼 수 있습니다. 

3. 작업 노드를 마우스 오른쪽 단추로 클릭 하 고 **포털에서 작업 보기 열기** 노드를 선택 하 여 Azure Portal에서 작업 보기를 엽니다.

   ![포털에서 작업 보기 열기](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code(미리 보기)에서 Azure Stream Analytics 클라우드 작업 만들기](quick-create-vs-code.md)
