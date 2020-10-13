---
title: Azure Stream Analytics 작업 복사 또는 백업
description: 이 문서에서는 Azure Stream Analytics 작업을 복사 하거나 백업 하는 방법을 설명 합니다.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: e0214cb9515a5974f1de9a27760dc037f7593f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892319"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics 작업 복사 또는 백업

Visual Studio Code 또는 Visual Studio를 사용 하 여 배포 된 Azure Stream Analytics 작업을 복사 하거나 백업할 수 있습니다. 작업을 다른 지역으로 복사 하면 마지막 출력 시간이 복사 되지 않습니다. 따라서 복사 된 작업을 시작할 때 [**마지막으로 중지 된 경우**](https://docs.microsoft.com/azure/stream-analytics/start-job#start-options) 에는이 옵션을 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에
* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

* Visual Studio Code 또는 [Azure Stream Analytics tools For Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) [용 Azure Stream Analytics 확장을](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) 설치 합니다.  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code 활동 표시줄에서 **Azure** 아이콘을 클릭 한 다음 **Stream Analytics** 노드를 확장 합니다. 사용자의 작업은 구독 아래에 표시 되어야 합니다.

   ![Stream Analytics 탐색기 열기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업을 로컬 프로젝트로 내보내려면 Visual Studio Code의 **Stream Analytics 탐색기** 에서 내보내려는 작업을 찾습니다. 그런 다음 프로젝트에 대 한 폴더를 선택 합니다.

    ![Visual Studio Code에서 GLOBAL.ASA 작업 찾기](./media/vscode-explore-jobs/export-job.png)

    프로젝트를 선택 하 고 현재 작업 영역에 추가 된 폴더로 내보냅니다.

    ![Visual Studio Code에서이 작업 내보내기](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. 다른 이름을 사용 하 여 작업을 다른 지역 또는 백업에 게시 하려면 쿼리 편집기 (. script.asaql)에서 **게시할 구독에서 선택을** 선택 하 \* 고 지침을 따릅니다.

    ![Visual Studio Code에서 Azure에 게시](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. 배포 된 [Azure Stream Analytics 작업을 프로젝트에 내보내기 지침](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)을 따릅니다.

2. \*쿼리 편집기에서 script.asaql 파일을 열고 스크립트 편집기에서 **Azure에 제출** 을 선택 하 고 지침에 따라 새 이름을 사용 하 여 작업을 다른 지역 또는 백업에 게시 합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code를 사용 하 여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
* [빠른 시작: Visual Studio를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Azure Pipelines를 사용하여 CI/CD를 통해 Azure Stream Analytics 작업 배포](stream-analytics-tools-visual-studio-cicd-vsts.md)
