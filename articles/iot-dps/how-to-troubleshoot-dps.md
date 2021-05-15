---
title: Azure IoT Hub DPS의 연결 끊김 문제 진단 및 해결
description: Azure IoT Hub DPS(Device Provisioning Service)의 디바이스 연결에 대한 일반적인 오류를 진단하고 해결하는 방법을 알아봅니다.
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: fd7d4407f7ea552409599946d1c13a7e9d1d7268
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062542"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service 문제 해결

IoT 디바이스에 대한 연결 문제는 증명 실패, 등록 실패 등의 여러 가지 가능한 오류 지점이 있으므로 문제를 해결하기 어려울 수 있습니다. 이 문서에서는 [Azure Monitor](../azure-monitor/overview.md)를 통해 디바이스 연결 문제를 검색하고 해결하는 방법에 대한 지침을 제공합니다.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure Monitor를 사용하여 메트릭 보기 및 경고 설정

다음 프로시저에서는 IoT Hub Device Provisioning Service 메트릭에서 경고를 보고 설정하는 방법을 설명합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub Device Provisioning Service로 이동합니다.

3. **메트릭** 을 선택합니다.

4. 원하는 메트릭을 선택합니다. 
   <br />현재 DPS용 메트릭은 세 가지가 있습니다.

    | 메트릭 이름 | Description |
    |-------|------------|
    | 증명 시도 | Device Provisioning Service를 사용하여 인증을 시도한 디바이스 수|
    | 등록 시도 | 인증에 성공한 후 IoT Hub에 등록하려고 시도한 디바이스 수|
    | 디바이스 할당됨 | IoT Hub에 성공적으로 할당된 디바이스 수|

5. 메트릭의 시각적 보기를 만들려면 원하는 집계 메서드를 선택합니다. 

6. 메트릭 경고를 설정하려면 메트릭 블레이드의 오른쪽 위에서 **새 경고 규칙** 을 선택합니다. 마찬가지로 **경고** 블레이드로 이동하여 **새 경고 규칙** 을 선택할 수도 있습니다.

7. **조건 추가** 를 선택한 다음 프롬프트에 따라 원하는 메트릭 및 임계값을 선택합니다.

자세한 내용은 [Azure Monitor의 경고](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>로그 분석을 사용하여 오류 확인 및 해결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **진단 설정** 을 선택합니다.

4. **진단 켜기** 를 선택합니다.

5. 원하는 로그가 수집될 수 있도록 합니다.

    | 로그 이름 | 설명 |
    |-------|------------|
    | DeviceOperations | 디바이스 연결 이벤트 관련 로그 |
    | ServiceOperations | 서비스 SDK 사용 관련 이벤트 로그(예: 등록 그룹 만들기 또는 업데이트)|

6. **Log Analytics에 보내기**([가격 책정 참고](https://azure.microsoft.com/pricing/details/log-analytics/))를 켭니다. 

7. Device Provisioning Service 리소스의 Azure Portal에 있는 **로그** 탭으로 이동합니다.

8. 최근 이벤트를 보려면 **실행** 을 클릭합니다.

9. 결과가 있는 경우 오류에 대한 자세한 정보를 얻으려면 `OperationName`, `ResultType`, `ResultSignature`, `ResultDescription`(오류 메시지)을 검색합니다.


## <a name="common-error-codes"></a>일반적인 오류 코드
이 표를 사용하여 일반적인 오류를 이해하고 해결합니다.

| 오류 코드| Description | HTTP 상태 코드 |
|-------|------------|------------|
| 400 | 요청 본문이 잘못되었습니다. 예를 들어 본문을 구문 분석할 수 없거나 개체의 유효성을 검사할 수 없습니다.| 400 잘못된 형식 |
| 401 | 권한 부여 토큰의 유효성을 검사할 수 없습니다. 예를 들어 토큰이 만료되었거나 요청의 URI에 적용되지 않습니다. 이 오류 코드는 TPM 증명 흐름의 일부로 디바이스에도 반환됩니다. | 401 권한 없음|
| 404 | Device Provisioning Service 인스턴스 또는 등록과 같은 리소스가 없습니다. |404 찾을 수 없음 |
| 412 | 요청의 ETag가 RFC7232에 따라 기존 리소스의 ETag와 일치하지 않습니다. | 412 사전 조건 실패 |
| 429 | 서비스에 의해 작업이 제한되고 있습니다. 특정 서비스 제한은 [IoT Hub Device Provisioning Service 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits)을 참조하세요. | 429 요청이 너무 많음 |
| 500 | 내부 오류가 발생했습니다. | 500 내부 서버 오류|