---
title: Azure IoT에 대 한 보안 권장 사항 | Microsoft Docs
description: 이 문서에서는 Azure IoT Hub 솔루션의 보안을 보장 하기 위한 추가 단계를 요약 합니다.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722863"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 사물 인터넷 (IoT) 배포에 대 한 보안 권장 사항

이 문서에서는 Azure IoT 서비스에 대 한 보안 권장 사항을 포함합니다. 이러한 권장 사항을 구현 하 고 Azure IoT의 고객에 게 귀하의 보안 의무를 충족 하는 도움말 보안을 향상 전체 IoT 솔루션에 대 한 합니다. Azure IoT에서 제공 하는 기본 보안 기능에 대 한 자세한 내용은 읽을 [처음부터 IoT 보안](iot-security-ground-up.md)합니다.

## <a name="general"></a>일반

| 권장 | 주석 |
|-|-|
| 최신 상태로 유지 | 지원 되는 플랫폼 프로그래밍 언어, 프로토콜 및 프레임 워크의 최신 버전을 사용 합니다. |
| 인증 키를 안전 하 게 유지 | 장치 Id 및 인증 키 배포 후 물리적으로 안전 하 게 유지 합니다. 등록 된 장치로 악의적인 장치 모조를 피해 야 합니다. |
| 가능한 경우 장치 Sdk를 사용 합니다. | 장치 Sdk는 다양 한 암호화, 인증 및 강력 하 고 안전한 장치 응용 프로그램 개발에 도움을 주는 등과 같은 보안 기능을 구현 합니다. 참조 [이해 및 사용 하 여 Azure IoT Hub Sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 자세한 내용은 합니다. |


## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 |
|-|-|
| 허브에 대 한 액세스 제어를 정의 합니다. | [이해 하 고 액세스 유형을 정의](iot-security-deployment.md#securing-the-cloud) 각 구성 요소는 IoT Hub 솔루션에는 기능을 기반으로 합니다. 허용 권한은 *레지스트리를 읽는*를 *RegistryReadWrite*를 *ServiceConnect*, 및 *DeviceConnect*합니다. 기본 [공유 액세스 정책에서 IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) 해당 역할에 따라 각 구성 요소에 대 한 사용 권한을 정의할 수 있습니다. |
| 백 엔드 서비스에 대 한 액세스 제어를 정의 합니다. | 와 같은 다른 Azure 서비스에서 사용할 수 IoT Hub 솔루션에서 수집 된 데이터 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)를 [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)하십시오 [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/), 및 [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)합니다. 이해 하 고 이러한 서비스에 대 한 설명 된 대로 적절 한 액세스 권한을 허용 해야 합니다. |


## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 |
|-|-|
| 보안 장치 인증 | 중 하나를 사용 하 여 장치와 IoT hub 간의 보안 통신을 유지 [고유한 id 키 또는 보안 토큰](iot-security-deployment.md#iot-hub-security-tokens), 또는 [장치에 X.509 인증서](iot-security-deployment.md#x509-certificate-based-device-authentication) 각 장치에 대 한 합니다. 적절 한 메서드를 사용 하 여 [선택한 프로토콜 (MQTT, AMQP 또는 HTTPS)을 기반으로 하는 보안 토큰을 사용 하 여](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)입니다. |
| 장치 통신을 보호 | IoT Hub 버전 1.2, 1.2 및 1.0 지원 전송 계층 보안 (TLS) 표준을 사용 하 여 장치에 대 한 연결을 보호 합니다. 사용 하 여 [TLS 1.2](https://tools.ietf.org/html/rfc5246) 최대 보안 되도록 합니다. |
| 서비스 통신 보안 유지 | IoT Hub와 같은 백 엔드 서비스에 연결 하는 끝점을 제공 [Azure Storage](/azure/storage/) 하거나 [Event Hubs](/azure/event-hubs) 는 암호화 되지 않은 채널에 노출 되는 TLS 프로토콜을 고 어떤 끝점을 사용 하 여 합니다. 이 데이터 저장소 또는 분석을 위한 이러한 백 엔드 서비스에 도달에 해당 서비스에 대 한 적절 한 보안 및 암호화 방법을 사용 하 여 백 엔드의 중요 한 정보를 보호 해야 합니다. |


## <a name="networking"></a>네트워킹

| 권장 | 주석 |
|-|-|
| 장치에 대 한 액세스 보호 | 포트를 유지 하드웨어 장치에서 원치 않는 액세스를 방지 하려면 최소한 합니다. 또한 방지 하거나 장치의 물리적 변조를 감지 하는 메커니즘을 빌드하십시오. 읽기 [IoT 보안 모범 사례](iot-security-best-practices.md) 세부 정보에 대 한 합니다. |
| 보안 하드웨어를 빌드 | 암호화 된 저장소 또는 모듈 TPM (Trusted Platform)를 보호 하는 장치 및 인프라와 같은 보안 기능을 통합 합니다. 장치 운영 체제 및 최신 버전으로 업그레이드 하는 드라이버를 유지 하 고 공간이 허용 하는 경우 바이러스 백신 및 맬웨어 방지 기능을 설치 합니다. 읽기 [IoT 보안 아키텍처](iot-security-architecture.md) 이해이 하는 방법에 몇 가지 보안 위협은 완화 시킬 수 있습니다. |


## <a name="monitoring"></a>모니터링

| 권장 | 주석 |
|-|-|
| 장치에 대 한 무단된 액세스를 모니터링 합니다. |  보안 위반 또는 장치에서 해당 포트의 물리적 변조 모니터링할 장치 운영 체제의 로깅 기능을 사용 합니다. |
| 클라우드에서 IoT 솔루션 모니터링 | 사용 하 여 IoT Hub 솔루션의 전반적인 상태를 모니터링 합니다 [Azure Monitor의 메트릭](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)합니다. |
| 진단 설정 | 밀접 하 게 솔루션의 이벤트를 기록 하 고 진단 로그를 Azure Monitor의 성능을 파악로 보내 작업을 시청 하세요. 읽기 [모니터 IoT hub의 문제를 진단 하 고](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) 자세한 내용은 합니다. |

## <a name="next-steps"></a>다음 단계

Azure IoT 관련 된 고급 시나리오에 대 한 추가 보안 요구 사항을 검토 해야 합니다. 참조 [IoT 보안 아키텍처](iot-security-architecture.md) 자세한 지침에 대 한 합니다.

