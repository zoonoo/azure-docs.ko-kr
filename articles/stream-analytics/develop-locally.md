---
title: Azure 스트림 분석 작업 개발 및 디버깅 로컬
description: Azure 포털에서 작업을 실행하기 전에 로컬 컴퓨터에서 Azure Stream Analytics 작업을 개발하고 테스트하는 방법을 알아봅니다.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879844"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure 스트림 분석 작업 개발 및 디버깅 로컬

Azure 포털에서 Azure Stream Analytics 작업을 만들고 테스트할 수 있지만 많은 개발자는 로컬 개발 환경을 선호합니다. Stream Analytics를 사용하면 즐겨 찾는 코드 편집기 및 개발 도구를 사용하여 완벽하게 작동하는 단일 노드 로컬 런타임을 사용하여 Azure Event Hub, IoT Hub 및 Blob Storage의 라이브 이벤트 스트림을 사용하여 작업을 만들고 테스트할 수 있습니다. 로컬 개발 환경에서 직접 작업을 Azure에 제출할 수도 있습니다.

## <a name="local-development-environments"></a>로컬 개발 환경

로컬 컴퓨터에서 Stream Analytics 작업을 개발하는 방법은 도구 기본 설정 및 기능 가용성에 따라 다릅니다. Azure [Stream Analytics 기능 비교를](feature-comparison.md) 참조하여 각 개발 환경에 대해 지원되는 기능을 확인합니다.

다음 표에서 환경은 로컬 개발을 지원합니다.

|Environment                              |Description    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio 코드용 [Azure 스트림 분석 도구 확장을](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) 사용하면 풍부한 IntelliSense 및 네이티브 소스 제어를 통해 로컬 및 클라우드에서 스트림 분석 작업을 작성, 관리 및 테스트할 수 있습니다. 리눅스, 맥 OS 및 Windows에서 개발을 지원합니다. 자세한 내용은 [Visual Studio 코드에서 Azure 스트림 분석 작업 만들기를](quick-create-vs-code.md)참조하십시오.|
|[비주얼 스튜디오 2019](stream-analytics-tools-for-visual-studio-install.md) |스트림 분석 도구는 Visual Studio의 Azure 개발 및 데이터 저장 및 처리 워크로드의 일부입니다. Visual Studio를 사용하여 사용자 지정 C# 사용자 정의 함수 및 역직렬화자를 작성할 수 있습니다. 자세한 내용은 [Visual Studio를 사용하여 Azure 스트림 분석 작업 만들기를](stream-analytics-quick-create-vs.md)참조하세요.|
|[명령 프롬프트 또는 터미널](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 스트림 분석 CI/CD NuGet 패키지는 임의의 컴퓨터에서 시각적 스튜디오 프로젝트 빌드, 로컬 테스트를 위한 도구를 제공합니다. Azure 스트림 분석 CI/CD npm 패키지는 임의의 컴퓨터에서 Visual Studio 코드 프로젝트 빌드(Azure Resource Manager 템플릿생성)를 위한 도구를 제공합니다.|

## <a name="next-steps"></a>다음 단계

* [Visual Studio 코드를 사용하여 샘플 데이터로 로컬에서 테스트 스트림 분석 쿼리](visual-studio-code-local-run.md)
* [Visual Studio 코드를 사용하여 실시간 스트림 입력에 대해 로컬로 스트림 분석 쿼리 테스트](visual-studio-code-local-run-live-input.md)
* [Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md)
