---
title: Azure IoT Hub DPS와의 연결을 진단 하 고 문제를 해결 합니다.
description: Azure IoT Hub 장치 프로 비전 서비스 (DPS)의 장치 연결을 사용 하 여 일반적인 오류를 진단 하 고 해결 하는 방법을 알아봅니다.
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646475"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 문제 해결

IoT 장치에 대 한 연결 문제는 증명 실패, 등록 오류 등의 여러 가지 가능한 오류 지점이 있으므로 문제를 해결 하기 어려울 수 있습니다. 이 문서에서는 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)를 통해 장치 연결 문제를 검색 하 고 해결 하는 방법에 대 한 지침을 제공 합니다.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure Monitor를 사용 하 여 메트릭 보기 및 경고 설정

다음 절차에서는 IoT Hub Device Provisioning Service 메트릭에 대 한 경고를 보고 설정 하는 방법을 설명 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub Device Provisioning Service로 이동 합니다.

3. **메트릭**을 선택합니다.

4. 원하는 메트릭을 선택 합니다. 
   <br />현재 DPS에는 세 가지 메트릭이 있습니다.

    | 메트릭 이름 | Description |
    |-------|------------|
    | 증명 시도 | 장치 프로 비전 서비스를 사용 하 여 인증을 시도한 장치 수|
    | 등록 시도 | 인증에 성공한 후 IoT Hub에 등록 하려고 시도한 장치 수|
    | 할당 된 장치 | IoT Hub에 성공적으로 할당 된 장치 수|

5. 메트릭의 시각적 보기를 만들려면 원하는 집계 방법을 선택 합니다. 

6. 메트릭에 대 한 경고를 설정 하려면 메트릭 블레이드의 오른쪽 위에서 **새 경고 규칙** 을 선택 합니다. 마찬가지로 **경고** 블레이드로 이동 하 여 **새 경고 규칙**을 선택할 수 있습니다.

7. **조건 추가**를 선택한 다음 프롬프트에 따라 원하는 메트릭 및 임계값을 선택 합니다.

자세한 내용은 [Microsoft Azure의 클래식 경고 란?](../azure-monitor/platform/alerts-overview.md) 을 참조 하세요.

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>로그 분석을 사용 하 여 오류 확인 및 해결

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. IoT Hub로 이동합니다.

3. **진단 설정**을 선택합니다.

4. **진단 켜기**를 선택합니다.

5. 원하는 로그를 수집 하도록 설정 합니다.

    | 로그 이름 | 설명 |
    |-------|------------|
    | DeviceOperations | 장치 연결 이벤트와 관련 된 로그 |
    | ServiceOperations | 서비스 SDK 사용과 관련 된 이벤트 로그 (예: 등록 그룹 만들기 또는 업데이트)|

6. **Log Analytics 보내기를** 켭니다 ([가격 책정 참조](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. 장치 프로 비전 서비스 리소스의 Azure Portal에 있는 **로그** 탭으로 이동 합니다.

8. 최근 이벤트를 보려면 **실행** 을 클릭 합니다.

9. 결과가 있는 경우 `OperationName` ,, `ResultType` `ResultSignature` 및 `ResultDescription` (오류 메시지)를 검색 하 여 오류에 대 한 자세한 정보를 얻습니다.


## <a name="common-error-codes"></a>일반적인 오류 코드
이 표를 사용하여 일반적인 오류를 이해하고 해결합니다.

| 오류 코드| Description | HTTP 상태 코드 |
|-------|------------|------------|
| 400 | 요청 본문이 잘못 되었습니다. 예를 들어이를 구문 분석할 수 없거나 개체의 유효성을 검사할 수 없습니다.| 400 잘못 된 형식 |
| 401 | 권한 부여 토큰의 유효성을 검사할 수 없습니다. 예를 들어 만료 되었거나 요청의 URI에는 적용 되지 않습니다. 이 오류 코드는 TPM 증명 흐름의 일부로 장치에도 반환 됩니다. | 401 권한 없음|
| 404 | 장치 프로 비전 서비스 인스턴스 또는 리소스 (예: 등록)가 없습니다. |404 찾을 수 없음 |
| 412 | 요청의 ETag가 기존 리소스의 ETag와 일치 하지 않습니다 (RFC7232). | 412 전제 조건 실패 |
| 429 | 서비스에서 작업을 제한 하 고 있습니다. 특정 서비스 제한에 대해서는 [IoT Hub Device Provisioning Service 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)을 참조 하세요. | 429 요청 수가 너무 많음 |
| 500 | 내부 오류가 발생했습니다. | 500 내부 서버 오류|
