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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878309"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="online-scaling"></a>온라인 크기 조정

온라인 크기 조정을 사용 하는 경우 SU 할당을 변경 해야 하는 경우 작업을 중지할 필요가 없습니다. 실행 중인 작업을 중지 하지 않고도 SU 용량을 늘리거나 줄일 수 있습니다. 이는 오늘날 제공 Stream Analytics는 장기 실행 업무상 중요 한 파이프라인의 고객 약속을 기반으로 합니다. 자세한 내용은 [Azure Stream Analytics 스트리밍 단위 구성](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)을 참조 하세요.

### <a name="c-custom-de-serializers"></a>C # 사용자 지정 역직렬화
개발자는 Azure Stream Analytics 기능을 활용 하 여 Protobuf, XML 또는 사용자 지정 형식의 데이터를 처리할 수 있습니다. C #에서 [사용자 지정 역직렬화](custom-deserializer-examples.md) 를 구현할 수 있으며,이를 사용 하 여 Azure Stream Analytics에서 받은 이벤트를 deserialize 할 수 있습니다.

### <a name="extensibility-with-c-custom-code"></a>C # 사용자 지정 코드를 사용 하 여 확장성

클라우드 또는 IoT Edge에서 Stream Analytics 모듈을 작성 하는 개발자는 사용자 [정의 함수](stream-analytics-edge-csharp-udf-methods.md)를 통해 쿼리에서 직접 호출 하 고 사용자 지정 c # 함수를 작성 하거나 다시 사용할 수 있습니다.


### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio에서 쿼리 단계 디버그

Visual Studio 용 Azure Stream Analytics 도구에서 로컬 테스트를 수행할 때 데이터 다이어그램의 중간 행 집합을 쉽게 미리 볼 수 있습니다. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code에서 라이브 데이터를 사용 하 여 로컬 테스트

Azure에 작업을 제출 하기 전에 로컬 컴퓨터의 라이브 데이터에 대해 쿼리를 테스트할 수 있습니다. 각 테스트 반복은 평균에 2 ~ 3 초 미만이 소요 되므로 개발 프로세스가 매우 효율적입니다.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics용 Visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. [VS Code 시작 자습서](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)를 참조 하세요.


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning에서 관리 하는 사용자 지정 ML 모델을 사용 하는 실시간 고성능 점수 매기기

Azure Stream Analytics는 코드를 작성 하지 않아도 되는 워크플로를 사용 하 여 Azure Machine Learning에서 관리 하는 사용자 지정 미리 학습 된 Machine Learning 모델을 활용 하 고 AKS (Azure Kubernetes Service) 또는 ACI (Azure Container Instances)에서 호스트 하는 고성능의 실시간 점수 매기기를 지원 합니다. 미리 보기 [등록](https://aka.ms/asapreview1)


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="other-previews"></a>기타 미리 보기

다음 기능은 요청 시 미리 보기 에서도 사용할 수 있습니다.

### <a name="support-for-azure-stack"></a>Azure Stack에 대 한 지원
Azure IoT Edge 런타임에 사용 하도록 설정 된이 기능은 Azure Stack에서 실행 되는 로컬 입력 및 출력에 대 한 기본 지원과 같은 사용자 지정 Azure Stack 기능을 활용 합니다 (예: Event Hubs, IoT Hub Blob Storage). 이 새로운 통합을 사용 하면 데이터를 생성 된 위치와 가깝게 분석 하 여 대기 시간을 줄이고 통찰력을 극대화 하는 하이브리드 아키텍처를 구축할 수 있습니다.
이 기능을 사용 하면 데이터를 생성 된 위치와 가깝게 분석 하 여 대기 시간을 낮추고 정보를 극대화 하는 하이브리드 아키텍처를 구축할 수 있습니다. 이 미리 보기에 [등록](https://aka.ms/asapreview1) 해야 합니다.
