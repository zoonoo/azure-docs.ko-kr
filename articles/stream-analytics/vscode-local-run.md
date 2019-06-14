---
title: Visual Studio Code (미리 보기)를 사용 하 여 로컬 Azure Stream Analytics 쿼리 테스트
description: 이 문서에서는 Visual Studio Code 용 Azure Stream Analytics 도구를 사용 하 여 로컬로 쿼리를 테스트 하는 방법을 설명 합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827952"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Visual Studio Code를 사용 하 여 로컬 Stream Analytics 쿼리 테스트

로컬로 샘플 데이터로 Stream Analytics 작업을 테스트 하려면 Visual Studio Code 용 Azure Stream Analytics 도구를 사용할 수 있습니다.

이 사용 하 여 [퀵 스타트](quick-create-vs-code.md) 를 Visual Studio Code를 사용 하 여 Stream Analytics 작업을 만드는 방법을 알아봅니다.

## <a name="run-queries-locally"></a>쿼리를 로컬로 실행

로컬로 샘플 데이터로 Stream Analytics 작업을 테스트 하려면 Visual Studio Code 용 Azure Stream Analytics 확장을 사용할 수 있습니다.

1. 사용 하 여 Stream Analytics 작업을 만들었으면 **Ctrl + Shift + P** 명령 팔레트를 엽니다. 입력 한 다음 선택한 **ASA: Add Input**을 입력합니다.

    ![Visual Studio code에서 ASA 입력 추가](./media/vscode-local-run/add-input.png)

2. 선택 **로컬 입력**합니다.

    ![Visual Studio code에서 ASA 로컬 입력 추가](./media/vscode-local-run/add-local-input.png)

3. 선택 **+ 새 로컬 입력**합니다.

    ![새 ASA 로컬 Visual Studio code에서 입력 추가](./media/vscode-local-run/add-new-local-input.png)

4. 쿼리에서 동일한 입력된 별칭을 입력 합니다.

    ![새 ASA 로컬 입력된 별칭 추가](./media/vscode-local-run/new-local-input-alias.png)

5. 에 **LocalInput_DefaultLocalStream.json** 파일을 로컬 데이터 파일에 있는 파일 경로 입력 합니다.

    ![Visual Studio에서 로컬 파일 경로를 입력 하십시오.](./media/vscode-local-run/local-file-path.png)

6. 선택한 쿼리 편집기로 돌아가서 **로컬로 실행**합니다.

    ![쿼리 편집기에서 로컬로 실행을 선택](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code (미리 보기)에서 클라우드 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)

* [Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 작업 탐색](vscode-explore-jobs.md)
