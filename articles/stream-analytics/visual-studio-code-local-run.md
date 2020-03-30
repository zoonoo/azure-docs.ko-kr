---
title: Visual Studio 코드를 사용하여 샘플 데이터로 Azure Stream Analytics 작업을 로컬로 테스트합니다.
description: 이 문서에서는 Visual Studio 코드에 대한 Azure 스트림 분석 도구를 사용하여 샘플 데이터를 사용하여 쿼리를 로컬로 테스트하는 방법을 설명합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486470"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Visual Studio 코드를 사용하여 샘플 데이터로 로컬에서 테스트 스트림 분석 쿼리

Visual Studio 코드에 Azure 스트림 분석 도구를 사용하여 샘플 데이터를 사용하여 로컬에서 Stream Analytics 작업을 테스트할 수 있습니다. 프로젝트의 **LocalRunOutputs** 폴더에서 JSON 파일에서 쿼리 결과를 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [.NET 코어 SDK를](https://dotnet.microsoft.com/download) 설치하고 시각적 스튜디오 코드를 다시 시작합니다.

* 이 [빠른 시작을](quick-create-vs-code.md) 사용하여 Visual Studio 코드를 사용하여 스트림 분석 작업을 만드는 방법을 알아봅니다.

## <a name="prepare-sample-data"></a>샘플 데이터 준비

먼저 샘플 입력 데이터 파일을 준비해야 합니다. 컴퓨터에 일부 샘플 데이터 파일이 이미 있는 경우 이 단계를 건너뛰고 다음 단계로 이동할 수 있습니다.

1. 맨 위에서 입력 구성 파일에서 **데이터 미리 보기를** 클릭합니다. 일부 입력 데이터는 IoT Hub에서 가져오고 미리 보기 창에 표시됩니다. 이 경우 시간이 걸릴 수 있습니다.

2. 데이터가 표시되면 **저장을** 클릭하여 로컬 파일에 데이터를 저장합니다.

 ![라이브 입력 미리 보기](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>로컬 입력 정의

1. 스트림 분석 프로젝트의 입력 폴더 아래에 **input.json을** 클릭합니다. 그런 다음 맨 위 줄에서 **로컬 입력 추가를** 선택합니다.

    ![프로젝트에서 로컬 입력 추가](./media/quick-create-vs-code/add-input-from-project.png)

    **Ctrl+Shift+P를** 사용하여 명령 팔레트를 열고 **ASA: 입력 추가**를 입력할 수도 있습니다.

   ![VS Code에서 Stream Analytics 입력 추가](./media/quick-create-vs-code/add-input.png)

2. **로컬 입력을 선택합니다.**

    ![비주얼 스튜디오 코드에 ASA 로컬 입력 추가](./media/vscode-local-run/add-local-input.png)

3. **+ 새 로컬 입력을 선택합니다.**

    ![비주얼 스튜디오 코드에 새 ASA 로컬 입력 추가](./media/vscode-local-run/add-new-local-input.png)

4. 쿼리에 사용한 것과 동일한 입력 별칭을 입력합니다.

    ![새 ASA 로컬 입력 별칭 추가](./media/vscode-local-run/new-local-input-alias.png)

5. 새로 생성된 **LocalInput_Input.json** 파일에서 로컬 데이터 파일이 있는 파일 경로를 입력합니다.

    ![비주얼 스튜디오에서 로컬 파일 경로 입력](./media/vscode-local-run/local-file-path.png)

6. 입력 데이터를 미리 보기 위해 **데이터 미리 보기를** 선택합니다. JSON 또는 CSV인 경우 데이터의 직렬화 유형이 자동으로 검색됩니다. 선택기를 사용하여 **데이터를 테이블** 또는 **원시** 형식으로 봅니다. 다음 표는 **테이블 형식의**데이터의 예입니다.

     ![테이블 형식으로 로컬 데이터 미리 보기](./media/vscode-local-run/local-file-preview-table.png)

    다음 표는 **Raw 형식의**데이터의 예입니다.

    ![원시 형식으로 로컬 데이터 미리 보기](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>로컬로 쿼리 실행

쿼리 편집기로 돌아가서 **로컬로 실행을**선택합니다. 그런 다음 드롭다운 목록에서 **로컬 입력 사용을** 선택합니다.

![쿼리 편집기에서 로컬로 실행 선택](./media/vscode-local-run/run-locally.png)

![로컬 입력 사용](./media/vscode-local-run/run-locally-use-local-input.png)

결과는 오른쪽 창에 표시됩니다. **실행을** 클릭하여 다시 테스트할 수 있습니다. **폴더에서 열기를** 선택하여 파일 탐색기에서 결과 파일을 확인하고 다른 도구를 사용하여 추가로 열 수도 있습니다. 결과 파일은 JSON 형식으로만 사용할 수 있습니다.

![로컬 실행 결과 보기](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>다음 단계

* [Visual Studio 코드를 사용하여 라이브 입력을 사용하여 로컬로 Azure Stream Analytics 작업 테스트](visual-studio-code-local-run-live-input.md)

* [비주얼 스튜디오 코드(미리 보기)를 통해 Azure 스트림 분석 작업 탐색](visual-studio-code-explore-jobs.md)
