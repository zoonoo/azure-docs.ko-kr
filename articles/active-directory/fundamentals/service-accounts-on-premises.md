---
title: Active Directory 서비스 계정 소개 | Azure Active Directory
description: Active Directory의 서비스 계정 유형과 보안 방법에 대한 소개입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9024bc9fbd460f403db2da8a65af1e9bd2e771b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645619"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory 서비스 계정 소개

서비스에는 로컬 및 네트워크 리소스에 대한 액세스 권한을 결정하는 기본 보안 ID가 있습니다. Microsoft Win32 서비스의 보안 컨텍스트는 서비스를 시작하는 데 사용되는 서비스 계정에 의해 결정됩니다. 서비스 계정은 다음 작업에 사용됩니다.
* 서비스 식별 및 인증
* 성공적으로 서비스 시작
* 코드 또는 애플리케이션 액세스 또는 실행
* 프로세스 시작 

## <a name="types-of-on-premises-service-accounts"></a>온-프레미스 서비스 계정 유형

사용 사례에 따라 MSA(관리형 서비스 계정), 컴퓨터 계정 또는 사용자 계정을 사용하여 서비스를 실행할 수 있습니다. 서비스를 테스트하여 관리형 서비스 계정 사용이 가능한지 확인해야 합니다. 가능하다면 그중 하나를 사용해야 합니다.

### <a name="group-msa-accounts"></a>그룹 MSA 계정

온-프레미스 환경에서 실행되는 서비스에 대해 가능한 경우 [그룹 관리형 서비스 계정](service-accounts-group-managed.md)(gMSAs)을 사용합니다. gMSAs는 서버 팜 또는 네트워크 부하 분산 장치 뒤에서 실행되는 서비스에 대해 단일 ID 솔루션을 제공합니다. 단일 서버에서 실행되는 서비스에도 사용할 수 있습니다. [gMSAs에는 충족되어야 하는 특정 요구 사항이 있습니다.](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>독립 실행형 MSA 계정

gMSA를 사용할 수 없는 경우에는 [독립 실행형 관리 서비스 계정](service-accounts-standalone-managed.md)(sMSA)를 사용합니다. sMSA에는 Windows Server 2008R2 이상이 필요합니다. gMSA와 달리 sMSA는 한 서버에서만 실행됩니다. 이는 해당 서버의 여러 서비스에서 사용할 수 있습니다.

### <a name="computer-account"></a>컴퓨터 계정

MSA를 사용할 수 없는 경우 [컴퓨터 계정](service-accounts-computer.md)을 사용하여 조사합니다. LocalSystem 계정은 로컬 컴퓨터에 대해 광범위한 권한이 있고 네트워크의 컴퓨터 ID 역할을 하는 미리 정의된 로컬 계정입니다.   
LocalSystem 계정으로 실행되는 서비스는 <domain_name>\<computer_name> 형식인 컴퓨터 계정의 자격 증명을 사용하여 네트워크 리소스에 액세스합니다.

NT AUTHORITY\SYSTEM은 LocalSystem 계정에 대해 미리 정의된 이름입니다. 서비스를 시작하고 해당 서비스에 대한 보안 컨텍스트를 제공하는 데 사용할 수 있습니다.

> [!NOTE]
> 컴퓨터 계정을 사용하는 경우 컴퓨터의 어떤 서비스가 해당 계정을 사용하는지 알 수 없으므로 어떤 서비스가 변경 중인지 감사할 수 없습니다. 

### <a name="user-account"></a>사용자 계정

MSA를 사용할 수 없는 경우 [사용자 계정](service-accounts-user-on-premises.md)을 사용하여 조사합니다. 사용자 계정은 도메인 사용자 계정 또는 로컬 사용자 계정일 수 있습니다.

도메인 사용자 계정을 사용하면 서비스에서 Windows 및 Microsoft Active Directory Domain Services의 서비스 보안 기능을 최대한 활용할 수 있습니다. 서비스에는 계정에 대한 로컬 및 네트워크 액세스 권한이 부여됩니다. 또한 계정이 구성원인 모든 그룹의 사용 권한도 갖게 됩니다. 도메인 서비스 계정은 Kerberos 상호 인증을 지원합니다.

로컬 사용자 계정(이름 형식: “.\UserName”)은 호스트 컴퓨터의 SAM 데이터베이스에만 존재합니다. Active Directory Domain Services에 사용자 개체가 없습니다. 로컬 계정은 도메인에서 인증할 수 없습니다. 따라서 로컬 사용자 계정의 보안 컨텍스트에서 실행되는 서비스는 네트워크 리소스에 액세스할 수 없습니다(익명 사용자 제외). 로컬 사용자 컨텍스트에서 실행되는 서비스는 해당 클라이언트에서 서비스를 인증하는 Kerberos 상호 인증을 지원할 수 없습니다. 그렇기 때문에 로컬 사용자 계정은 일반적으로 디렉터리 사용 서비스에 적합하지 않습니다.

> [!IMPORTANT]
> 권한 있는 그룹의 구성원 자격은 보안에 위험이 될 수 있는 권한을 줄 수도 있기 때문에 서비스 계정은 권한 있는 그룹의 구성원이 될 수 없습니다. 각 서비스에는 감사 및 보안을 위해 자체 서비스 계정이 있어야 합니다.

## <a name="choose-the-right-type-of-service-account"></a>올바른 종류의 서비스 계정 선택


| 조건| gMSA| sMSA| 컴퓨터 계정| 사용자 계정 |
| - | - | - | - | - |
| 앱이 단일 서버에서 실행됨| 예| 예. 가급적 gMSA를 사용합니다| 예. 가급적 MSA를 사용합니다| 예. 가급적 MSA를 사용합니다. |
| 앱이 여러 서버에서 실행됨| 예| 예| 아니요. 계정이 서버에 연결되어 있습니다.| 예. 가급적 MSA를 사용합니다. |
| 앱이 부하 분산 장치 뒤에서 실행됨| 예| 예| 아니요| 예. gMSA를 사용할 수 없는 경우에만 사용합니다. |
| 앱이 Windows Server 2008 R2에서 실행됨| 예| 예| 예. 가급적 MSA를 사용합니다.| 예. 가급적 MSA를 사용합니다. |
| Windows Server 2012에서 실행| 예| 예. 가급적 gMSA를 사용합니다| 예. 가급적 MSA를 사용합니다| 예. 가급적 MSA를 사용합니다. |
| 단일 서버로 서비스 계정을 제한하기 위한 요구 사항| 예| 예| 예. 가급적 sMSA를 사용합니다| 아니요. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>서버 로그 및 PowerShell을 사용하여 조사

서버 로그를 사용하여 애플리케이션이 실행되는 서버 및 서버 수를 확인할 수 있습니다.

다음 PowerShell 명령을 실행하여 네트워크의 모든 서버에 대한 Windows Server 버전 목록을 가져올 수 있습니다. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>온-프레미스 서비스 계정 찾기

“svc.”와 같은 접두사를 추가하는 것이 좋습니다. 서비스 계정으로 사용되는 모든 계정 이 명명 규칙을 사용하면 쉽게 찾고 관리할 수 있습니다. 또한 서비스 계정 및 서비스 계정의 소유자에 대한 설명 특성을 사용하는 것을 고려해야 합니다. 이 특성은 팀 별칭이나 보안 팀 소유자가 될 수 있습니다.

온-프레미스 서비스 계정을 찾는 것은 보안을 보장하기 위한 핵심입니다. 또한 MSA가 아닌 계정에 대해서는 어려울 수 있습니다. 중요한 온-프레미스 리소스에 대한 액세스 권한이 있는 모든 계정을 검토하고 서비스 계정으로 작동할 수 있는 컴퓨터 또는 사용자 계정을 결정하는 것이 좋습니다. 계정을 찾기 위해서 다음 방법을 사용할 수도 있습니다.

* 각 계정 유형에 대한 문서에는 해당 계정 유형을 찾기 위한 단계가 자세히 나와 있습니다. 이 문서에 대한 링크는 이 문서의 다음 단계 섹션을 참조하세요.

## <a name="document-service-accounts"></a>문서 서비스 계정

온-프레미스 환경에서 서비스 계정을 찾았으면 각 계정에 대해 다음 정보를 문서화합니다. 

* 소유자입니다. 계정 유지 관리를 담당하는 사람입니다.

* 용도입니다. 계정이 나타내는 애플리케이션 또는 기타 용도입니다. 

* 사용 권한 범위 어떤 권한이 있으며, 어떤 권한이 있어야 하나요? 그룹이 속해 있으면 어떻게 되나요?

* 위험 프로필 이 계정이 손상된 경우 비즈니스에 미치는 위험은 무엇인가요? 위험이 높으면 MSA를 사용합니다.

* 예상 수명 및 정기 증명 이 계정은 얼마 동안 살아있나요? 소유자가 얼마나 자주 필요성을 검토하고 증명해야 하나요?

* 암호 보안 사용자 및 로컬 컴퓨터 계정의 경우 암호가 저장됩니다. 암호가 안전하게 유지되는지 확인하고 액세스 권한이 있는 문서를 확인합니다. [Privileged Identity Management](../privileged-identity-management/pim-configure.md)를 사용하여 저장된 암호를 보호하는 것이 좋습니다. 

  

## <a name="next-steps"></a>다음 단계

서비스 계정 보안에 대해서는 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [그룹 관리형 서비스 계정 보안](service-accounts-group-managed.md)

* [독립 실행 관리형 서비스 계정 보안](service-accounts-standalone-managed.md)

* [컴퓨터 계정 보안](service-accounts-computer.md)

* [사용자 계정 보안](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

