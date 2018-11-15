---
title: Azure IoT Edge 보안 관리자 | Microsoft Docs
description: IoT Edge 장치 보안 태세와 보안 서비스의 무결성을 관리합니다.
services: iot-edge
keywords: 보안, 보안 요소, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6231f50134f7dcfd29e8754841847beb69570204
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567692"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 보안 관리자

Azure IoT Edge 보안 관리자는 보안 실리콘 하드웨어를 추상화하여 IoT Edge 장치 및 이 장치의 모든 구성 요소를 보호하기 위해 잘 정립된 보안 코어입니다. 보안 강화를 위한 중심 지점이며 원래 장치 제조업체(OEM)에 기술 통합 지점을 제공합니다.

![Azure IoT Edge 보안 관리자](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 보안 관리자의 목표는 IoT Edge 장치 및 모든 고유 소프트웨어 작업의 무결성을 보호하는 것입니다.  이러한 작업은 트러스트 하드웨어의 기본 하드웨어 루트 (사용 가능한 경우)에서 트러스트를 전환하여 IoT Edge 런타임을 안전하게 부트스트랩하고 작업의 무결성을 계속 모니터링하여 수행합니다.  IoT Edge 보안 관리자는 본질적으로 가능한 경우 보안 실리콘 하드웨어와 함께 작동하는 소프트웨어로 구성되어 있으며 최대한의 보안을 보장할 수 있도록 지원합니다.  

IoT Edge 보안 관리자의 책임에는 다음 사항이 포함됩니다(이에 국한되지 않음).

* Azure IoT Edge 장치의 측정된 보안 부트스트래핑.
* 해당되는 경우 장치 ID 프로비전 및 트러스트 전환.
* Device Provisioning Service와 같은 클라우드 서비스의 장치 구성 요소 호스트 및 보호.
* IoT Edge 모듈에 모듈 고유의 ID를 안전하게 프로비전.
* 공증 서비스를 통한 신뢰할 수 있는 장치 하드웨어 루트에 대한 게이트키퍼.
* 런타임 시 IoT Edge 작업의 무결성 모니터링.

IoT Edge 보안 관리자는 세 가지 주요 구성 요소로 이루어져 있습니다.

* IoT Edge 보안 디먼.
* 하드웨어 보안 모듈 플랫폼 추상화 계층(HSM PAL).
* 선택 사항이지만 권장되는 신뢰할 수 있는 하드웨어 실리콘 루트 또는 HSM.

## <a name="the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼

IoT Edge 보안 디먼은 IoT Edge 보안 관리자의 논리 연산을 담당하는 소프트웨어입니다.  IoT Edge 장치의 신뢰할 수 있는 컴퓨팅 기반의 상당 부분을 구성합니다. 

### <a name="design-principles"></a>디자인 원칙

책임을 적절하고 실질적으로 수행하기 위해, IoT Edge 보안 디먼은 다음과 같은 핵심 원칙을 준수하는 것을 목표로 합니다.

#### <a name="maximize-operational-integrity"></a>작동 무결성 극대화

IoT Edge 보안 디먼은 주어진 트러스트 하드웨어 루트의 방어 기능 내에서 가능한한 높은 무결성으로 작동하도록 설계되었습니다. 적절한 통합을 통해 트러스트 하드웨어의 루트는 변조를 방지하기 위해 보안 디먼을 런타임 시 정적으로 측정하고 모니터링합니다.

일반적으로 물리적 액세스는 항상 IoT 장치에 대한 위협입니다.  따라서 신뢰할 수 있는 하드웨어 루트는 IoT Edge 보안 디먼의 무결성을 방어하는 데 중요한 역할을 수행합니다.  신뢰할 수 있는 하드웨어 루트는 두 가지로 제공됩니다.

* 비밀 및 암호화 키와 같은 중요한 정보를 보호하기 위한 보안 요소
* 키와 같은 비밀의 보호를 위한 보안 enclave 및 계량 및 요금 청구와 같은 중요한 워크로드.

신뢰할 수 있는 하드웨어 루트를 활용하는 모델이 두 가지 있기 때문에 두 가지 실행 환경이 가능합니다.

* 보안 요소의 사용에 의존하여 중요한 정보를 보호하는 표준 또는 REE(rich execution environment).
* 보안 enclave 기술의 사용에 의존하여 중요한 정보를 보호하고 소프트웨어 실행에 대한 보호를 제공하는 TEE(trusted execution environment).

보안 enclave를 신뢰할 수 있는 하드웨어 루트로 사용하는 장치의 경우 IoT Edge 보안 디먼 내의 중요한 논리가 enclave 내에 상주해야 합니다.  보안 디먼의 중요하지 않은 부분은 TEE 외부에 상주할 수 있습니다.  어떤 경우든 ODM(Original Design Manufacturer)과 OEM(Original Equipment Manufacturer)은 부팅 및 런타임 시 Edge 보안 디먼의 무결성을 측정하고 방어하기 위해 HSM으로부터 트러스트를 확장할 것으로 예상합니다.

#### <a name="minimize-bloat-and-churn"></a>블로트 및 변동 최소화

Edge 보안 디먼의 또 다른 핵심 원칙은 변동을 최소화하는 것입니다.  최고 수준의 신뢰를 위해, IoT Edge 보안 디먼은 가능한 경우 신뢰할 수 있는 장치 하드웨어 루트와 밀접하게 결합하여 네이티브 코드로 작동할 수 있습니다.  이러한 종류의 구현은 신뢰할 수 있는 하드웨어 루트의 보안 업데이트 경로를 통해(OS 제공 업데이트 메커니즘과 반대) 디먼 소프트웨어를 업데이트하는 것이 일반적이며, 이 방법은 특정 하드웨어 및 배포 시나리오에 따라 어려울 수 있습니다.  IoT 장치에 대해 보안 갱신이 강력이 권장되지만, 과도한 업데이트 요구 사항이나 대규모 업데이트 페이로드가 여러 가지 면에서 위협적인 측면을 확장시킬 수 있는 이유가 됩니다.  예를 들어 업데이트를 건너뛰고 운영 가용성을 최대화하거나 트러스트 하드웨어의 루트가 대규모 업데이트 페이로드를 처리하기에 제약이 많은 경우가 해당됩니다.  이와 같이 IoT Edge 보안 디먼의 디자인은 차지하는 공간 및 신뢰할 수 있는 컴퓨팅 기반을 작게 유지하고 업데이트 요구 사항을 최소화하도록 간결합니다.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 보안 디먼의 아키텍처

![Azure IoT Edge 보안 디먼](media/edge-security-manager/iot-edge-security-daemon.png)

IoT Edge 보안 디먼은 보안 강화를 위해 사용 가능한 신뢰할 수 있는 하드웨어 루트 기술을 활용하도록 설계되었습니다.  더욱이 표준 REE(Rich Execution Environment)와 TEE(Trusted Execution Environment) 간의 분할 작업을 감안하여 TEE(Trusted Execution Environment)를 제공하는 하드웨어 기술을 활용할 수 있도록 설계되었습니다.  IoT Edge 보안 디먼 아키텍처의 핵심은 IoT Edge 장치 및 작업의 무결성을 보장할 수 있도록 Edge의 주요 구성 요소의 상호 작용을 가능하게 하는 역할별 인터페이스입니다.

#### <a name="cloud-interface"></a>클라우드 인터페이스

클라우드 인터페이스를 통해 IoT Edge 보안 디먼은 cloud compliment와 같은 클라우드 서비스에 액세스하고 보안 갱신과 같은 장치 보안에 액세스할 수 있습니다.  예를 들어 IoT Edge 보안 디먼은 현재 이 인터페이스를 통해 장치 ID 수명 주기 관리를 위해 Azure IoT Hub [DPS(Device Provisioning Service)](https://docs.microsoft.com/azure/iot-dps/)에 액세스합니다.  

#### <a name="management-api"></a>관리 API

IoT Edge 보안 디먼은 관리 API를 제공하는데 이것은 Edge 모듈을 생성/시작/중지/제거할 때 Edge 에이전트에 의해 호출됩니다. IoT Edge 보안 디먼은 모든 활성 모듈에 대한 “등록”을 저장합니다. 이러한 등록은 모듈의 ID를 모듈의 일부 속성과 매핑합니다. 이러한 속성의 몇 가지 예에는 컨테이너에서 실행 중인 프로세스의 식별자(pid) 또는 Docker 컨테이너 콘텐츠의 해시가 있습니다.

이러한 속성은 호출자가 작업을 수행할 권한이 있다는 것을 워크로드 API(아래 설명 참조)가 증명하는 데 사용됩니다.

관리 API는 권한이 있는 API이며 IoT Edge 에이전트에서만 호출할 수 있습니다.  IoT Edge 보안 디먼은 IoT Edge 에이전트를 부트스트랩하고 시작하기 때문에 IoT Edge 에이전트가 변조되지 않았다는 것을 증명한 후에, IoT Edge 에이전트에 대해 암시적인 등록을 만들 수 있습니다. 워크로드 API가 사용하는 동일한 증명 프로세스는 관리 API에 대한 액세스를 IoT Edge 에이전트로만 제한하는 데 사용됩니다.

#### <a name="container-api"></a>컨테이너 API

IoT Edge 보안 디먼은 모듈 인스턴스화를 위해 Moby 및 Docker와 같이 사용 중인 컨테이너 시스템과 상호 작용할 수 있는 컨테이너 인터페이스를 제공합니다.

#### <a name="workload-api"></a>워크로드 API

워크로드 API는 IoT Edge 에이전트를 비롯한 모든 모듈에 액세스할 수 있는 IoT Edge 보안 디먼 API입니다. HSM 루트 서명된 토큰 또는 X509 인증서의 형태로 ID에 대한 증명을 제공하고 모듈에 해당 트러스트 번들을 제공합니다. 트러스트 번들은 모듈이 신뢰해야 하는 다른 모든 서버에 대한 CA 인증서를 포함합니다.

IoT Edge 보안 디먼은 증명 프로세스를 사용하여 이 API를 보호합니다. 모듈이 이 API를 호출하면 IoT Edge 보안 디먼은 ID에 대한 등록을 찾으려고 합니다. 성공하면 등록의 속성을 사용하여 모듈을 측정합니다. 측정 프로세스의 결과가 등록과 일치하면 새로운 HSM 루트 서명된 토큰 또는 X509 인증서가 생성되고 해당 CA 인증서 (트러스트 번들)가 모듈에 반환됩니다.  모듈은 이 인증서를 사용하여 IoT Hub나 다른 모듈에 연결하거나 서버를 시작합니다. 서명된 토큰 또는 인증서의 만료가 가까운 경우, 새 인증서를 요청하는 것은 모듈의 책임입니다.  ID 갱신 프로세스를 최대한 원활하게 만들기 위해서 추가 고려 사항이 디자인에 통합됩니다.

### <a name="integration-and-maintenance"></a>통합 및 유지 관리

Microsoft는 [IoT Edge 보안 디먼용 기본 코드 베이스를 GitHub](https://github.com/Azure/iotedge/tree/master/edgelet)에 유지 관리합니다.

#### <a name="installation-and-updates"></a>설치 및 업데이트

IoT Edge 보안 디먼의 설치 및 업데이트는 기본 프로세스이므로 운영 체제의 패키지 관리 시스템을 통해 관리됩니다.  하지만 신뢰할 수 있는 하드웨어 루트가 있는 IoT Edge 장치는 장치 보안 부트 및 업데이트 관리 시스템을 통해 수명 주기를 관리하여 디먼의 무결성을 강화할 것으로 예상됩니다.  장치 각각의 기능에 따라 이러한 방법을 찾는 것은 장치 제조업체의 몫입니다.

#### <a name="versioning"></a>버전 관리

IoT Edge 런타임은 IoT Edge 보안 디먼의 버전을 추적 및 보고합니다. 버전은 Edge 에이전트 모듈이 보고한 속성의 *runtime.platform.version* 특성으로 보고됩니다.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>하드웨어 보안 모듈 플랫폼 추상화 계층(HSM PAL)

HSM PAL은 트러스트 하드웨어의 모든 루트를 추상화하여 IoT Edge의 개발자나 사용자를 복잡한 문제로부터 떼어놓습니다.  API(응용 프로그래밍 인터페이스) 및 트랜스 도메인 통신 프로시저(예: 표준 실행 환경과 보안 enclave 사이 통신)의 조합으로 구성됩니다.  HSM PAL의 실제 구현은 사용 중인 특정 보안 하드웨어에 따라 달라집니다.  보안 하드웨어가 있으면 IoT 에코시스템 전반에 걸쳐 거의 모든 보안 실리콘 하드웨어를 사용할 수 있습니다.

## <a name="secure-silicon-root-of-trust-hardware"></a>트러스트 하드웨어의 보안 실리콘 루트

보안 실리콘은 IoT Edge 장치 하드웨어 내부에 신뢰를 확보하는 데 필요합니다.  보안 실리콘은 TPM(신뢰할 수 있는 플랫폼 모듈), eSE(embedded Secure Element), ARM TrustZone, Intel SGX 및 사용자 지정 보안 실리콘 기술을 비롯하여 다양합니다.  IoT 장치에 대한 물리적 액세스 가능성과 관련된 위협을 고려할 때 장치에 안전한 실리콘 기반 루트를 사용하는 것이 좋습니다.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 보안 관리자 통합 및 유지 관리

IoT Edge 보안 관리자의 주요 목표 중 하나는 사용자 지정 강화를 위해 Azure IoT Edge 플랫폼의 보안 및 무결성을 방어하는 데 필요한 구성 요소를 식별하고 격리하는 것입니다.  따라서 타사(예: 장치 제조업체)는 자체 장치 하드웨어에서 사용할 수 있는 사용자 지정 보안 기능을 사용해야 합니다.  아래의 다음 섹션에서 Linux 및 Windows 플랫폼에서 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Azure IoT 보안 관리자를 강화하는 방법에 대한 예제 링크를 참조하세요.  이 예제는 소프트웨어 또는 가상 TPM을 사용하지만 개별 TPM 장치를 사용하는 데 직접 적용됩니다.  

## <a name="next-steps"></a>다음 단계

[인텔리전트 에지 보안](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) 블로그를 참조하세요.

[Linux 가상 머신에서 가상 TPM](how-to-auto-provision-simulated-device-linux.md)을 사용하여 Edge 장치 만들기 및 프로비전

[Windows에서 시뮬레이션된 TPM Edge 장치](how-to-auto-provision-simulated-device-windows.md) 만들기 및 프로비전

<!-- Links -->
[lnk-edge-blog]: https://azure.microsoft.com/blog/securing-the-intelligent-edge/
