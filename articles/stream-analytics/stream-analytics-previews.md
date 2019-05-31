---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e63937cedf44b1642e091a4744d898a26422be10
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393682"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Azure Stream Analytics (2019 년 5 월 릴리스됨)에 대 한 visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. 참조 우리의 [VS Code 시작 자습서](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)합니다.

### <a name="anomaly-detection"></a>이상 감지

Azure Stream Analytics에는 양방향, 느린 긍정 및 느린 부정 추세 검색 외에도 *spike* 및 *dips* 검색을 지원하는 새로운 기계 학습 모델이 도입되었습니다. 자세한 내용은 [Azure Stream Analytics의 변칙 검색](stream-analytics-machine-learning-anomaly-detection.md)합니다.

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning과 통합

ML(Machine Learning) 함수로 Stream Analytics 작업을 확장할 수 있습니다. Stream Analytics 작업에서 ML 함수를 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기 조정](stream-analytics-scale-with-machine-learning-functions.md)을 참조하세요. [Azure Stream Analytics 및 Azure Machine Learning을 사용한 감정 분석 수행](stream-analytics-machine-learning-integration-tutorial.md)을 통해 실제 시나리오를 확인하세요.

### <a name="javascript-user-defined-aggregate"></a>JavaScript 사용자 정의 집계

Azure Stream Analytics는 JavaScript로 작성된 UDA(사용자 정의 집계)를 지원하고 이를 통해 복잡한 상태 저장 비즈니스 논리를 구현할 수 있습니다. UDA를 사용하는 방법은 [Azure Stream Analytics JavaScript 사용자 정의 집계](stream-analytics-javascript-user-defined-aggregates.md) 설명서를 참조하세요. 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="other-previews"></a>다른 미리 보기

다음과 같은 기능이 미리 보기에서 사용할 수 있습니다.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#IoT Edge 및 클라우드 (Announced 2019 년 5 월)의 Azure Stream Analytics에 대 한 사용자 지정 역직렬 변환기

개발자의 사용자 지정 deserializers를 구현할 수 있습니다 C# Azure Stream Analytics에서 받은 이벤트를 deserialize 합니다. 역직렬화할 수 있는 형식의 예로는 Parquet, Protobuf, XML 또는 임의의 이진 형식이 있습니다. 이 미리 보기에 등록 [여기](https://aka.ms/asapreview1)합니다.

### <a name="parquet-output-announced-may-2019"></a>Parquet 출력 (월 2019 발표)
Parquet에 효율적으로 빅 데이터 처리를 사용 하도록 설정 하면 열 형식입니다. Data lake에서 Parquet 형식으로 데이터를 출력 하 여 수 ETL 스트리밍 power 대규모 확장에 Azure Stream Analytics를 활용 및 일괄 처리를 실행, 기계 학습 알고리즘을 학습 또는 기록 데이터에서 대화형 쿼리를 실행 합니다. 이 미리 보기에 등록 [여기](https://aka.ms/asapreview1)합니다.

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Event Hubs (Announced 2019 년 5 월)를 사용 하 여 한 번의 클릭 통합 
이 통합을 사용 하 여 들어오는 데이터를 시각화 하 여 이벤트 허브 포털에서 한 번의 클릭을 사용 하 여 Stream Analytics 쿼리를 작성을 시작할 수 이제 합니다. 쿼리 준비 되 면 몇 번의 클릭 만으로 제품 화 및 실시간 통찰력을 제공 수 있습니다. 이렇게 하면 시간과 실시간 분석 솔루션 개발 비용을 크게 줄어듭니다. 이 미리 보기에 등록 [여기](https://aka.ms/asapreview1)합니다.

### <a name="support-for-azure-stack-announced-may-2019"></a>(월 2019 발표) Azure Stack에 대 한 지원
이 기능을 Azure IoT Edge 런타임에서 사용은 로컬 입력에 대 한 네이티브 지원과 같은 사용자 지정 Azure Stack 기능을 활용 하 고 (예: Event Hubs, IoT Hub, Blob Storage) Azure Stack에서 실행을 출력 합니다. 이 새로운 통합을 사용 하면 생성 된 위치, 대기 시간을 절감 및 통찰력을 극대화에 가까운 데이터를 분석할 수 있는 하이브리드 아키텍처를 빌드할 수 있습니다.
이 미리 보기에 등록 [여기](https://aka.ms/asapreview1)합니다.

