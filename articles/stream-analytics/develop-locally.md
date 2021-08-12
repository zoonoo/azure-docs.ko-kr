---
title: 로컬에서 Azure Stream Analytics 작업 개발 및 디버그
description: Azure Portal에서 실행하기 전에 로컬 머신에서 Azure Stream Analytics 작업을 개발하고 테스트하는 방법을 알아봅니다.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016476"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>로컬에서 Azure Stream Analytics 작업 개발 및 디버그

Azure Portal에서 Azure Stream Analytics 작업을 만들고 테스트할 수 있지만 대부분의 개발자는 로컬 개발 환경을 선호합니다. Stream Analytics를 사용하면 즐겨 사용하는 코드 편집기 및 개발 도구를 사용하여 전기능 단일 노드 로컬 런타임을 통해 Azure Event Hub, IoT Hub 및 Blob Storage의 라이브 이벤트 스트림으로 쉽게 작업을 만들고 테스트할 수 있습니다. 로컬 개발 환경에서 직접 Azure에 작업을 제출할 수도 있습니다.

## <a name="local-development-environments"></a>로컬 개발 환경

로컬 컴퓨터에서 Stream Analytics 작업을 개발하는 방식은 도구 기본 설정 및 기능 가용성에 따라 달라집니다. 각 개발 환경에 대해 지원되는 기능을 확인하려면 [Azure Stream Analytics 기능 비교](feature-comparison.md)를 참조하세요.

다음 표에서 환경은 로컬 개발을 지원합니다.

|환경                              |Description    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio Code용 [Azure Stream Analytics Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa)을 사용하여 풍부한 IntelliSense 및 네이티브 소스 제어를 통해 로컬 및 클라우드 둘 다에서 Stream Analytics 작업을 작성, 관리, 테스트할 수 있습니다. Linux, MacOS 및 Windows에서 개발을 지원합니다. 자세히 알아보려면 [Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)를 참조하세요. 또한 이 확장은 클라우드 호스팅 개발 환경인 [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)도 지원합니다.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics 도구는 Visual Studio에서 Azure 개발과 데이터 스토리지 및 처리 워크로드의 일부입니다. Visual Studio를 사용하여 사용자 지정 C# 사용자 정의 함수 및 역직렬 변환기를 작성할 수 있습니다. 자세히 알아보려면 [Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)를 참조하세요.|
|[명령 프롬프트 또는 터미널](stream-analytics-tools-for-visual-studio-cicd.md)|Azure Stream Analytics CI/CD NuGet 패키지는 임의 머신에서 Visual Studio 프로젝트 빌드, 로컬 테스트를 위한 도구를 제공합니다. Azure Stream Analytics CI/CD npm 패키지는 임의 머신에서 Visual Studio Code 프로젝트 빌드(Azure Resource Manager 템플릿 생성)를 위한 도구를 제공합니다.|

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용하여 라이브 스트림 입력에 대해 로컬로 Azure Stream Analytics 쿼리 테스트](visual-studio-code-local-run-live-input.md)
* [Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md)
