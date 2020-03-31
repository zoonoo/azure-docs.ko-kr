---
title: Azure IoT에 대한 보안 권장 사항 | 마이크로 소프트 문서
description: 이 문서에서는 Azure IoT Hub 솔루션의 보안을 보장하기 위한 추가 단계를 요약합니다.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048467"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 사물 인터넷(IoT) 배포에 대한 보안 권장 사항

이 문서에는 IoT에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공동 책임 모델에 설명된 대로 보안 의무를 이행하는 데 도움이 됩니다. Microsoft가 서비스 공급자의 책임을 수행하기 위해 수행하는 일에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임을](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)참조하십시오.

이 문서에 포함된 권장 사항 중 일부는 Azure 보안 센터에서 자동으로 모니터링할 수 있습니다. Azure 보안 센터는 Azure에서 리소스를 보호하는 첫 번째 방어선입니다. Azure 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약점을 식별합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다.

- Azure 보안 센터 권장 사항에 대한 자세한 내용은 [Azure 보안 센터의 보안 권장 사항을](../security-center/security-center-recommendations.md)참조하십시오.
- Azure 보안 센터에 대한 자세한 [내용은 Azure 보안 센터란 무엇입니까?](../security-center/security-center-intro.md)

## <a name="general"></a>일반

| 권장 | 주석 | ASC지원 |
|-|----|--|
| 최신 정보 유지 | 지원되는 플랫폼, 프로그래밍 언어, 프로토콜 및 프레임워크의 최신 버전을 사용합니다. | - |
| 인증 키를 안전하게 유지 | 배포 후 장치 ID와 인증 키를 물리적으로 안전하게 유지합니다. 이렇게 하면 등록된 장치로 가장하는 악의적인 장치를 방지할 수 있습니다. | - |
| 가능하면 장치 SDK 사용 | 장치 SDK는 강력하고 안전한 장치 응용 프로그램을 개발하는 데 도움이 되도록 암호화, 인증 등과 같은 다양한 보안 기능을 구현합니다. 자세한 내용은 [Azure IoT Hub SDK 이해 및 사용을](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 참조하십시오. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 | 주석 | ASC지원 |
|-|----|--|
| 허브에 대한 액세스 제어 정의 | 기능에 따라 각 구성 요소가 IoT Hub 솔루션에 액세스할 수 있는 [액세스 유형을 이해하고 정의합니다.](iot-security-deployment.md#securing-the-cloud) 허용된 권한은 *레지스트리 읽기,* *레지스트리ReadWrite,* *ServiceConnect*및 *장치 연결*입니다. [IoT 허브의](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) 기본 공유 액세스 정책은 역할에 따라 각 구성 요소에 대한 사용 권한을 정의하는 데도 도움이 될 수 있습니다. | - |
| 백 엔드 서비스에 대한 액세스 제어 정의 | IoT Hub 솔루션에서 수집한 데이터는 [Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [스트림 분석,](https://docs.microsoft.com/azure/stream-analytics/)앱 [서비스,](https://docs.microsoft.com/azure/app-service/) [논리 앱](https://docs.microsoft.com/azure/logic-apps/)및 [Blob 저장소와](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)같은 다른 Azure 서비스에서 사용할 수 있습니다. 이러한 서비스에 대해 설명된 대로 적절한 액세스 권한을 이해하고 허용해야 합니다. | - |

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 | ASC지원 |
|-|----|--|
| 안전한 장치 인증 | [고유한 ID 키 또는 보안 토큰](iot-security-deployment.md#iot-hub-security-tokens)또는 각 장치에 대한 장치 내 [X.509 인증서를](iot-security-deployment.md#x509-certificate-based-device-authentication) 사용하여 장치와 IoT 허브 간의 보안 통신을 보장합니다. 적절한 방법을 사용하여 [선택한 프로토콜(MQTT, AMQP 또는 HTTPS)을 기반으로 보안 토큰을 사용합니다.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| 안전한 장치 통신 | IoT Hub는 TLS(전송 계층 보안) 표준을 사용하여 장치에 대한 연결을 보호하며 버전 1.2 및 1.0을 지원합니다. [TLS 1.2를](https://tools.ietf.org/html/rfc5246) 사용하여 보안을 극대화하십시오. | - |
| 서비스 통신 보안 유지 | IoT Hub는 TLS 프로토콜만 사용하여 [Azure 저장소](/azure/storage/) 또는 [이벤트 허브와](/azure/event-hubs) 같은 백 엔드 서비스에 연결하는 끝점을 제공하며 암호화되지 않은 채널에는 엔드포인트가 노출되지 않습니다. 이 데이터가 저장 또는 분석을 위해 이러한 백 엔드 서비스에 도달하면 해당 서비스에 적합한 보안 및 암호화 방법을 활용하고 백 엔드에서 중요한 정보를 보호해야 합니다. | - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | ASC지원 |
|-|----|--|
| 기기에 대한 액세스 보호 | 원치 않는 액세스를 방지하기 위해 장치에 하드웨어 포트를 최소한으로 유지하십시오. 또한 장치의 물리적 변조를 방지하거나 감지하는 메커니즘을 빌드합니다. 자세한 내용은 [IoT 보안 모범 사례를](iot-security-best-practices.md) 참조하십시오. | - |
| 보안 하드웨어 구축 | 암호화된 저장소 또는 TPM(신뢰할 수 있는 플랫폼 모듈)과 같은 보안 기능을 통합하여 장치와 인프라를 보다 안전하게 유지합니다. 장치 운영 체제와 드라이버를 최신 버전으로 업그레이드하고 공간이 허용되는 경우 바이러스 백신 및 맬웨어 방지 기능을 설치합니다. [IoT 보안 아키텍처를](iot-security-architecture.md) 읽고 이를 통해 여러 보안 위협을 완화하는 데 도움이 되는 방법을 이해합니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | ASC지원 |
|-|----|--|
| 장치에 대한 무단 액세스 모니터링 |  장치 운영 체제의 로깅 기능을 사용하여 장치 또는 해당 포트의 보안 위반 또는 물리적 변조를 모니터링합니다. | - |
| 클라우드에서 IoT 솔루션 모니터링 | [Azure 모니터의 메트릭을](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)사용하여 IoT Hub 솔루션의 전반적인 상태를 모니터링합니다. | - |
| 진단 설정 | 솔루션에서 이벤트를 로깅한 다음 진단 로그를 Azure Monitor로 전송하여 성능을 가시성을 얻습니다. 자세한 내용은 [IoT 허브에서 모니터링및 문제를 진단합니다.](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) | - |

## <a name="next-steps"></a>다음 단계

Azure IoT와 관련된 고급 시나리오의 경우 추가 보안 요구 사항을 고려해야 할 수 있습니다. 자세한 지침은 [IoT 보안 아키텍처를](iot-security-architecture.md) 참조하십시오.

