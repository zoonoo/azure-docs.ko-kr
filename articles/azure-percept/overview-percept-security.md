---
title: Azure Percept 보안 개요
description: Azure Percept 보안에 대 한 자세한 정보
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 9575c0aec4a1b45f9099bff1dc4209c63529ddf6
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025546"
---
# <a name="azure-percept-security-overview"></a>Azure Percept 보안 개요

Azure Percept 진한 장치는 신뢰의 하드웨어 루트: 모든 장치에 대 한 추가 기본 제공 보안을 사용 하 여 설계 되었습니다. 카메라, 마이크, 유추 데이터 등의 개인 정보 보호 센서를 보호 하 고, Azure Percept Studio 서비스에 대 한 장치 인증 및 권한 부여를 사용 하도록 설정 합니다.

> [!NOTE]
> Azure Percept 진한은 개발 및 테스트 환경 에서만 사용이 허가 됩니다.

## <a name="devices"></a>디바이스

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept에는 추가 보안을 사용 하 여 장치를 Azure 장치 프로 비전 서비스에 연결 하는 데 활용할 수 있는 TPM (신뢰할 수 있는 플랫폼 모듈) 버전 2.0이 포함 되어 있습니다. TPM은 TCG(신뢰할 수 있는 컴퓨팅 그룹)의 산업 전반에 걸친 ISO 표준 이며 [전체 tpm 2.0 사양](https://trustedcomputinggroup.org/resource/tpm-library-specification/) 또는 ISO/IEC 11889 사양에서 tpm에 대 한 자세한 내용을 확인할 수 있습니다. DPS에서 안전 하 게 장치를 프로 비전 하는 방법에 대 한 자세한 내용은 [장치 프로 비전 서비스-TPM 증명 Azure IoT Hub](../iot-dps/concepts-tpm-attestation.md)를 참조 하세요.

### <a name="azure-percept-system-on-module-som"></a>Azure SOM (Percept system on module)

Azure Percept 진한 비전 사용 가능 모듈 (SOM) 및 Azure Percept Audio 액세서리 SOM에는 포함 된 AI 센서에 대 한 액세스를 보호 하기 위한 MCU (마이크로 컨트롤러 단위)가 포함 되어 있습니다. 모든 부팅에서 MCU 펌웨어는 주사위 (장치 식별자 컴퍼지션 엔진) 아키텍처를 사용 하 여 Azure Percept Studio 서비스에서 AI 가속기를 인증 하 고 권한을 부여 합니다. UD (고유 장치 비밀)를 기반으로 하 여 계층으로 부팅을 분할 하 고 각 계층 및 구성에 고유한 비밀을 만드는 방식으로 분석을 수행 합니다. 서로 다른 코드 또는 구성이 부팅 되 면 체인의 특정 지점에서 비밀이 달라 집니다. 주사위 [작업 그룹 사양](https://trustedcomputinggroup.org/work-groups/dice-architectures/)에서 분석에 대해 자세히 알아볼 수 있습니다. Azure Percept Studio 및 필수 서비스에 대 한 액세스를 구성 하려면 아래 **Azure Percept에 대 한 방화벽 구성** 을 참조 하세요.

Azure Percept 장치는 하드웨어 루트 트러스트를 사용 하 여 펌웨어를 보호 합니다. 부팅 ROM은 ROM과 OS (운영 체제) 로더 간의 펌웨어 무결성을 보장 하며,이는 다른 소프트웨어 구성 요소의 무결성을 보장 하 여 신뢰 체인을 만듭니다.

## <a name="services"></a>서비스

### <a name="iot-edge"></a>IoT Edge

Azure Percept는 TLS (전송 계층 보안) 프로토콜을 활용 하는 추가 보안 및 기타 Azure 서비스를 사용 하 여 Azure Percept Studio에 연결 합니다. Azure Percept 진한 Azure IoT Edge 활성화 된 장치입니다. IoT Edge runtime은 장치를 IoT Edge 장치로 전환 하는 프로그램 컬렉션입니다. IoT Edge 런타임 구성 요소를 통해 IoT Edge 장치가 Edge에서 실행 되는 코드를 수신 하 고 결과를 전달할 수 있습니다. Azure Percept는 호스트 운영 체제 및 Edge 사용 응용 프로그램에서 IoT Edge 작업을 격리 하기 위해 Docker 컨테이너를 활용 합니다. Azure IoT Edge 보안 프레임 워크에 대 한 자세한 내용은 [IoT Edge security manager](../iot-edge/iot-edge-security-manager.md)를 참조 하세요.

### <a name="device-update-for-iot-hub"></a>IoT Hub용 디바이스 업데이트 문서

IoT Hub에 대 한 장치 업데이트를 통해 Azure Percept 장치에 갱신 가능한 보안을 제공 하는 더 안전 하 고 확장 가능 하며 안정적인 공중파 업데이트를 사용할 수 있습니다. 풍부한 관리 제어 기능을 제공 하 고 정보를 통해 규정 준수를 업데이트 합니다. Azure Percept 진한에는 펌웨어에서 OS 계층으로의 복원 력 업데이트 (A/B)를 제공 하는 사전 통합 된 장치 업데이트 솔루션이 포함 되어 있습니다.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Azure Percept에 대 한 방화벽 구성

네트워킹 설정에서 Azure Percept 진한 장치에서 연결을 명시적으로 허용 해야 하는 경우 다음 구성 요소 목록을 검토 하세요.

다음 검사 목록은 방화벽 규칙의 시작점입니다.

|URL (* = 와일드 카드) |아웃바운드 TCP 포트|    사용량|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Azure 진한 SOM 인증 및 권한 부여|
|*. auth.projectsantacruz.azure.net| 443|    Azure 진한 SOM 인증 및 권한 부여|

또한 [Azure IoT Edge에서 사용 하는 연결](../iot-edge/production-checklist.md#allow-connections-from-iot-edge-devices)의 목록을 검토 합니다.

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>다음 단계

사용 가능한 [Azure PERCEPT AI 모델](./overview-ai-models.md)에 대해 알아봅니다.