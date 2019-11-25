---
title: 보안 프레임워크 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge를 개발하는 데 사용되었으며 솔루션을 디자인할 때 고려해야 하는 보안, 인증 및 권한 부여 표준에 대해 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0d315c7955fff854fc52f73de16eda71b9296b40
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452457"
---
# <a name="security-standards-for-azure-iot-edge"></a>Azure IoT Edge에 대한 보안 표준

Azure IoT Edge addresses the risks that are inherent when moving your data and analytics to the intelligent edge. The IoT Edge security standards balance flexibility for different deployment scenarios with the protection that you expect from all Azure services. 

IoT Edge runs on various makes and models of hardware, supports several operating systems, and applies to diverse deployment scenarios. The risk of a deployment scenario depends on factors that include solution ownership, deployment geography, data sensitivity, privacy, application vertical, and regulatory requirements. Rather than offering concrete solutions for specific scenarios, IoT Edge is an extensible security framework based on well-grounded principles that are designed for scale. 
 
이 문서에서는 IoT Edge 보안 프레임워크의 개요를 제공합니다. For more information, see [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/).

## <a name="standards"></a>표준

표준에서는 보안의 특징인 감시의 용이성과 구현의 용이성이 증진됩니다. A security solution should lend itself to scrutiny under evaluation to build trust and shouldn't be a hurdle to deployment. The design of the framework to secure Azure IoT Edge is based on time-tested and industry proven security protocols for familiarity and reuse. 

## <a name="authentication"></a>Authentication

When you deploy an IoT solution, you need to know that only trusted actors, devices, and modules have access to your solution. Certificate-based authentication is the primary mechanism for authentication for the Azure IoT Edge platform. This mechanism is derived from a set of standards governing Public Key Infrastructure (PKiX) by the Internet Engineering Task Force (IETF).     

물리적 또는 네트워크 연결을 통해 Azure IoT Edge 디바이스와 상호 작용하는 모든 디바이스, 모듈 및 행위자는 고유한 인증서 ID가 있어야 합니다. Not every scenario or component may lend itself to certificate-based authentication, so the extensibility of the security framework offers secure alternatives. 

For more information, see [Azure IoT Edge certificate usage](iot-edge-certs.md).

## <a name="authorization"></a>권한 부여

최소 권한의 원칙은 사용자 및 시스템 구성 요소가 해당 역할을 수행하는 데 필요한 최소한의 리소스 및 데이터 세트에만 액세스해야 한다고 지정합니다. 디바이스, 모듈 및 작업자는 권한 범위 내에서 구조적으로 허용되는 경우에만 해당 리소스 및 데이터에 대한 액세스 권한을 얻을 수 있습니다. Some permissions are configurable with sufficient privileges and others are architecturally enforced.  For example, some modules may be authorized to connect to Azure IoT Hub. However, there is no reason why a module in one IoT Edge device should access the twin of a module in another IoT Edge device.

Other authorization schemes include certificate signing rights and role-based access control (RBAC). 

## <a name="attestation"></a>증명

Attestation ensures the integrity of software bits, which is important for detecting and preventing malware.  The Azure IoT Edge security framework classifies attestation under three main categories:

* 정적 증명
* 런타임 증명
* 소프트웨어 증명

### <a name="static-attestation"></a>정적 증명

Static attestation verifies the integrity of all software on a device during power-up, including the operating system, all runtimes, and configuration information. Because static attestation occurs during power-up, it's often referred to as secure boot. The security framework for IoT Edge devices extends to manufacturers and incorporates secure hardware capabilities that assure static attestation processes. These processes include secure boot and secure firmware upgrade.  Working in close collaboration with silicon vendors eliminates superfluous firmware layers, so minimizes the threat surface. 

### <a name="runtime-attestation"></a>런타임 증명

Once a system has completed a secure boot process, well-designed systems should detect attempts to inject malware and take proper countermeasures. Malware attacks may target the system's ports and interfaces. If malicious actors have physical access to a device, they may tamper with the device itself or use side-channel attacks to gain access. Such malcontent, whether malware or unauthorized configuration changes, can't be detected by static attestation because it is injected after the boot process. 디바이스의 하드웨어가 제공하거나 시행하는 대책을 통해 이러한 위협에서 벗어날 수 있습니다.  The security framework for IoT Edge explicitly calls for extensions that combat runtime threats.  

### <a name="software-attestation"></a>소프트웨어 증명

All healthy systems, including intelligent edge systems, need patches and upgrades.  Security is important for update processes, otherwise they can be potential threat vectors.  The security framework for IoT Edge calls for updates through measured and signed packages to assure the integrity of and authenticate the source of the packages.  This standard applies to all operating systems and application software bits. 

## <a name="hardware-root-of-trust"></a>신뢰할 수 있는 하드웨어 루트

For many intelligent edge devices, especially devices that can be physically accessed by potential malicious actors, hardware security is the last defense for protection. Tamper resistant hardware is crucial for such deployments. Azure IoT Edge encourages secure silicon hardware vendors to offer different flavors of hardware root of trust to accommodate various risk profiles and deployment scenarios. 하드웨어 트러스트는 신뢰할 수 있는 플랫폼 모듈(ISO/IEC 11889) 및 신뢰할 수 있는 컴퓨팅 그룹의 DICE(Device Identifier Composition Engine)와 같은 일반적인 보안 프로토콜 표준을 통해 얻을 수 있습니다. Secure enclave technologies like TrustZones and Software Guard Extensions (SGX) also provide hardware trust. 

## <a name="certification"></a>인증

To help customers make informed decisions when procuring Azure IoT Edge devices for their deployment, the IoT Edge framework includes certification requirements.  Foundational to these requirements are certifications pertaining to security claims and certifications pertaining to validation of the security implementation.  For example, a security claim certification means that the IoT Edge device uses secure hardware known to resist boot attacks. A validation certification means that the secure hardware was properly implemented to offer this value in the device.  In keeping with the principle of simplicity, the framework tries to keep the burden of certification minimal.   

## <a name="extensibility"></a>확장성

With IoT technology driving different types of business transformations, security should evolve in parallel to address emerging scenarios.  The Azure IoT Edge security framework starts with a solid foundation on which it builds in extensibility into different dimensions to include: 

* Azure IoT Hub용 Device Provisioning Service와 같은 당사 보안 서비스
* Third-party services like managed security services for different application verticals (like industrial or healthcare) or technology focus (like security monitoring in mesh networks, or silicon hardware attestation services) through a rich network of partners.
* 인증 및 ID 관리를 위한 인증서 이외의 보안 기술 사용과 같은 대체 보안 전략으로 수정을 포함하는 기존 시스템
* 새로운 보안 하드웨어 기술 및 실리콘 파트너의 적용을 채택하는 안전한 하드웨어

In the end, securing the intelligent edge requires collaborative contributions from an open community driven by the common interest in securing IoT.  These contributions might be in the form of secure technologies or services.  The Azure IoT Edge security framework offers a solid foundation for security that is extensible for the maximum coverage to offer the same level of trust and integrity in the intelligent edge as with Azure cloud.  

## <a name="next-steps"></a>다음 단계

Azure IoT Edge에서 [지능형 에지 보안](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) 방법에 대해 알아봅니다.
