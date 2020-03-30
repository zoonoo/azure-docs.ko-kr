---
title: Azure 스트림 분석 작업 복사 또는 백업
description: 이 문서에서는 Azure Stream Analytics 작업을 복사하거나 백업하는 방법을 설명합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 5c8f770855dd8d19a9d313f1b79f9bf8da4b2393
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771498"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure 스트림 분석 작업 복사 또는 백업

Visual Studio 코드 또는 Visual Studio를 사용하여 배포된 Azure Stream Analytics 작업을 복사하거나 백업할 수 있습니다. 

## <a name="before-you-begin"></a>시작하기 전에
* Azure 구독이 없는 경우 [무료 계정을](https://azure.microsoft.com/free/)만듭니다.

* [Azure 포털에](https://portal.azure.com/)로그인합니다.

* Visual Studio 코드 또는 [Visual Studio용 Azure 스트림](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension) 분석 [도구에 Azure 스트림 분석](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)확장자를 설치합니다.  

## <a name="visual-studio-code"></a>Visual Studio Code

1. 시각적 스튜디오 코드 활동 표시줄에서 **Azure** 아이콘을 클릭한 다음 **스트림 분석** 노드를 확장합니다. 작업은 구독 아래에 표시되어야 합니다.

   ![오픈 스트림 분석 탐색기](./media/vscode-explore-jobs/open-explorer.png)

2. 로컬 프로젝트로 작업을 내보내려면 Visual Studio 코드의 **분석 탐색기 스트림에서** 내보낼 작업을 찾습니다. 그런 다음 프로젝트의 폴더를 선택합니다.

    ![비주얼 스튜디오 코드에서 ASA 작업 내보내기](./media/vscode-explore-jobs/export-job.png)

    프로젝트를 선택한 폴더로 내보내현재 작업 영역에 추가됩니다.

    ![비주얼 스튜디오 코드에서 ASA 작업 내보내기](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. 다른 이름을 사용하여 다른 지역이나 백업에 작업을 게시하려면 **구독에서 선택을 선택하여** 쿼리 편집기(.asaql)에\*게시하고 지침을 따릅니다.

    ![비주얼 스튜디오 코드에서 Azure에 게시](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. [배포된 Azure Stream Analytics 작업을 프로젝트 지침으로 내보내십시오.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)

2. 쿼리 \*편집기에서 .asaql 파일을 열고 스크립트 편집기에서 **Azure에 제출을** 선택하고 지침에 따라 새 이름을 사용하여 다른 지역 또는 백업에 작업을 게시합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio 코드를 사용하여 스트림 분석 작업 만들기](quick-create-vs-code.md)
* [빠른 시작: Visual Studio를 사용하여 스트림 분석 작업 만들기](stream-analytics-quick-create-vs.md)
* [Azure Pipelines를 사용하여 CI/CD를 통해 Azure Stream Analytics 작업 배포](stream-analytics-tools-visual-studio-cicd-vsts.md)
