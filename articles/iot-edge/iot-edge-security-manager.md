---
title: Azure IoT 에지 보안 관리자 - Azure IoT 에지
description: IoT Edge 디바이스 보안 태세와 보안 서비스의 무결성을 관리합니다.
services: iot-edge
keywords: 보안, 보안 요소, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: d5cfa16196a8815b711fd5277a80f6eb67d3a388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548699"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 보안 관리자

Azure IoT Edge 보안 관리자는 보안 실리콘 하드웨어를 추상화하여 IoT Edge 디바이스 및 이 디바이스의 모든 구성 요소를 보호하기 위해 잘 정립된 보안 코어입니다. 보안 관리자는 보안 강화의 핵심이며 OEM(원래 장비 제조업체)에 기술 통합 지점을 제공합니다.

![Azure IoT Edge 보안 관리자](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 보안 관리자의 목표는 IoT Edge 디바이스 및 모든 고유 소프트웨어 작업의 무결성을 보호하는 것입니다. 보안 관리자는 신뢰를 신뢰 하드웨어의 기본 하드웨어 루트(사용 가능한 경우)에서 전환하여 IoT Edge 런타임을 부트스트랩하고 진행 중인 작업을 모니터링합니다.  IoT Edge 보안 관리자는 보안 실리콘 하드웨어(사용 가능한 경우)와 연동되어 가능한 최고 수준의 보안을 보장하는 소프트웨어입니다.  

IoT Edge 보안 관리자의 책임에는 다음을 비롯한 여러 사항이 포함됩니다.

* Azure IoT Edge 디바이스의 측정된 보안 부트스트래핑.
* 해당되는 경우 디바이스 ID 프로비전 및 트러스트 전환.
* Device Provisioning Service와 같은 클라우드 서비스의 디바이스 구성 요소 호스트 및 보호.
* 고유한 ID를 지정하여 안전하게 IoT Edge 모듈 프로비전
* 공증 서비스를 통한 신뢰할 수 있는 디바이스 하드웨어 루트에 대한 게이트키퍼.
* 런타임 시 IoT Edge 작업의 무결성 모니터링.

IoT Edge 보안 관리자는 세 가지 구성 요소를 포함합니다.

* IoT Edge 보안 디먼.
* 하드웨어 보안 모듈 플랫폼 추상화 계층(HSM PAL).
* 선택 사항이지만 권장되는 신뢰할 수 있는 하드웨어 실리콘 루트 또는 HSM.

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼

IoT Edge 보안 데몬은 IoT Edge 보안 관리자의 논리적 운영을 담당합니다. IoT Edge 장치의 신뢰할 수 있는 컴퓨팅 기반의 상당 부분을 나타냅니다.

### <a name="design-principles"></a>디자인 원칙

IoT Edge 보안 디먼은 작동 무결성 최대화 및 블로트/변동 최소화라는 두 가지 핵심 원칙을 따릅니다.

#### <a name="maximize-operational-integrity"></a>작동 무결성 극대화

IoT Edge 보안 데몬은 주어진 신뢰 하드웨어의 방어 기능 내에서 가능한 최고의 무결성으로 작동합니다. 적절한 통합을 통해 트러스트 하드웨어의 루트는 변조를 방지하기 위해 보안 디먼을 런타임 시 정적으로 측정하고 모니터링합니다.

IoT 디바이스에 물리적으로 액세스하는 행위는 항상 위협으로 간주됩니다. 따라서 IoT Edge 보안 디먼의 무결성을 방어하려면 신뢰할 수 있는 하드웨어 루트의 역할이 중요합니다.  신뢰의 하드웨어 루트는 두 가지 종류로 제공됩니다.

* 비밀 및 암호화 키와 같은 중요한 정보를 보호하기 위한 보안 요소
* 키와 같은 비밀의 보호를 위한 보안 enclave 및 계량 및 요금 청구와 같은 중요한 워크로드.

트러스트의 하드웨어 루트를 사용하는 두 가지 종류의 실행 환경이 있습니다.

* 중요한 정보를 보호하기 위해 보안 요소를 사용하는 표준 또는 풍부한 실행 환경(REE)입니다.
* 중요한 정보를 보호하고 소프트웨어 실행을 보호하기 위해 보안 영토 기술을 사용하는 신뢰할 수 있는 실행 환경(TEE)입니다.

보안 엔클레이브를 신뢰의 하드웨어 루트로 사용하는 장치의 경우 IoT Edge 보안 데몬 내의 중요한 논리가 영토 내에 있어야 합니다.  보안 데몬의 민감하지 않은 부분은 TEE 외부에 있을 수 있습니다.  어쨌든, 원래 설계 제조업체(ODM) 및 OEM(원래 장비 제조업체)은 부팅 및 런타임 시 IoT Edge 보안 데몬의 무결성을 측정하고 방어하기 위해 HSM으로부터 신뢰를 확장해야 합니다.

#### <a name="minimize-bloat-and-churn"></a>블로트 및 변동 최소화

IoT Edge 보안 디먼이 따르는 또 다른 핵심 원칙은 변동의 최소화합니다.  최고 수준의 신뢰를 보장하기 위해 IoT Edge 보안 디먼은 신뢰할 수 있는 디바이스 하드웨어 루트와 밀접하게 결합되어 네이티브 코드로 작동할 수 있습니다.  이러한 유형의 실현은 일부 시나리오에서 어려울 수 있는 OS 제공 업데이트 메커니즘과 는 달리 트러스트의 보안 업데이트 경로의 하드웨어 루트를 통해 데몬 소프트웨어를 업데이트하는 것이 일반적입니다.  IoT 장치에는 보안 갱신이 권장되지만 과도한 업데이트 요구 사항이나 대규모 업데이트 페이로드는 여러 가지 방법으로 위협 표면을 확장할 수 있습니다.  예를 들어 업데이트를 건너뛰고 운영 가용성을 최대화하거나 트러스트 하드웨어의 루트가 대규모 업데이트 페이로드를 처리하기에 제약이 많은 경우가 해당됩니다.  따라서 IoT Edge 보안 데몬의 설계는 설치 공간 및 신뢰할 수 있는 컴퓨팅 기반을 작게 유지하고 업데이트 요구 사항을 최소화하기 위해 간결합니다.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 보안 디먼의 아키텍처

![Azure IoT Edge 보안 디먼](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 보안 데몬은 보안 강화를 위해 신뢰할 수 있는 기술의 사용 가능한 하드웨어 루트를 활용합니다.  또한 하드웨어 기술이 신뢰할 수 있는 실행 환경을 제공하는 경우 표준/풍부한 실행 환경(REE)과 신뢰할 수 있는 실행 환경(TEE) 간에 분할-월드-월드(분할)를 수행할 수 있습니다. 역할별 인터페이스를 통해 IoT Edge의 주요 구성 요소를 통해 IoT Edge 장치 및 해당 운영의 무결성을 보장할 수 있습니다.

#### <a name="cloud-interface"></a>클라우드 인터페이스

클라우드 인터페이스를 통해 IoT Edge 보안 데몬은 보안 갱신과 같은 장치 보안에 대한 클라우드 칭찬과 같은 클라우드 서비스에 액세스할 수 있습니다.  예를 들어, IoT Edge 보안 데몬은 현재 이 인터페이스를 사용하여 장치 ID 수명 주기 관리를 위해 Azure IoT Hub [장치 프로비저닝 서비스에](https://docs.microsoft.com/azure/iot-dps/) 액세스합니다.  

#### <a name="management-api"></a>관리 API

IoT Edge 보안 데몬은 IoT Edge 모듈을 생성/시작/중지/제거할 때 IoT Edge 에이전트에서 호출하는 관리 API를 제공합니다. 보안 데몬은 모든 활성 모듈에 대해 "등록"을 저장합니다. 이러한 등록은 모듈의 ID를 모듈의 일부 속성과 매핑합니다. 예를 들어 이러한 모듈 속성에는 컨테이너에서 실행되는 프로세스의 프로세스 식별자(pid)와 docker 컨테이너 내용의 해시가 포함됩니다.

이러한 속성은 워크로드 API(아래에 설명)에서 호출자의 작업에 대한 권한이 있는지 확인하는 데 사용됩니다.

관리 API는 IoT Edge 에이전트에서만 호출할 수 있는 권한 있는 API입니다.  IoT Edge 보안 데몬 부트스트랩을 시작하고 IoT Edge 에이전트를 시작하기 때문에 IoT Edge 에이전트가 변조되지 않았음을 확인한 다음 IoT Edge 에이전트에 대한 암시적 등록을 만들 수 있습니다. 워크로드 API가 사용하는 동일한 증명 프로세스는 관리 API에 대한 액세스를 IoT Edge 에이전트로만 제한합니다.

#### <a name="container-api"></a>컨테이너 API

컨테이너 API는 Moby 또는 Docker와 같은 모듈 관리에 사용되는 컨테이너 시스템과 상호 작용합니다.

#### <a name="workload-api"></a>워크로드 API

워크로드 API는 모든 모듈에서 액세스할 수 있습니다. HSM이 서명된 토큰 또는 X509 인증서로 ID 증명을 제공하고 해당 트러스트번들을 모듈에 제공합니다. 트러스트 번들에는 모듈이 신뢰해야 하는 다른 모든 서버용 CA 인증서가 포함됩니다.

IoT Edge 보안 데몬은 증명 프로세스를 사용하여 이 API를 보호합니다. 모듈이 이 API를 호출하면 보안 데몬은 ID에 대한 등록을 찾으려고 시도합니다. 성공하면 등록의 속성을 사용하여 모듈을 측정합니다. 측정 프로세스의 결과가 등록과 일치하면 새 ID 증명이 생성됩니다. 그리고 해당 CA 인증서(트러스트 번들)가 모듈에 반환됩니다.  모듈은 이 인증서를 사용하여 IoT Hub나 다른 모듈에 연결하거나 서버를 시작합니다. 서명된 토큰 또는 인증서가 만료될 때 새 인증서를 요청하는 것은 모듈의 책임입니다.

### <a name="integration-and-maintenance"></a>통합 및 유지 관리

Microsoft는 [IoT Edge 보안 디먼용 기본 코드 베이스를 GitHub](https://github.com/Azure/iotedge/tree/master/edgelet)에 유지 관리합니다.

#### <a name="installation-and-updates"></a>설치 및 업데이트

IoT Edge 보안 디먼의 설치 및 업데이트는 운영 체제의 패키지 관리 시스템을 통해 관리됩니다. 신뢰할 수 있는 하드웨어 루트가 있는 IoT Edge 디바이스는 보안 부트 및 업데이트 관리 시스템을 통해 수명 주기를 관리하여 디먼의 무결성을 추가로 강화해야 합니다. 장치 제조업체는 해당 장치 기능에 따라 이러한 방법을 탐색해야 합니다.

#### <a name="versioning"></a>버전 관리

IoT Edge 런타임은 IoT Edge 보안 디먼의 버전을 추적 및 보고합니다. 버전은 IoT Edge 에이전트 모듈이 보고한 속성의 *runtime.platform.version* 특성으로 보고됩니다.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>하드웨어 보안 모듈 플랫폼 추상화 계층(HSM PAL)

HSM PAL은 트러스트 하드웨어의 모든 루트를 추상화하여 IoT Edge의 개발자나 사용자를 복잡한 문제로부터 떼어놓습니다.  여기에는 표준 실행 환경과 보안 영역 간의 통신과 같은 응용 프로그램 프로그래밍 인터페이스(API) 및 도메인 간 통신 절차의 조합이 포함됩니다.  HSM PAL의 실제 구현은 사용 중인 특정 보안 하드웨어에 따라 달라집니다. 보안 하드웨어가 있으면 사실상 모든 보안 실리콘 하드웨어를 사용할 수 있습니다.

## <a name="secure-silicon-root-of-trust-hardware"></a>트러스트 하드웨어의 보안 실리콘 루트

보안 실리콘은 IoT Edge 디바이스 하드웨어 내부에 신뢰를 확보하는 데 필요합니다.  보안 실리콘은 TPM(신뢰할 수 있는 플랫폼 모듈), eSE(embedded Secure Element), ARM TrustZone, Intel SGX 및 사용자 지정 보안 실리콘 기술을 비롯하여 다양합니다.  IoT 장치의 물리적 접근성과 관련된 위협을 감안할 때 장치에 대한 안전한 실리콘 루트를 사용하는 것이 좋습니다.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 보안 관리자 통합 및 유지 관리

IoT Edge 보안 관리자의 목표는 사용자 지정 강화를 위해 Azure IoT Edge 플랫폼의 보안 및 무결성을 방어하는 구성 요소를 식별하고 격리하는 것입니다. 디바이스 제조업체 등의 타사는 디바이스 하드웨어에서 제공되는 사용자 지정 보안 기능을 사용해야 합니다.  다음 단계 섹션에서 Linux 및 Windows 플랫폼에서 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Azure IoT 보안 관리자를 강화하는 방법을 확인할 수 있는 링크를 참조하세요. 이러한 예제에서는 소프트웨어 또는 가상 TPM을 사용하지만, 개별 TPM 디바이스를 사용하는 경우에도 해당 예제가 직접 적용됩니다.  

## <a name="next-steps"></a>다음 단계

[인텔리전트 에지 보안](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) 블로그를 참조하세요.

[Linux 가상 머신에서 가상 TPM을 사용하는 IoT Edge 디바이스](how-to-auto-provision-simulated-device-linux.md)를 만들고 프로비전합니다.

[Windows에서 시뮬레이션된 TPM을 사용하는 IoT Edge 디바이스](how-to-auto-provision-simulated-device-windows.md)를 만들고 프로비전합니다.
