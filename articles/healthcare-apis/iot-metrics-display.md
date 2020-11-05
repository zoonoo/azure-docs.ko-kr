---
title: Azure IoT Connector for FHIR 보기 및 구성(미리 보기) 메트릭
description: 이 문서에서는 FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터를 표시 하 고 구성 하는 방법을 설명 합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 10/29/2020
ms.author: jasteppe
ms.openlocfilehash: 1cdae789b8286be408735fff92e2de46e28ce514
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394291"
---
# <a name="view-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>Azure IoT Connector for FHIR 보기 및 구성(미리 보기) 메트릭 

이 문서에서는 FHIR * 메트릭에 대 한 Azure IoT 커넥터를 보고 구성 하는 방법에 대해 알아봅니다. 

> [!TIP]
> [진단 설정을 통해 FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터 내보내기](./iot-metrics-diagnostics-export.md) 의 지침에 따라 메트릭 데이터의 내보내기를 설정 하는 방법을 알아봅니다.

## <a name="view-metrics-for-azure-iot-connector-for-fhir-preview"></a>FHIR 용 Azure IoT 커넥터 (미리 보기)에 대 한 메트릭 보기
1. IoT 커넥터에 대 한 메트릭을 보려면 Azure Portal에서 Azure API for FHIR 서비스를 선택 합니다. 

2. **메트릭** 탐색 

3. **IoT 커넥터** 탭을 선택 합니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="IoT Connector1" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. IoT 커넥터를 선택 하 여 메트릭을 볼 수 있습니다. 예를 들어이 Azure API for FHIR 서비스와 연결 된 IoT 커넥터가 있습니다 (4).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector2" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

> [!NOTE]
> **사용자 지정** 탭에서는 IoT 커넥터 메트릭을 보기 위한 특정 시간/날짜 조합을 만들 수 있습니다.

5. 표시할 IoT 커넥터 메트릭의 기간을 선택 합니다 (예: 1 시간, 24 시간, 7 일 또는 사용자 지정).

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="IoT Connector3" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metrics-types-for-azure-iot-connector-for-fhir-preview"></a>FHIR 용 Azure IoT 커넥터 메트릭 유형 (미리 보기) 
표시 되는 IoT 커넥터 메트릭은 다음과 같습니다.

|메트릭 유형|메트릭 용도| 
|-----------|--------------|
|들어오는 메시지 수|받은 원시 수신 메시지 수입니다 (예: 장치 이벤트).|
|정규화 된 메시지 수|정규화 된 메시지의 수입니다.|
|메시지 그룹 수|지정 된 기간에 집계 된 메시지를 포함 하는 그룹의 수입니다.|
|평균 정규화 된 단계 대기 시간|정규화 단계의 평균 대기 시간입니다. 단계 표준화는 들어오는 원시 메시지에 대해 정규화를 수행 하는 것입니다.|
|평균 그룹 단계 대기 시간|그룹 단계의 평균 대기 시간입니다. 그룹 단계는 정규화 된 메시지에서 버퍼링, 집계 및 그룹화를 수행 하는 것입니다.| 
|총 오류 수|총 오류 수입니다.| 

## <a name="focusing-and-configuring-azure-iot-connector-for-fhir-preview-metrics"></a>FHIR (미리 보기) 메트릭에 대 한 Azure IoT 커넥터 집중 및 구성
이 예제에서는 **들어오는 메시지 메트릭의 수** 를 집중적으로 살펴봅니다.

1. 초점을 맞출 특정 시점을 선택 합니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="IoT Connector4" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. 이 화면에서 **메트릭을 추가** 하 고 필터를 **추가** 하 고 추가 사용자 지정을 위해 **분할을 적용할** 수 있습니다. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="IoT Connector5" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>결론 
데이터 평면 메트릭에 대 한 액세스는 모니터링 및 문제 해결에 필수적입니다.  FHIR 용 Azure IoT 커넥터는 메트릭을 통해 이러한 작업을 수행 하는 데 도움이 됩니다. 

## <a name="next-steps"></a>다음 단계

FHIR 용 Azure IoT 커넥터에 대 한 자주 묻는 질문을 확인 합니다.

>[!div class="nextstepaction"]
>[FHIR Faq 용 Azure IoT 커넥터](fhir-faq.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.