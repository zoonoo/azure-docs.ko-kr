---
title: IoT Edge의 Azure Stream Analytics
description: Azure Stream Analytics에서 Edge 작업을 만들고 Azure IoT Edge가 실행되는 디바이스에 배포합니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 65f1ad93bf711f7f7efe95c38619390dde527dd0
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827243"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge의 Azure Stream Analytics
 
IoT Edge의 Azure Stream Analytics를 통해 개발자는 장치 생성 데이터의 전체 값을 잠금 해제할 수 있도록 거의 실시간 분석 인텔리전스를 IoT 장치에 더 가깝게 배포할 수 있습니다. Azure Stream Analytics는 짧은 대기 시간, 복원력, 대역폭의 효율적 사용 및 규정 준수를 위해 고안되었습니다. 기업은 산업 운영에 가까운 제어 논리를 배포 하 고 클라우드에서 수행 된 빅 데이터 분석을 보완할 수 있습니다.

IoT Edge의 Azure Stream Analytics는 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 프레임워크 내에서 실행됩니다. 작업이 Stream Analytics에서 만들어지면 IoT Hub를 사용 하 여 배포 하 고 관리할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

이 섹션에서는 IoT Edge의 Stream Analytics에 대 한 일반적인 시나리오에 대해 설명 합니다. 다음 다이어그램에서는 IoT 장치와 Azure 클라우드 간의 데이터 흐름을 보여 줍니다.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge에 대 한 개략적인 다이어그램":::

### <a name="low-latency-command-and-control"></a>대기 시간이 짧은 명령 및 컨트롤

제조 안전 시스템은 매우 짧은 대기 시간으로 운영 데이터에 응답 해야 합니다. IoT Edge에 대 한 Stream Analytics를 사용 하 여 거의 실시간으로 센서 데이터를 분석 하 고 컴퓨터를 중지 하거나 경고를 트리거할 수 있는 변칙을 감지 하면 명령을 실행할 수 있습니다.

### <a name="limited-connectivity-to-the-cloud"></a>클라우드에 대 한 제한 된 연결

원격 마이닝 장비, 연결된 선박 또는 해양 굴착 같은 중요한 업무용 시스템은 클라우드 연결이 간헐적으로 끊어지더라도 데이터를 분석하고 대응해야 합니다. Stream Analytics를 사용 하는 경우 스트리밍 논리는 네트워크 연결과 독립적으로 실행 되며 추가 처리 또는 저장소로 클라우드로 전송 되는 항목을 선택할 수 있습니다.

### <a name="limited-bandwidth"></a>제한 된 대역폭

제트 엔진 또는 연결된 자동차에서 생성하는 데이터 양이 매우 커서 데이터를 클라우드로 전송하기 전에 필터링하고 사전 처리해야 합니다. Stream Analytics를 사용 하 여 클라우드로 전송 해야 하는 데이터를 필터링 하거나 집계할 수 있습니다.

### <a name="compliance"></a>규정 준수

규정 준수에 따라 일부 데이터를 클라우드로 전송하기 전에 로컬로 익명화하거나 집계해야 할 수 있습니다.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics의 Edge 작업

Stream Analytics Edge 작업은 [Azure IoT Edge 장치](../iot-edge/about-iot-edge.md)에 배포 된 컨테이너에서 실행 됩니다. Edge 작업은 다음 두 부분으로 구성 됩니다.

* 작업 정의를 담당 하는 클라우드 파트: 사용자는 클라우드에서 입력, 출력, 쿼리 및 기타 설정 (예: 잘못 된 이벤트)을 정의 합니다.

* IoT 디바이스에서 실행되는 모듈입니다. 모듈은 Stream Analytics 엔진을 포함 하며 클라우드에서 작업 정의를 수신 합니다. 

Stream Analytics IoT Hub를 사용 하 여에 지 작업을 장치에 배포 합니다. 자세한 내용은 [IoT Edge 배포](../iot-edge/module-deployment-monitoring.md)를 참조 하세요.

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics Edge 작업":::

## <a name="edge-job-limitations"></a>Edge 작업 제한 사항

목표는 IoT Edge 작업과 클라우드 작업 간에 패리티를 유지하는 것입니다. Edge와 클라우드 모두에 대해 대부분의 SQL 쿼리 언어 기능을 지원 합니다. 그러나 다음 기능은 edge 작업에 대해 지원 되지 않습니다.
* JavaScript의 UDF(사용자 정의 함수). UDF는 [IoT Edge 작업용 C#](./stream-analytics-edge-csharp-udf.md)(미리 보기)에서 사용할 수 있습니다.
* UDA(사용자 정의 집계).
* Azure ML 함수
* AVRO 형식의 입/출력 현재 CSV 및 JSON만 지원됩니다.
* 다음 SQL 연산자:
    * PARTITION BY
    * GetMetadataPropertyValue
* 지연 도착 정책

### <a name="runtime-and-hardware-requirements"></a>런타임 및 하드웨어 요구 사항
IoT Edge에서 Stream Analytics를 실행 하려면 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)를 실행할 수 있는 장치가 필요 합니다. 

Stream Analytics 및 Azure IoT Edge **Docker** 컨테이너를 사용 하 여 여러 호스트 운영 체제 (Windows, Linux)에서 실행 되는 이식 가능한 솔루션을 제공 합니다.

IoT Edge Stream Analytics은 x86-64 또는 ARM (Advanced RISC 컴퓨터) 아키텍처에서 실행 되는 Windows 및 Linux 이미지로 제공 됩니다. 


## <a name="input-and-output"></a>입력 및 출력

Stream Analytics Edge 작업은 IoT Edge 장치에서 실행 되는 다른 모듈에서 입력 및 출력을 가져올 수 있습니다. 특정 모듈과 연결하려면 배포 시에 라우팅 구성을 설정할 수 있습니다. 자세한 내용은 [IoT Edge 모듈 컴퍼지션 설명서](../iot-edge/module-composition.md)를 참조하세요.

입/출력 모두에서 CSV 및 JSON 형식이 지원됩니다.

Stream Analytics 작업에서 만드는 각 입력 및 출력 스트림에 대해 해당 끝점은 배포 된 모듈에 생성 됩니다. 이러한 엔드포인트는 배포 경로에서 사용될 수 있습니다.

지원 되는 스트림 입력 형식은 다음과 같습니다.
* 에지 허브
* 이벤트 허브
* IoT Hub

지원 되는 스트림 출력 유형은 다음과 같습니다.
* 에지 허브
* SQL Database
* 이벤트 허브
* Blob Storage/ADLS Gen2

참조 입력은 참조 파일 유형을 지원합니다. 다른 출력은 클라우드 작업 다운스트림을 사용하여 도달할 수 있습니다. 예를 들어 Edge에서 호스트되는 Stream Analytics 작업은 Edge Hub에 출력을 보냅니다. 그러면 IoT Hub에 출력을 보낼 수 있습니다. IoT Hub에서 입력 하 고 Power BI 또는 다른 출력 형식으로 출력 하는 두 번째 클라우드 호스팅 Azure Stream Analytics 작업을 사용할 수 있습니다.

## <a name="license-and-third-party-notices"></a>라이선스 및 타사 알림
* [IoT Edge의 Azure Stream Analytics 라이선스](https://go.microsoft.com/fwlink/?linkid=862827) 
* [IoT Edge의 Azure Stream Analytics에 대한 타사 알림](https://go.microsoft.com/fwlink/?linkid=862828)

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics 모듈 이미지 정보 

이 버전 정보는 2020-09-21에서 마지막으로 업데이트 되었습니다.

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

* [Azure IoT Edge에 대 한 자세한 정보](../iot-edge/about-iot-edge.md)
* [IoT Edge 자습서의 Stream Analytics](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [API를 사용하여 Stream Analytics를 위한 CI/CD 구현](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
