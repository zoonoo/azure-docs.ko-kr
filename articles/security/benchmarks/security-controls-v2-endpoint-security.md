---
title: Azure 보안 벤치 마크 V2-끝점 보안
description: Azure Security 벤치 마크 V2 끝점 보안
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: c04e4233ded34ceaeec9cd9afb240d3d1ac864e0
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059259"
---
# <a name="security-control-endpoint-security"></a>보안 제어: 끝점 보안

끝점 보안은 끝점 검색 및 응답의 컨트롤을 포함 합니다. 여기에는 Azure 환경에서 끝점에 대해 EDR (끝점 검색 및 응답) 및 맬웨어 방지 서비스를 사용 하는 작업이 포함 됩니다.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: EDR (끝점 검색 및 응답) 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

서버 및 클라이언트에 대해 EDR (끝점 검색 및 응답) 기능을 사용 하도록 설정 하 고 SIEM 및 보안 작업 프로세스와 통합 합니다.

Microsoft Defender Advanced Threat Protection은 엔터프라이즈 끝점 보안 플랫폼의 일부로 EDR 기능을 제공 하 여 고급 위협을 방지 하 고, 감지 하 고, 조사 하 고, 대응 합니다. 

- [Microsoft Defender Advanced Threat Protection 개요](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [비 Windows 서버용 Microsoft Defender ATP 서비스](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**책임**: Customer

**고객 보안 관련자**:

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: 중앙에서 관리 되는 최신 맬웨어 방지 소프트웨어 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

실시간 및 정기적인 검색을 지 원하는 중앙 집중식 관리 끝점 맬웨어 방지 솔루션 사용

Azure Security Center는 가상 머신에 많이 사용 되는 여러 맬웨어 방지 솔루션의 사용을 자동으로 식별 하 고 endpoint protection 실행 상태를 보고 하 고 권장 사항을 만들 수 있습니다. 

Azure Cloud Services에 대 한 Microsoft 맬웨어 방지 프로그램은 Windows Vm (가상 머신)에 대 한 기본 맬웨어 방지 프로그램입니다. Linux Vm의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.  또한 데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 Azure Storage 계정에 업로드 된 맬웨어를 검색할 수 있습니다. 

- [Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지 프로그램을 구성 하는 방법](../fundamentals/antimalware.md)

- [지원되는 Endpoint Protection 솔루션](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**책임**: Customer

**고객 보안 관련자**:

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: 맬웨어 방지 소프트웨어 및 서명이 업데이트 되었는지 확인

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

맬웨어 방지 서명이 신속 하 고 일관 되 게 업데이트 되는지 확인 합니다. 

Azure Security Center "계산 앱"의 권장 사항을 따라 &amp; 모든 끝점이 최신 서명으로 최신 상태를 유지 하도록 합니다. Microsoft 맬웨어 방지 프로그램은 기본적으로 최신 서명 및 엔진 업데이트를 자동으로 설치 합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.

- [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법](../fundamentals/antimalware.md)

**책임**: Customer

**고객 보안 관련자**:

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [보안 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

