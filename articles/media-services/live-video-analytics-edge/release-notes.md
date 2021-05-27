---
title: Azure Live Video Analytics on IoT Edge 릴리스 정보 - Azure
description: 이 토픽에서는 Azure Live Video Analytics on IoT Edge 릴리스 정보, 개선 사항, 버그 수정 및 알려진 문제에 대한 정보를 제공합니다.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 67b30cfbcb374dbe8773023f67b77ef43b35f3ba
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372049"
---
# <a name="azure-live-video-analytics-on-iot-edge-release-notes"></a>Azure Live Video Analytics on IoT Edge 릴리스 정보

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

<hr width=100%>

## <a name="january-12-2021"></a>2021년 1월 12일

모듈의 2021년 1월 새로 고침에 대한 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:2.0.1
```

> [!NOTE]
> 빠른 시작 및 자습서의 배포 매니페스트는 태그 2(live-video-analytics:2)를 사용합니다. 따라서 간단하게 이러한 매니페스트를 다시 배포하면 에지 > 디바이스의 모듈이 업데이트됩니다.
### <a name="bug-fixes"></a>버그 수정 

* 신호 게이트 프로세서의 `ActivationSignalOffset`, `MinimumActivationTime` 및 `MaximumActivationTime` 필드가 필수 속성으로 잘못 설정되었습니다. 이제 이러한 필드는 **선택적** 속성입니다.
* 특정 지역에 배포할 경우 IoT Edge 모듈의 Live Video Analytics가 충돌하는 사용 버그를 수정했습니다.

<hr width=100%>

## <a name="december-14-2020"></a>2020년 12월 14일
이 릴리스는 Live Video Analytics on IoT Edge 공개 미리 보기 새로 고침 릴리스입니다. 릴리스 태그는 다음과 같습니다.

```
     mcr.microsoft.com/media/live-video-analytics:2.0.0
```
### <a name="module-updates"></a>모듈 업데이트
* 그래프 토폴로지마다 두 개 이상의 HTTP 확장 프로세서 및 gRPC 확장 프로세서를 사용할 수 있도록 지원이 추가되었습니다.
* [싱크 노드의 디스크 공간 관리](upgrading-lva-module.md#disk-space-management-with-sink-nodes)에 대한 지원이 추가되었습니다.
* 이제 `MediaGraphGrpcExtension` 노드는 단일 gRPC 서버 내에서 여러 AI 모델을 사용하기 위한 [extensionConfiguration](grpc-extension-protocol.md) 속성을 지원합니다.
* [프로메테우스 형식](https://prometheus.io/docs/practices/naming/)의 Live Video Analytics 모듈 메트릭을 수집하기 위한 지원이 추가되었습니다. [Azure Monitor에서 메트릭 수집 및 보기](monitoring-logging.md#azure-monitor-collection-via-telegraf) 방법에 대해 자세히 알아보세요. 
* 출력 선택을 필터링하는 기능이 추가되었습니다. `outputSelectors`를 사용하여 **오디오만**, **비디오만** 또는 **오디오와 비디오 모두** 그래프 노드에 전달할 수 있습니다. 
* 프레임 속도 필터 프로세서가 **사용되지 않습니다**.  
    * 이제 그래프 확장 프로세서 자체에서 프레임 속도 관리를 사용할 수 있습니다.

### <a name="visual-studio-code-extension"></a>Visual Studio Code 확장
* LVA 미디어 그래프 관리를 도와주는 Visual Studio Code 확장인 [Live Video Analytics on IoT Edge](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)가 출시되었습니다. 이 확장은 **LVA 2.0 모듈** 에서 작동하며 매끈하고 사용하기 쉬운 그래픽 인터페이스를 통해 미디어 그래프를 편집하고 관리하는 기능을 제공합니다.
## <a name="september-22-2020"></a>2020년 9월 22일

모듈의 2020년 9월 새로 고침에 대한 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:1.0.4
```

> [!NOTE]
> 빠른 시작 및 자습서의 배포 매니페스트는 태그 1(live-video-analytics:1)을 사용합니다. 따라서 간단하게 이러한 매니페스트를 다시 배포하면 에지 > 디바이스의 모듈이 업데이트됩니다.

### <a name="module-updates"></a>모듈 업데이트

* 새 그래프 확장 노드인 [MediaGraphCognitiveServicesVisionExtension](spatial-analysis-tutorial.md)은 Cognitive Services의 [공간 분석](/legal/cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview)(미리 보기) 모듈과 통합할 수 있습니다.
* Linux ARM64 디바이스 지원이 추가되었습니다. 이러한 디바이스에 배포하려면 [수동 단계](deploy-iot-edge-device.md)를 사용합니다.

### <a name="documentation-updates"></a>설명서 업데이트

* [지침](deploy-azure-stack-edge-how-to.md)은 Azure Stack Edge 디바이스에서 Live Video Analytics on IoT Edge를 사용하는 데 이용할 수 있습니다.
* [Custom Vision 서비스](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)를 사용하여 시나리오별 컴퓨터 비전 모델을 개발하고 실시간 [라이브 비디오 분석](custom-vision-tutorial.md)에 사용하는 방법에 대한 새 자습서가 공개되었습니다.

<hr width=100%>

## <a name="august-19-2020"></a>2020년 8월 19일

모듈의 2020년 8월 새로 고침에 대한 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 빠른 시작 및 자습서의 배포 매니페스트는 태그 1(live-video-analytics:1)을 사용합니다. 따라서 간단하게 이러한 매니페스트를 다시 배포하면 에지 > 디바이스의 모듈이 업데이트됩니다.

### <a name="module-updates"></a>모듈 업데이트

* 이제 gRPC 프레임워크를 사용하여 Live Video Analytics on IoT Edge와 사용자 지정 확장 간에 높은 데이터 콘텐츠 전송 성능을 얻을 수 있습니다. 시작하려면 [빠른 시작](analyze-live-video-use-your-grpc-model-quickstart.md)을 참조하세요.
* 광범위한 Live Video Analytics 지역 배포와 클라우드 서비스가 업데이트되었습니다.  
* 이제 25개가 넘는 지역에서 Live Video Analytics를 사용할 수 있습니다. 사용 가능한 모든 지역의 [목록](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)은 다음과 같습니다.  
* 빠른 시작을 위한 [설정](https://aka.ms/lva-edge/setup-resources-for-samples)이 새로운 지역 지원과 함께 업데이트되었습니다.
    * 이미 리소스를 설치한 분들을 위한 활용 방안은 없습니다.

### <a name="bug-fixes"></a>버그 수정 

* 설치 스크립트에서 사용되지 않은 Azure 확장 사용 제거

<hr width=100%>

## <a name="july-13-2020"></a>2020년 7월 13일

모듈의 2020년 7월 새로 고침에 대한 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 빠른 시작 및 자습서의 배포 매니페스트는 태그 1(live-video-analytics:1)을 사용합니다. 따라서 간단하게 이러한 매니페스트를 다시 배포하면 에지 > 디바이스의 모듈이 업데이트됩니다.

### <a name="module-updates"></a>모듈 업데이트

* 이제 신호 게이트 프로세서 노드 다운스트림의 자산 싱크 노드 및 파일 싱크 노드를 포함하는 그래프 토폴로지를 만들 수 있습니다. 예제는 [토폴로지](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files)를 참조하세요.

### <a name="bug-fixes"></a>버그 수정

* desired 속성의 유효성 검사 기능 개선

<hr width=100%>

## <a name="june-1-2020"></a>2020년 6월 1일

이 릴리스는 Live Video Analytics on IoT Edge의 첫 번째 공개 미리 보기 릴리스입니다. 릴리스 태그는 다음과 같습니다.

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>지원되는 기능

* 원하는 AI 모듈을 사용하여 라이브 비디오 스트림을 분석하고 필요에 따라 에지 디바이스 또는 클라우드에서 비디오 녹화
* IoT Edge에서 [지원하는](../../iot-edge/support.md) Linux AMD64 운영 체제에서 사용
* Azure Portal 또는 Visual Studio Code를 사용하여 IoT Hub를 통해 모듈 배포 및 구성
* 다음 직접 메서드 호출을 통해 [그래프 토폴로지 및 그래프 인스턴스](media-graph-concept.md#media-graph-topologies-and-instances)를 원격 또는 로컬로 관리

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
