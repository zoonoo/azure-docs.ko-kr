---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: df577ab2b5e9658fd55324c8fd6fd008621b4d46
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545891"
---
이 문서에는 Azure 가상 시스템에 대한 보안 권장 사항이 포함되어 있습니다. 다음 권장 사항을 준수하여 공동 책임에 대한 모델에 설명된 보안 의무를 이행하는 데 도움을 줍니다. 또한 권장 사항은 웹 앱 솔루션에 대한 전반적인 보안을 개선하는 데도 도움이 됩니다. Microsoft가 서비스 공급자 의 책임을 수행하기 위해 수행하는 일에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임을](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)참조하십시오.

이 문서의 권장 사항 중 일부는 Azure 보안 센터에서 자동으로 해결할 수 있습니다. Azure 보안 센터는 Azure의 리소스에 대한 첫 번째 방어선입니다. Azure 리소스의 보안 상태를 주기적으로 분석하여 잠재적인 보안 취약점을 식별합니다. 그런 다음 취약점을 해결하는 방법을 권장합니다. 자세한 내용은 [Azure 보안 센터의 보안 권장 사항을](../articles/security-center/security-center-recommendations.md)참조하십시오.

Azure 보안 센터에 대한 일반 정보는 [Azure 보안 센터란 무엇입니까?](../articles/security-center/security-center-intro.md)

## <a name="general"></a>일반

| 권장 | 주석 | Security Center |
|-|----|--|
| 사용자 지정 VM 이미지를 빌드할 때 최신 업데이트를 적용합니다. | 이미지를 만들기 전에 운영 체제 및 이미지의 일부가 될 모든 응용 프로그램에 대한 최신 업데이트를 설치합니다.  | - |
| 최신 상태로 VM 유지 | Azure 자동화의 [업데이트 관리](../articles/automation/automation-update-management.md) 솔루션을 사용하여 Azure의 Windows 및 Linux 컴퓨터에 대한 운영 체제 업데이트를 관리할 수 있습니다. | [예](../articles/security-center/security-center-apply-system-updates.md) |
| VM을 백업합니다. | [Azure Backup은](../articles/backup/backup-overview.md) 응용 프로그램 데이터를 보호하는 데 도움이 되며 운영 비용이 최소화됩니다. 애플리케이션 오류로 인해 데이터가 손상되고 사용자 오류로 인해 애플리케이션에 버그가 발생할 수 있습니다. Azure 백업은 Windows 및 Linux를 실행하는 VM을 보호합니다. | - |
| 복원력과 가용성을 높이기 위해 여러 VM을 사용합니다. | VM에서 가용성이 높은 응용 프로그램을 실행하는 경우 여러 VM 또는 [가용성 집합을](../articles/virtual-machines/windows/manage-availability.md)사용합니다. | - |
| 비즈니스 연속성 및 재해 복구(BCDR) 전략을 채택합니다. | Azure 사이트 복구를 사용하면 비즈니스 연속성을 지원하도록 설계된 다양한 옵션 중에서 선택할 수 있습니다. 다른 복제 및 장애 조치 시나리오를 지원합니다. 자세한 내용은 [사이트 복구 정보를](../articles/site-recovery/site-recovery-overview.md)참조하십시오. | - |

## <a name="data-security"></a>데이터 보안

| 권장 | 주석 | Security Center |
|-|----|--|
| 운영 체제 디스크를 암호화합니다. | [Azure 디스크 암호화를](../articles/security/azure-security-disk-encryption-overview.md) 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 필요한 키가 없으면 암호화된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크를 복사할 경우 가능한 무단 액세스로부터 저장된 데이터를 보호합니다.| [예](../articles/security-center/security-center-apply-disk-encryption.md) |
| 데이터 디스크를 암호화합니다. | [Azure 디스크 암호화를](../articles/security/azure-security-disk-encryption-overview.md) 사용하면 Windows 및 Linux IaaS VM 디스크를 암호화할 수 있습니다. 필요한 키가 없으면 암호화된 디스크의 내용을 읽을 수 없습니다. 디스크 암호화는 디스크를 복사할 경우 가능한 무단 액세스로부터 저장된 데이터를 보호합니다.| -  |
| 설치된 소프트웨어를 제한합니다. | 설치된 소프트웨어를 솔루션을 성공적으로 적용하는 데 필요한 것으로 제한합니다. 이 지침은 솔루션의 공격 표면을 줄이는 데 도움이 됩니다. | - |
| 바이러스 백신 또는 맬웨어 방지를 사용합니다. | Azure에서는 Microsoft, 시만텍, 트렌드 마이크로 및 카스퍼스키와 같은 보안 공급업체의 맬웨어 방지 소프트웨어를 사용할 수 있습니다. 이 소프트웨어는 악성 파일, 애드웨어 및 기타 위협으로부터 VM을 보호하는 데 도움이됩니다. 응용 프로그램 워크로드에 따라 Microsoft 맬웨어 방지를 배포할 수 있습니다. 기본 보안 기본 또는 고급 사용자 지정 구성을 사용합니다. 자세한 내용은 [Azure 클라우드 서비스 및 가상 컴퓨터에 대한 Microsoft 맬웨어 방지](../articles/security/azure-security-antimalware.md)를 참조하십시오. | - |
| 키와 비밀을 안전하게 저장합니다. | 응용 프로그램 소유자에게 중앙에서 관리되는 안전한 옵션을 제공하여 비밀 및 키 관리를 간소화합니다. 이 관리를 통해 우발적인 손상이나 누출의 위험을 줄일 수 있습니다. Azure Key Vault는 FIPS 140-2 수준 2로 인증된 하드웨어 보안 모듈(HSM)에 키를 안전하게 저장할 수 있습니다. 키와 비밀을 저장하기 위해 FIP 140.2 수준 3을 사용해야 하는 경우 [Azure 전용 HSM](../articles/dedicated-hsm/overview.md)을 사용할 수 있습니다. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 | 주석 | Security Center |
|-|----|--|
| VM 인증을 중앙 집중화합니다. | [Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md)인증을 사용하여 Windows 및 Linux VM의 인증을 중앙 집중화할 수 있습니다. | - |

## <a name="monitoring"></a>모니터링

| 권장 | 주석 | Security Center |
|-|----|--|
| VM을 모니터링합니다. | [VM용 Azure 모니터를](../articles/azure-monitor/insights/vminsights-overview.md) 사용하여 Azure VM 및 가상 시스템 크기 집합의 상태를 모니터링할 수 있습니다. VM의 성능 문제로 인해 가용성의 보안 원칙을 위반하는 서비스 중단이 발생할 수 있습니다. | - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | Security Center |
|-|----|--|
| 관리 포트에 대한 액세스를 제한합니다. | 공격자는 공용 클라우드 IP 범위를 검색하여 개방형 관리 포트를 검색하고 일반적인 암호 및 알려진 패치되지 않은 취약점과 같은 "쉬운" 공격을 시도합니다. [JIT(적시) VM 액세스를](../articles/security-center/security-center-just-in-time.md) 사용하여 Azure VM에 대한 인바운드 트래픽을 잠그고 필요할 때 VM에 대한 간편한 연결을 제공하면서 공격에 대한 노출을 줄일 수 있습니다. | - |
| 네트워크 액세스를 제한합니다. | 네트워크 보안 그룹을 사용하면 네트워크 액세스를 제한하고 노출된 끝점 수를 제어할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 만들기, 변경 또는 삭제를](../articles/virtual-network/manage-network-security-group.md)참조하십시오. | - |

## <a name="next-steps"></a>다음 단계

추가 보안 요구 사항에 대해 알아보려면 응용 프로그램 공급자에게 문의하십시오. 보안 응용 프로그램 개발에 대한 자세한 내용은 [보안 개발 설명서를](../articles/security/fundamentals/abstract-develop-secure-apps.md)참조하십시오.
