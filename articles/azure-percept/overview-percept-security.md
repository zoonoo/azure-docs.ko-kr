---
title: Azure Percept 보안 개요
description: Azure Percept 보안에 대 한 자세한 정보
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567647"
---
# <a name="azure-percept-security-overview"></a>Azure Percept 보안 개요

Azure Percept 장치는 신뢰의 하드웨어 루트를 사용 하 여 설계 되었습니다. 이러한 기본 제공 보안을 통해 카메라 및 마이크와 같은 유추 데이터 및 개인 정보 보호 센서를 보호 하 고, Azure Percept Studio 서비스에 대 한 장치 인증 및 권한 부여를 사용할 수 있습니다.

> [!NOTE]
> Azure Percept 진한은 개발 및 테스트 환경 에서만 사용이 허가 됩니다.

## <a name="devices"></a>디바이스

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept에는 장치를 Azure 장치 프로 비전 서비스 (DPS)에 추가 보안으로 연결 하는 데 활용할 수 있는 신뢰할 수 있는 플랫폼 모듈 (TPM) 버전 2.0이 포함 되어 있습니다. TPM은 TCG(신뢰할 수 있는 컴퓨팅 그룹)의 업계 전체 ISO 표준입니다. 전체 TPM 2.0 사양 또는 ISO/IEC 11889 사양에 대 한 자세한 내용은 [TCG(신뢰할 수 있는 컴퓨팅 그룹) 웹 사이트](https://trustedcomputinggroup.org/resource/tpm-library-specification/) 를 확인 하세요. DPS에서 안전 하 게 장치를 프로 비전 하는 방법에 대 한 자세한 내용은 [장치 프로 비전 서비스-TPM 증명 Azure IoT Hub](../iot-dps/concepts-tpm-attestation.md)를 참조 하세요.

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept (SoMs)

Azure Percept 비전 SoM (시스템-모듈) 및 Azure Percept Audio SoM은 모두 포함 된 AI 센서에 대 한 액세스를 보호 하기 위한 MCU (마이크로컨트롤러 유닛)를 포함 합니다. 모든 부팅에서 MCU 펌웨어는 주사위 (장치 식별자 컴퍼지션 엔진) 아키텍처를 사용 하 여 Azure Percept Studio 서비스에서 AI 가속기를 인증 하 고 권한을 부여 합니다. 분석은 계층으로 부팅 하 고 각 계층 및 구성에 대해 UD (고유 장치 암호)를 만드는 방식으로 작동 합니다. 체인의 특정 지점에서 다른 코드 또는 구성이 부팅 되는 경우 비밀이 달라 집니다. 주사위 [작업 그룹 사양](https://trustedcomputinggroup.org/work-groups/dice-architectures/)에서 분석에 대해 자세히 알아볼 수 있습니다. Azure Percept Studio 및 필수 서비스에 대 한 액세스를 구성 하려면 [Azure PERCEPT 진한 방화벽 구성](concept-security-configuration.md)문서를 참조 하세요.

Azure Percept 장치는 신뢰의 하드웨어 루트를 사용 하 여 펌웨어를 보호 합니다. 부팅 ROM은 ROM 및 운영 체제 (OS) 로더 간에 펌웨어 무결성을 보장 합니다. 그러면이 로더는 다른 소프트웨어 구성 요소의 무결성을 보장 하 여 신뢰 체인을 만듭니다.

## <a name="services"></a>서비스

### <a name="iot-edge"></a>IoT Edge

Azure Percept는 TLS (전송 계층 보안) 프로토콜을 활용 하는 추가 보안 및 기타 Azure 서비스를 사용 하 여 Azure Percept Studio에 연결 합니다. Azure Percept 진한은 Azure IoT Edge 사용할 수 있는 장치입니다. IoT Edge runtime은 장치를 IoT Edge 장치로 전환 하는 프로그램 컬렉션입니다. IoT Edge 런타임 구성 요소를 통해 IoT Edge 장치가 Edge에서 실행 되는 코드를 수신 하 고 결과를 전달할 수 있습니다. Azure Percept는 호스트 운영 체제 및에 지 사용 응용 프로그램에서 IoT Edge 작업을 격리 하기 위해 Docker 컨테이너를 활용 합니다. Azure IoT Edge 보안 프레임 워크에 대 한 자세한 내용은 [IoT Edge security manager](../iot-edge/iot-edge-security-manager.md)를 참조 하세요.

### <a name="device-update-for-iot-hub"></a>IoT Hub용 디바이스 업데이트 문서

IoT Hub에 대 한 장치 업데이트를 통해 Azure Percept 장치에 갱신 가능한 보안을 제공 하는 더 안전 하 고 확장 가능 하며 안정적인 공중파 업데이트를 사용할 수 있습니다. 풍부한 관리 제어 기능을 제공 하 고 정보를 통해 규정 준수를 업데이트 합니다. Azure Percept 진한에는 펌웨어에서 OS 계층으로의 복원 력 업데이트 (A/B)를 제공 하는 사전 통합 된 장치 업데이트 솔루션이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방화벽 구성 및 보안 권장 사항에 대 한 자세한 정보](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Microsoft online store에서 Azure Percept 진한 구매](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
