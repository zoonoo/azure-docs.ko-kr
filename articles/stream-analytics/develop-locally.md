---
title: Azure Stream Analytics 작업을 로컬로 개발 및 디버그
description: Azure Portal에서 실행 하기 전에 로컬 컴퓨터에서 Azure Stream Analytics 작업을 개발 하 고 테스트 하는 방법을 알아봅니다.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879844"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure Stream Analytics 작업을 로컬로 개발 및 디버그

Azure Portal에서 Azure Stream Analytics 작업을 만들고 테스트할 수 있지만 대부분의 개발자는 로컬 개발 환경을 선호 합니다. Stream Analytics를 사용 하면 즐겨 사용 하는 코드 편집기와 개발 도구를 사용 하 여 Azure Event Hub에서 라이브 이벤트 스트림을 사용 하 여 작업을 만들고 테스트 하 고, IoT Hub, 완전히 작동 하는 단일 노드 로컬 런타임을 사용 하 여 Blob Storage 수 있습니다. 로컬 개발 환경에서 직접 Azure에 작업을 제출할 수도 있습니다.

## <a name="local-development-environments"></a>로컬 개발 환경

로컬 컴퓨터에서 Stream Analytics 작업을 개발 하는 방법은 도구 기본 설정 및 기능 가용성에 따라 달라 집니다. 각 개발 환경에 대해 지원 되는 기능을 보려면 [Azure Stream Analytics 기능 비교](feature-comparison.md) 를 참조 하세요.

다음 표에서 환경은 로컬 개발을 지원합니다.

|환경                              |Description    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio Code 용 [Azure Stream Analytics 도구 확장](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) 을 사용 하면 풍부한 IntelliSense 및 네이티브 소스 제어를 사용 하 여 로컬 및 클라우드에서 Stream Analytics 작업을 작성, 관리 및 테스트할 수 있습니다. Linux, MacOS 및 Windows에서의 개발을 지원 합니다. 자세히 알아보려면 [Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)를 참조 하세요.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics 도구는 Visual Studio의 Azure 개발 및 데이터 저장 및 처리 워크 로드의 일부입니다. Visual Studio를 사용 하 여 사용자 지정 c # 사용자 정의 함수 및 deserializers를 작성할 수 있습니다. 자세히 알아보려면 [Visual Studio를 사용 하 여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)를 참조 하세요.|
|[명령 프롬프트 또는 터미널](stream-analytics-tools-for-visual-studio-cicd.md)|Azure Stream Analytics CI/CD NuGet 패키지는 Visual studio 프로젝트 빌드, 임의 컴퓨터에서 로컬 테스트를 위한 도구를 제공 합니다. Azure Stream Analytics CI/CD npm 패키지는 임의의 컴퓨터에서 Visual Studio Code 프로젝트 빌드 (Azure Resource Manager 템플릿 생성)를 위한 도구를 제공 합니다.|

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용 하 여 라이브 스트림 입력에 대해 로컬로 쿼리 Stream Analytics 테스트](visual-studio-code-local-run-live-input.md)
* [Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md)
