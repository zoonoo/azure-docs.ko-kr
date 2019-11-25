---
title: 보안 관리자가 디바이스, 소프트웨어를 보호하는 방법 알아보기 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 디바이스 보안 태세와 보안 서비스의 무결성을 관리합니다.
services: iot-edge
keywords: 보안, 보안 요소, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457540"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 보안 관리자

Azure IoT Edge 보안 관리자는 보안 실리콘 하드웨어를 추상화하여 IoT Edge 디바이스 및 이 디바이스의 모든 구성 요소를 보호하기 위해 잘 정립된 보안 코어입니다. It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Azure IoT Edge 보안 관리자](media/edge-security-manager/iot-edge-security-manager.png)

IoT Edge 보안 관리자의 목표는 IoT Edge 디바이스 및 모든 고유 소프트웨어 작업의 무결성을 보호하는 것입니다. The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  IoT Edge 보안 관리자는 보안 실리콘 하드웨어(사용 가능한 경우)와 연동되어 가능한 최고 수준의 보안을 보장하는 소프트웨어입니다.  

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

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>디자인 원칙

IoT Edge 보안 디먼은 작동 무결성 최대화 및 블로트/변동 최소화라는 두 가지 핵심 원칙을 따릅니다.

#### <a name="maximize-operational-integrity"></a>작동 무결성 극대화

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. 적절한 통합을 통해 트러스트 하드웨어의 루트는 변조를 방지하기 위해 보안 디먼을 런타임 시 정적으로 측정하고 모니터링합니다.

IoT 디바이스에 물리적으로 액세스하는 행위는 항상 위협으로 간주됩니다. 따라서 IoT Edge 보안 디먼의 무결성을 방어하려면 신뢰할 수 있는 하드웨어 루트의 역할이 중요합니다.  Hardware root of trust come in two varieties:

* 비밀 및 암호화 키와 같은 중요한 정보를 보호하기 위한 보안 요소
* 키와 같은 비밀의 보호를 위한 보안 enclave 및 계량 및 요금 청구와 같은 중요한 워크로드.

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>블로트 및 변동 최소화

IoT Edge 보안 디먼이 따르는 또 다른 핵심 원칙은 변동의 최소화합니다.  최고 수준의 신뢰를 보장하기 위해 IoT Edge 보안 디먼은 신뢰할 수 있는 디바이스 하드웨어 루트와 밀접하게 결합되어 네이티브 코드로 작동할 수 있습니다.  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  예를 들어 업데이트를 건너뛰고 운영 가용성을 최대화하거나 트러스트 하드웨어의 루트가 대규모 업데이트 페이로드를 처리하기에 제약이 많은 경우가 해당됩니다.  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>IoT Edge 보안 디먼의 아키텍처

![Azure IoT Edge 보안 디먼](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>클라우드 인터페이스

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>관리 API

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. 이러한 등록은 모듈의 ID를 모듈의 일부 속성과 매핑합니다. 이러한 속성의 몇 가지 예에는 컨테이너에서 실행 중인 프로세스의 식별자(pid) 또는 Docker 컨테이너 콘텐츠의 해시가 있습니다.

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  IoT Edge 보안 디먼은 IoT Edge 에이전트를 부트스트랩하고 시작하기 때문에 IoT Edge 에이전트가 변조되지 않았다는 것을 증명한 후에, IoT Edge 에이전트에 대해 암시적인 등록을 만들 수 있습니다. The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>컨테이너 API

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>워크로드 API

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. 트러스트 번들에는 모듈이 신뢰해야 하는 다른 모든 서버용 CA 인증서가 포함됩니다.

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. 성공하면 등록의 속성을 사용하여 모듈을 측정합니다. If the result of the measurement process matches the registration, a new proof of identity is generated. 그리고 해당 CA 인증서(트러스트 번들)가 모듈에 반환됩니다.  모듈은 이 인증서를 사용하여 IoT Hub나 다른 모듈에 연결하거나 서버를 시작합니다. When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>통합 및 유지 관리

Microsoft는 [IoT Edge 보안 디먼용 기본 코드 베이스를 GitHub](https://github.com/Azure/iotedge/tree/master/edgelet)에 유지 관리합니다.

#### <a name="installation-and-updates"></a>설치 및 업데이트

IoT Edge 보안 디먼의 설치 및 업데이트는 운영 체제의 패키지 관리 시스템을 통해 관리됩니다. 신뢰할 수 있는 하드웨어 루트가 있는 IoT Edge 디바이스는 보안 부트 및 업데이트 관리 시스템을 통해 수명 주기를 관리하여 디먼의 무결성을 추가로 강화해야 합니다. Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>버전 관리

IoT Edge 런타임은 IoT Edge 보안 디먼의 버전을 추적 및 보고합니다. 버전은 IoT Edge 에이전트 모듈이 보고한 속성의 *runtime.platform.version* 특성으로 보고됩니다.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>하드웨어 보안 모듈 플랫폼 추상화 계층(HSM PAL)

HSM PAL은 트러스트 하드웨어의 모든 루트를 추상화하여 IoT Edge의 개발자나 사용자를 복잡한 문제로부터 떼어놓습니다.  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  HSM PAL의 실제 구현은 사용 중인 특정 보안 하드웨어에 따라 달라집니다. 보안 하드웨어가 있으면 사실상 모든 보안 실리콘 하드웨어를 사용할 수 있습니다.

## <a name="secure-silicon-root-of-trust-hardware"></a>트러스트 하드웨어의 보안 실리콘 루트

보안 실리콘은 IoT Edge 디바이스 하드웨어 내부에 신뢰를 확보하는 데 필요합니다.  보안 실리콘은 TPM(신뢰할 수 있는 플랫폼 모듈), eSE(embedded Secure Element), ARM TrustZone, Intel SGX 및 사용자 지정 보안 실리콘 기술을 비롯하여 다양합니다.  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>IoT Edge 보안 관리자 통합 및 유지 관리

IoT Edge 보안 관리자의 목표는 사용자 지정 강화를 위해 Azure IoT Edge 플랫폼의 보안 및 무결성을 방어하는 구성 요소를 식별하고 격리하는 것입니다. 디바이스 제조업체 등의 타사는 디바이스 하드웨어에서 제공되는 사용자 지정 보안 기능을 사용해야 합니다.  다음 단계 섹션에서 Linux 및 Windows 플랫폼에서 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Azure IoT 보안 관리자를 강화하는 방법을 확인할 수 있는 링크를 참조하세요. 이러한 예제에서는 소프트웨어 또는 가상 TPM을 사용하지만, 개별 TPM 디바이스를 사용하는 경우에도 해당 예제가 직접 적용됩니다.  

## <a name="next-steps"></a>다음 단계

[인텔리전트 에지 보안](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) 블로그를 참조하세요.

[Linux 가상 머신에서 가상 TPM을 사용하는 IoT Edge 디바이스](how-to-auto-provision-simulated-device-linux.md)를 만들고 프로비전합니다.

[Windows에서 시뮬레이션된 TPM을 사용하는 IoT Edge 디바이스](how-to-auto-provision-simulated-device-windows.md)를 만들고 프로비전합니다.
