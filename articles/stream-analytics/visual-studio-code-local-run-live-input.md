---
title: Visual Studio 코드를 사용하여 라이브 스트림 입력에 대해 로컬로 Azure Stream Analytics 쿼리 테스트
description: 이 문서에서는 Visual Studio 코드에 대한 Azure 스트림 분석 도구를 사용하여 실시간 스트림 입력에 대해 로컬로 쿼리를 테스트하는 방법을 설명합니다.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660854"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Visual Studio 코드를 사용하여 실시간 스트림 입력에 대해 로컬로 스트림 분석 쿼리 테스트

Visual Studio 코드용 Azure 스트림 분석 도구를 사용하여 실시간 스트림 입력에 대해 로컬로 스트림 분석 작업을 테스트할 수 있습니다. 입력은 Azure 이벤트 허브 또는 Azure IoT 허브와 같은 소스에서 올 수 있습니다. 출력 결과는 **LocalRunOutputs라는**프로젝트의 폴더에 JSON 파일로 전송됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* [.NET 코어 SDK를](https://dotnet.microsoft.com/download) 설치하고 시각적 스튜디오 코드를 다시 시작합니다.

* [이 빠른 시작을](quick-create-vs-code.md) 사용하여 Visual Studio 코드를 사용하여 스트림 분석 작업을 만드는 방법을 알아봅니다.

## <a name="define-a-live-stream-input"></a>라이브 스트림 입력 정의

1. Stream Analytics 프로젝트에서 마우스 오른쪽 단추로 **Inputs** 폴더를 클릭합니다. 그런 다음 **ASA:** 컨텍스트 메뉴에서 입력 추가를 선택합니다.

   ![Inputs 폴더에서 입력 추가](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   **Ctrl+Shift+P를** 선택하여 명령 팔레트를 열고 **ASA: 입력 추가**를 입력할 수도 있습니다.

   ![Visual Studio Code에서 Stream Analytics 입력 추가](./media/quick-create-vs-code/add-input.png)

2. 드롭다운 목록에서 입력 소스 유형을 선택합니다.

   ![입력 옵션으로 IoT 허브 선택](./media/quick-create-vs-code/iot-hub.png)

3. 명령 팔레트에서 입력을 추가한 경우 해당 입력을 사용할 Stream Analytics 쿼리 스크립트를 선택합니다. 그러면 **myASAproj.asaql**의 파일 경로로 자동으로 채워집니다.

   ![Visual Studio에서 Stream Analytics 스크립트 선택](./media/quick-create-vs-code/asa-script.png)

4. 드롭다운 메뉴에서 **Azure 구독에서 선택**을 선택합니다.

    ![구독에서 선택](./media/quick-create-vs-code/add-input-select-subscription.png)

5. 새로 생성된 JSON 파일을 구성합니다. CodeLens 기능을 사용하여 문자열을 입력하거나, 드롭다운 목록에서 선택하거나, 파일에서 텍스트를 직접 변경할 수 있습니다. 다음 스크린샷에서는 **구독에서 선택**을 예로 보여 줍니다.

   ![Visual Studio Code에서 입력 구성](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>입력 미리 보기

입력 데이터가 오는지 확인하려면 맨 위에서 라이브 입력 구성 파일의 **데이터 미리 보기를** 선택합니다. 일부 입력 데이터는 IoT 허브에서 제공되며 미리 보기 창에 표시됩니다. 미리 보기가 표시되는 데 몇 초 정도 걸릴 수 있습니다.

 ![라이브 입력 미리 보기](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>로컬로 쿼리 실행

쿼리 편집기로 돌아가서 **로컬로 실행을**선택합니다. 그런 다음 드롭다운 목록에서 **라이브 입력 사용을** 선택합니다.

![쿼리 편집기에서 "로컬로 실행"을 선택합니다.](./media/vscode-local-run/run-locally.png)

!["라이브 입력 사용" 선택](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

결과는 오른쪽 창에 표시되고 3초마다 새로 고쳐집니다. **실행을** 선택하여 다시 테스트할 수 있습니다. **폴더에서 열기를** 선택하여 파일 탐색기에서 결과 파일을 보고 Visual Studio 코드 또는 Excel과 같은 도구를 사용하여 열 수도 있습니다. 결과 파일은 JSON 형식으로만 사용할 수 있습니다.

출력 만들기를 시작하는 작업의 기본 시간은 **지금으로**설정됩니다. 결과 창에서 출력 시작 **시간** 단추를 선택하여 시간을 사용자 지정할 수 있습니다.

![로컬 실행 결과 보기](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>다음 단계

* [비주얼 스튜디오 코드(미리 보기)를 통해 Azure 스트림 분석 작업 살펴보기](visual-studio-code-explore-jobs.md)

* [npm 패키지를 사용하여 CI/CD 파이프라인 설정](setup-cicd-vs-code.md)
