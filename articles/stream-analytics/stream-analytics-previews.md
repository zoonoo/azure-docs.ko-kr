---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7391fbccaf7983a070d80da64a2908333280420b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83609004"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>관리 ID를 사용하여 SQL Database 출력에 인증

Azure Stream Analytics는 Azure SQL Database 출력 싱크에 대한 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 지원합니다. 관리 ID는 암호 변경으로 인한 재인증 필요성이나 90일마다 발생하는 사용자 토큰 만료와 같은 사용자 기반 인증 방법의 제한을 없애줍니다. 수동으로 인증할 필요가 없어지면 Stream Analytics 배포를 완전히 자동화할 수 있습니다.

### <a name="output-to-azure-synapse-analytics"></a>Azure Synapse Analytics에 출력

Azure Stream Analytics 작업은 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics)의 SQL 풀 테이블로 출력될 수 있으며 최대 200MB/초의 속도로 처리할 수 있습니다. 이는 보고 및 대시보드와 같은 워크로드를 위한 가장 까다로운 실시간 분석 및 핫 경로 데이터 처리 요구를 지원합니다.  


### <a name="online-scaling"></a>온라인 크기 조정

온라인 크기 조정을 사용하면 SU 할당을 변경해야 하는 경우 작업을 중지할 필요가 없습니다. 실행 중인 작업을 중지하지 않고도 SU 용량을 늘리거나 줄일 수 있습니다. 이는 오늘날 Stream Analytics가 제공하는 장기 실행 중요 업무용 파이프라인에 대한 고객 약속을 기반으로 합니다. 자세한 내용은 [Azure Stream Analytics 스트리밍 단위 구성](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)을 참조하세요.

### <a name="c-custom-de-serializers"></a>C# 사용자 지정 역직렬 변환기
개발자는 Azure Stream Analytics 기능을 활용하여 Protobuf, XML 또는 사용자 지정 형식의 데이터를 처리할 수 있습니다. C#에서 [사용자 지정 역직렬 변환기](custom-deserializer-examples.md)를 구현하여 Azure Stream Analytics에서 수신한 이벤트를 역직렬화하는 데 사용할 수 있습니다.

### <a name="extensibility-with-c-custom-code"></a>C# 사용자 지정 코드를 사용한 확장 가능 여부

클라우드 또는 IoT Edge에서 Stream Analytics 모듈을 만드는 개발자는 사용자 지정 C# 함수를 작성하거나 다시 사용할 수 있으며, [사용자 정의 함수](stream-analytics-edge-csharp-udf-methods.md)를 통해 쿼리에서 직접 호출할 수 있습니다.


### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio에서의 쿼리 디버그 단계

Visual Studio용 Azure Stream Analytics 도구에서 로컬 테스트를 수행할 때 데이터 다이어그램의 중간 행 세트를 손쉽게 미리 볼 수 있습니다. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Visual Studio Code의 라이브 데이터를 사용하여 로컬 테스트

Azure에 작업을 제출하기 전에 로컬 컴퓨터의 라이브 데이터에 대해 쿼리를 테스트할 수 있습니다. 각 테스트 반복은 평균 2 ~ 3초 미만이 소요되므로 개발 프로세스가 매우 효율적입니다.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Azure Stream Analytics용 Visual Studio Code

Azure Stream Analytics 작업은 Visual Studio Code에서 작성할 수 있습니다. [VS Code 시작 자습서](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)를 참조하세요.


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning에서 관리하는 사용자 지정 ML 모델을 사용하는 실시간 고성능 점수 매기기

Azure Stream Analytics는 코드를 작성할 필요가 없는 워크플로를 사용하는 Azure Machine Learning에서 관리하며, AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에서 호스팅되는 사용자 지정의 미리 학습된 Machine Learning 모델을 활용하여 고성능의 실시간 점수 매기기를 지원합니다. 미리 보기에 [등록](https://aka.ms/asapreview1)하세요.


### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="other-previews"></a>기타 미리 보기

다음 기능은 요청 시 미리 보기에서도 사용할 수 있습니다.

### <a name="support-for-azure-stack"></a>Azure Stack 지원
Azure IoT Edge 런타임에 사용하도록 설정된 이 기능은 Azure Stack에서 실행되는 로컬 입력 및 출력에 대한 기본 지원과 같은 사용자 지정 Azure Stack 기능을 활용합니다(예: 이벤트 허브, IoT 허브, Blob Storage). 이 새로운 통합을 사용하면 데이터를 생성 위치에 가깝게 분석하여 대기 시간을 줄이고 인사이트를 극대화하는 하이브리드 아키텍처를 구축할 수 있습니다.
이 기능을 사용하면 데이터를 생성 위치와 가깝게 분석하여 대기 시간을 낮추고 인사이트를 극대화하는 하이브리드 아키텍처를 구축할 수 있습니다. 이 미리 보기에 [등록](https://aka.ms/asapreview1)해야 합니다.
