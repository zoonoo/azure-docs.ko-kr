---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878309"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="online-scaling"></a>온라인 스케일링

온라인 확장의 경우 SU 할당을 변경해야 하는 경우 작업을 중지할 필요가 없습니다. 중지하지 않고도 실행 중인 작업의 SU 용량을 늘리거나 줄일 수 있습니다. 이는 Stream Analytics가 오늘날 제공하는 장기 실행 미션 크리티컬 파이프라인에 대한 고객의 약속을 기반으로 합니다. 자세한 내용은 [Azure 스트림 분석 스트리밍 단위 구성을](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)참조하십시오.

### <a name="c-custom-de-serializers"></a>C# 사용자 지정 직렬 화자
개발자는 Azure Stream Analytics의 기능을 활용하여 Protobuf, XML 또는 사용자 지정 형식으로 데이터를 처리할 수 있습니다. C#에서 [사용자 지정 직렬 화자를](custom-deserializer-examples.md) 구현할 수 있으며, 이 경우 Azure Stream Analytics에서 수신한 이벤트를 직렬화해제하는 데 사용할 수 있습니다.

### <a name="extensibility-with-c-custom-code"></a>C# 사용자 지정 코드로 확장성

클라우드 또는 IoT Edge에서 Stream Analytics 모듈을 만드는 개발자는 사용자 정의 C# 함수를 작성하거나 재사용하고 [사용자 정의 함수를](stream-analytics-edge-csharp-udf-methods.md)통해 쿼리에서 직접 호출할 수 있습니다.


### <a name="debug-query-steps-in-visual-studio"></a>비주얼 스튜디오에서 쿼리 단계 디버그

Visual Studio용 Azure Stream Analytics 도구에서 로컬 테스트를 수행할 때 데이터 다이어그램에서 설정된 중간 행을 쉽게 미리 볼 수 있습니다. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>비주얼 스튜디오 코드의 라이브 데이터로 로컬 테스트

작업을 Azure에 제출하기 전에 로컬 컴퓨터의 라이브 데이터에 대해 쿼리를 테스트할 수 있습니다. 각 테스트 반복은 평균 2~3초 미만이 소요되어 매우 효율적인 개발 프로세스가 이루어집니다.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics용 Visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. 우리의 [VS 코드를 시작 자습서를](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)참조하십시오 .


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure 기계 학습에서 관리하는 사용자 지정 ML 모델로 실시간 고성능 점수 매기기

Azure Stream Analytics는 코드를 작성할 필요가 없는 워크플로를 사용하여 Azure Kubernetes 서비스(AKS) 또는 Azure 컨테이너 인스턴스(ACI)에서 호스팅되는 사용자 지정 사전 학습된 기계 학습 모델을 활용하여 고성능 실시간 채점을 지원합니다. 미리 보기 [등록](https://aka.ms/asapreview1)


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="other-previews"></a>기타 미리 보기

다음 기능은 요청 시 미리 보기에서도 사용할 수 있습니다.

### <a name="support-for-azure-stack"></a>Azure 스택에 대한 지원
Azure IoT Edge 런타임에서 활성화된 이 기능은 Azure 스택에서 실행되는 로컬 입력 및 출력에 대한 기본 지원과 같은 사용자 지정 Azure 스택 기능을 활용합니다(예: 이벤트 허브, IoT Hub, Blob 저장소). 이 새로운 통합을 통해 데이터가 생성된 위치에 가깝게 데이터를 분석하여 대기 시간을 줄이며 통찰력을 극대화할 수 있는 하이브리드 아키텍처를 구축할 수 있습니다.
이 기능을 사용하면 데이터가 생성된 위치에 가깝게 데이터를 분석하여 대기 시간을 줄이며 통찰력을 극대화할 수 있는 하이브리드 아키텍처를 구축할 수 있습니다. 이 미리 보기에 [등록해야](https://aka.ms/asapreview1) 합니다.
