---
title: Visual Studio Code를 사용 하 여 쿼리 Azure Stream Analytics 테스트
description: 이 문서에서는 Visual Studio Code에 대 한 Azure Stream Analytics 도구를 사용 하 여 쿼리를 로컬로 테스트 하는 방법을 설명 합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924964"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Visual Studio Code를 사용 하 여 로컬로 쿼리 Stream Analytics 테스트

Visual Studio Code Azure Stream Analytics 도구를 사용 하 여 샘플 데이터를 사용 하 여 Stream Analytics 작업을 로컬로 테스트할 수 있습니다.

이 [빠른](quick-create-vs-code.md) 시작을 사용 하 여 Visual Studio Code를 사용 하 여 Stream Analytics 작업을 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>전제 조건
* [.Net CORE SDK](https://dotnet.microsoft.com/download)를 설치 합니다.
* Visual Studio Code를 다시 시작합니다.
 
## <a name="run-queries-locally"></a>로컬로 쿼리 실행

Visual Studio Code에 대 한 Azure Stream Analytics 확장을 사용 하 여 샘플 데이터를 사용 하 여 Stream Analytics 작업을 로컬로 테스트할 수 있습니다.

1. Stream Analytics 작업을 만들었으면 **Ctrl + Shift + P** 를 사용 하 여 명령 팔레트를 엽니다. 그런 다음를 입력 하 고이를 선택 합니다. **입력 추가**합니다.

    ![Visual Studio code에서 GLOBAL.ASA 입력 추가](./media/vscode-local-run/add-input.png)

2. **로컬 입력**을 선택 합니다.

    ![Visual Studio code에서 GLOBAL.ASA 로컬 입력 추가](./media/vscode-local-run/add-local-input.png)

3. **+ 새 로컬 입력**을 선택 합니다.

    ![Visual Studio code에서 새 GLOBAL.ASA 로컬 입력 추가](./media/vscode-local-run/add-new-local-input.png)

4. 쿼리에서 사용한 것과 동일한 입력 별칭을 입력 합니다.

    ![새 GLOBAL.ASA 로컬 입력 별칭 추가](./media/vscode-local-run/new-local-input-alias.png)

5. **LocalInput_DefaultLocalStream** 파일에서 로컬 데이터 파일이 있는 파일 경로를 입력 합니다.

    ![Visual Studio에서 로컬 파일 경로 입력](./media/vscode-local-run/local-file-path.png)

6. 쿼리 편집기로 돌아가서 **로컬에서 실행**을 선택 합니다.

    ![쿼리 편집기에서 로컬로 실행을 선택 합니다.](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code에서 Azure Stream Analytics 클라우드 작업 만들기 (미리 보기)](quick-create-vs-code.md)

* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 작업 살펴보기 (미리 보기)](vscode-explore-jobs.md)
