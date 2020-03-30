---
title: Azure IoT Hub DPS로 연결 해제 진단 및 해결
description: Azure IoT Hub 장치 프로비저닝 서비스(DPS)에 대한 장치 연결을 통해 일반적인 오류를 진단하고 해결하는 방법을 알아봅니다.
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646475"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Azure IoT 허브 장치 프로비저닝 서비스 문제 해결

증명 오류, 등록 실패 등과 같은 많은 가능한 오류 지점이 있기 때문에 IoT 장치의 연결 문제는 문제 해결이 어려울 수 있습니다. 이 문서에서는 [Azure Monitor를](https://docs.microsoft.com/azure/azure-monitor/overview)통해 장치 연결 문제를 감지하고 해결하는 방법에 대한 지침을 제공합니다.

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Azure 모니터를 사용하여 메트릭을 보고 경고를 설정합니다.

다음 절차에서 IoT Hub 장치 프로비저닝 서비스 메트릭에서 경고를 보고 설정하는 방법을 설명합니다. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. IoT 허브 장치 프로비저닝 서비스로 이동합니다.

3. **메트릭을 선택합니다.**

4. 원하는 메트릭을 선택합니다. 
   <br />현재 DPS에는 세 가지 메트릭이 있습니다.

    | 메트릭 이름 | 설명 |
    |-------|------------|
    | 증명 시도 | 장치 프로비저닝 서비스로 인증하려고 시도한 장치 수|
    | 등록 시도 | 인증성공 후 IoT Hub에 등록하려고 시도한 장치 수|
    | 할당된 장치 | IoT Hub에 성공적으로 할당된 장치 수|

5. 원하는 집계 방법을 선택하여 메트릭의 시각적 보기를 만듭니다. 

6. 메트릭 경고를 설정하려면 메트릭 블레이드의 오른쪽 상단에서 **새 경고 규칙을** 선택하고 마찬가지로 **경고** 블레이드로 이동하여 새 경고 **규칙을**선택할 수 있습니다.

7. **조건 추가를**선택한 다음 프롬프트에 따라 원하는 메트릭 및 임계값을 선택합니다.

자세한 내용은 [Microsoft Azure의 클래식 경고란 무엇입니까?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>로그 분석사용으로 오류를 보고 해결

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. IoT Hub로 이동합니다.

3. **진단 설정**을 선택합니다.

4. **진단 켜기**를 선택합니다.

5. 원하는 로그를 수집할 수 있습니다.

    | 로그 이름 | 설명 |
    |-------|------------|
    | DeviceOperations | 장치 연결 이벤트와 관련된 로그 |
    | ServiceOperations | 서비스 SDK 사용과 관련된 이벤트 로그(예: 등록 그룹 생성 또는 업데이트)|

6. **로그 분석으로 보내기** 를 켜십시오(가격[표시).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. 장치 프로비저닝 서비스 리소스 아래의 Azure 포털에서 **로그** 탭으로 이동합니다.

8. 최근 이벤트를 보려면 **실행을 클릭합니다.**

9. 결과가 있는 경우 오류에 `OperationName`대한 자세한 내용을 보려면 에서 를 `ResultType` `ResultSignature`찾습니다. `ResultDescription`


## <a name="common-error-codes"></a>일반적인 오류 코드
이 표를 사용하여 일반적인 오류를 이해하고 해결합니다.

| 오류 코드| 설명 | HTTP 상태 코드 |
|-------|------------|------------|
| 400 | 요청 본문이 잘못되었습니다. 예를 들어 구문 분석할 수 없거나 개체의 유효성을 검사할 수 없습니다.| 400 불량 형식 |
| 401 | 권한 부여 토큰의 유효성을 검사할 수 없습니다. 예를 들어 만료되었거나 요청의 URI에 적용되지 않습니다. 이 오류 코드는 TPM 증명 흐름의 일부로 장치에도 반환됩니다. | 401 권한 없음|
| 404 | 장치 프로비저닝 서비스 인스턴스 또는 리소스(예: 등록)가 존재하지 않습니다. |404 찾을 수 없음 |
| 412 | RFC7232에 따라 요청의 ETag가 기존 리소스의 ETag와 일치하지 않습니다. | 412 사전 조건 실패 |
| 429 | 서비스가 작업을 제한하고 있습니다. 특정 서비스 제한은 [IoT Hub 장치 프로비저닝 서비스 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)참조하십시오. | 429 요청이 너무 많습니다. |
| 500 | 내부 오류가 발생했습니다. | 500 내부 서버 오류|
