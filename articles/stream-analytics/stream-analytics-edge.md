---
title: IoT Edge의 Azure Stream Analytics
description: Azure Stream Analytics에서 Edge 작업을 만들고 Azure IoT Edge가 실행되는 디바이스에 배포합니다.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012617"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge의 Azure Stream Analytics
 
IoT Edge의 Azure Stream Analytics는 개발자가 IoT 장치에 보다 가깝게 거의 실시간으로 분석 인텔리전스를 배포하여 장치 생성 데이터의 가치를 극대화할 수 있도록 합니다. Azure Stream Analytics는 짧은 대기 시간, 복원력, 대역폭의 효율적 사용 및 규정 준수를 위해 고안되었습니다. 기업에서는 업계 운영과 밀접한 제어 논리를 배포하고, 클라우드에서 수행된 빅 데이터 분석을 보완할 수 있습니다.

IoT Edge의 Azure Stream Analytics는 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 프레임워크 내에서 실행됩니다. Stream Analytics에서 작업을 만든 후에는 IoT Hub를 사용하여 배포 및 관리할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

이 섹션에서는 IoT Edge의 Stream Analytics에 대한 일반적인 시나리오를 설명합니다. 다음 다이어그램에는 IoT 장치와 Azure 클라우드 간의 데이터 흐름이 나와 있습니다.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge의 고급 다이어그램":::

### <a name="low-latency-command-and-control"></a>낮은 대기 시간의 명령 및 제어

제조 안전 시스템은 매우 짧은 대기 시간으로 작동 데이터에 응답해야 합니다. IoT Edge의 Stream Analytics를 사용하면 변칙이 검색되었을 때 근 실시간으로 센서 데이터를 분석하고 명령을 실행하여 컴퓨터를 중지하거나 경고를 트리거할 수 있습니다.

### <a name="limited-connectivity-to-the-cloud"></a>클라우드에 대한 연결 제한

원격 마이닝 장비, 연결된 선박 또는 해양 굴착 같은 중요한 업무용 시스템은 클라우드 연결이 간헐적으로 끊어지더라도 데이터를 분석하고 대응해야 합니다. Stream Analytics를 사용하는 경우 스트리밍 논리는 네트워크 연결과는 별개로 실행되며 추가 처리 또는 스토리지용 클라우드로 전송되는 항목을 선택할 수 있습니다.

### <a name="limited-bandwidth"></a>제한된 대역폭

제트 엔진 또는 연결된 자동차에서 생성하는 데이터 양이 매우 커서 데이터를 클라우드로 전송하기 전에 필터링하고 사전 처리해야 합니다. Stream Analytics를 사용하여 클라우드로 전송해야 하는 데이터를 필터링하거나 집계할 수 있습니다.

### <a name="compliance"></a>규정 준수

규정 준수에 따라 일부 데이터를 클라우드로 전송하기 전에 로컬로 익명화하거나 집계해야 할 수 있습니다.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics의 Edge 작업

Stream Analytics Edge 작업은 [Azure IoT Edge 장치](../iot-edge/about-iot-edge.md)에 배포된 컨테이너에서 실행됩니다. Edge 작업은 다음의 두 부분으로 구성됩니다.

* 작업 정의를 담당하는 클라우드 부분: 사용자가 클라우드의 입력, 출력, 쿼리 및 기타 설정(예: 잘못된 이벤트)을 정의합니다.

* IoT 디바이스에서 실행되는 모듈입니다. 모듈은 Stream Analytics 엔진을 포함하며, 클라우드에서 작업 정의를 수신합니다. 

Stream Analytics는 IoT Hub를 사용하여 장치에 Edge 작업을 배포합니다. 자세한 내용은 [IoT Edge 배포](../iot-edge/module-deployment-monitoring.md)를 참조하세요.

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics Edge 작업":::

## <a name="edge-job-limitations"></a>에지 작업 제한 사항

목표는 IoT Edge 작업과 클라우드 작업 간에 패리티를 유지하는 것입니다. 대부분의 SQL 쿼리 언어 기능은 에지와 클라우드에서 모두 지원됩니다. 그러나 다음 기능은 에지 작업에서는 아직 지원되지 않습니다.
* JavaScript의 UDF(사용자 정의 함수). UDF는 [IoT Edge 작업용 C#](./stream-analytics-edge-csharp-udf.md)(미리 보기)에서 사용할 수 있습니다.
* UDA(사용자 정의 집계).
* Azure ML 함수
* AVRO 형식의 입/출력 현재 CSV 및 JSON만 지원됩니다.
* 다음 SQL 연산자:
    * PARTITION BY
    * GetMetadataPropertyValue
* 지연 도착 정책

### <a name="runtime-and-hardware-requirements"></a>런타임 및 하드웨어 요구 사항
IoT Edge에서 Stream Analytics를 실행하려면 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)를 실행할 수 있는 장치가 필요합니다. 

Stream Analytics 및 Azure IoT Edge는 **Docker** 컨테이너를 사용하여 여러 호스트 운영 체제(Windows, Linux)에서 실행되는 이식 가능한 솔루션을 제공합니다.

IoT Edge의 Stream Analytics는 x86-64 또는 ARM(Advanced RISC Machines) 아키텍처에서 모두 실행되는 Windows 및 Linux 이미지로 사용할 수 있습니다. 


## <a name="input-and-output"></a>입력 및 출력

Stream Analytics Edge 작업은 IoT Edge 장치에서 실행되는 다른 모듈의 입/출력을 가져올 수 있습니다. 특정 모듈과 연결하려면 배포 시에 라우팅 구성을 설정할 수 있습니다. 자세한 내용은 [IoT Edge 모듈 컴퍼지션 설명서](../iot-edge/module-composition.md)를 참조하세요.

입/출력 모두에서 CSV 및 JSON 형식이 지원됩니다.

Stream Analytics 작업에서 만드는 각각의 입/출력 스트림의 경우 배포된 모듈에 해당 엔드포인트가 만들어집니다. 이러한 엔드포인트는 배포 경로에서 사용될 수 있습니다.

지원되는 스트림 입력 형식은 다음과 같습니다.
* 에지 허브
* 이벤트 허브
* IoT Hub

지원되는 스트림 출력 형식은 다음과 같습니다.
* 에지 허브
* SQL Database
* 이벤트 허브
* Blob Storage/ADLS Gen2

참조 입력은 참조 파일 유형을 지원합니다. 다른 출력은 클라우드 작업 다운스트림을 사용하여 도달할 수 있습니다. 예를 들어 Edge에서 호스트되는 Stream Analytics 작업은 Edge Hub에 출력을 보냅니다. 그러면 IoT Hub에 출력을 보낼 수 있습니다. IoT Hub에서의 입력과 Power BI 또는 다른 출력 형식으로의 출력을 통해 두 번째 클라우드에 호스팅된 Azure Stream Analytics 작업을 사용할 수 있습니다.

## <a name="license-and-third-party-notices"></a>라이선스 및 타사 알림
* [IoT Edge의 Azure Stream Analytics 라이선스](https://go.microsoft.com/fwlink/?linkid=862827) 
* [IoT Edge의 Azure Stream Analytics에 대한 타사 알림](https://go.microsoft.com/fwlink/?linkid=862828)

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics 모듈 이미지 정보 

이 버전 정보는 2020년 9월 21일에 마지막으로 업데이트되었습니다.

- 이미지: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - 기본 이미지: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - 플랫폼:
      - 아키텍처: amd64
      - os: linux
 
- 이미지: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - 기본 이미지: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - 플랫폼:
      - 아키텍처: arm
      - os: linux
 
- 이미지: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - 기본 이미지: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - 플랫폼:
      - 아키텍처: arm64
      - os: linux
      
      
## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)를 사용해보세요.

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge에 대한 추가 정보](../iot-edge/about-iot-edge.md)
* [IoT Edge의 Stream Analytics 자습서](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [API를 사용하여 Stream Analytics를 위한 CI/CD 구현](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
