---
title: Active Directory 서비스 계정 소개 | Azure Active Directory
description: Active Directory의 서비스 계정 유형과 보안 방법에 대 한 소개입니다.
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
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645619"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Active Directory 서비스 계정 소개

서비스에는 로컬 및 네트워크 리소스에 대 한 액세스 권한을 결정 하는 기본 보안 id가 있습니다. Microsoft Win32 서비스의 보안 컨텍스트는 서비스를 시작 하는 데 사용 되는 서비스 계정에 의해 결정 됩니다. 서비스 계정은 다음 작업에 사용 됩니다.
* 서비스 식별 및 인증
* 서비스를 시작 했습니다.
* 코드 또는 응용 프로그램 액세스 또는 실행
* 프로세스를 시작 합니다. 

## <a name="types-of-on-premises-service-accounts"></a>온-프레미스 서비스 계정 유형

사용 사례에 따라 MSA (관리 서비스 계정), 컴퓨터 계정 또는 사용자 계정을 사용 하 여 서비스를 실행할 수 있습니다. 서비스를 테스트 하 여 관리 서비스 계정을 사용할 수 있는지 확인 해야 합니다. 가능 하면 하나를 사용 해야 합니다.

### <a name="group-msa-accounts"></a>MSA 계정 그룹화

온-프레미스 환경에서 실행 되는 서비스에 대해 가능한 경우 gmsas ( [그룹 관리 서비스 계정](service-accounts-group-managed.md) )를 사용 합니다. gMSAs는 서버 팜 또는 네트워크 부하 분산 장치 뒤에서 실행 되는 서비스에 대해 단일 id 솔루션을 제공 합니다. 단일 서버에서 실행 되는 서비스에도 사용할 수 있습니다. [gMSAs에는 충족 되어야 하는 특정 요구 사항이 있습니다.](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>독립 실행형 MSA 계정

GMSA를 사용할 수 없는 경우에는 smsa ( [독립 실행형 관리 서비스 계정](service-accounts-standalone-managed.md))를 사용 합니다. sMSAs에는 Windows Server 2008 R2 이상이 필요 합니다. GMSAs와 달리 sMSAs는 한 서버 에서만 실행 됩니다. 이러한 서버는 해당 서버의 여러 서비스에 사용할 수 있습니다.

### <a name="computer-account"></a>컴퓨터 계정

MSA를 사용할 수 없는 경우 [컴퓨터 계정을](service-accounts-computer.md)사용 하 여 조사 합니다. LocalSystem 계정은 로컬 컴퓨터에 대 한 광범위 한 권한이 있고 네트워크의 컴퓨터 id 역할을 하는 미리 정의 된 로컬 계정입니다.   
LocalSystem 계정으로 실행 되는 서비스는 <domain_name>형식으로 컴퓨터 계정의 자격 증명을 사용 하 여 네트워크 리소스에 액세스 \<computer_name> 합니다.

NT 권한 없음은 LocalSystem 계정에 대해 미리 정의 된 이름입니다. 서비스를 시작 하 고 해당 서비스에 대 한 보안 컨텍스트를 제공 하는 데 사용할 수 있습니다.

> [!NOTE]
> 컴퓨터 계정을 사용 하는 경우 컴퓨터에서 해당 계정을 사용 하는 서비스를 알 수 없으므로 어떤 서비스에서 어떤 서비스를 변경 하는지 감사할 수 없습니다. 

### <a name="user-account"></a>사용자 계정

MSA를 사용할 수 없는 경우 [사용자 계정을](service-accounts-user-on-premises.md)사용 하 여 조사 합니다. 사용자 계정은 도메인 사용자 계정 또는 로컬 사용자 계정일 수 있습니다.

도메인 사용자 계정을 사용 하면 서비스에서 Windows 및 Microsoft Active Directory Domain Services의 서비스 보안 기능을 최대한 활용할 수 있습니다. 서비스에는 계정에 대 한 로컬 및 네트워크 액세스 권한이 부여 됩니다. 또한 계정이 구성원 인 모든 그룹의 사용 권한도 갖게 됩니다. 도메인 서비스 계정은 Kerberos 상호 인증을 지원 합니다.

로컬 사용자 계정 (이름 형식: ".\UserName")은 호스트 컴퓨터의 SAM 데이터베이스에만 존재 합니다. Active Directory Domain Services에 사용자 개체가 없습니다. 로컬 계정은 도메인에서 인증할 수 없습니다. 따라서 로컬 사용자 계정의 보안 컨텍스트에서 실행 되는 서비스는 익명 사용자가 아닌 네트워크 리소스에 액세스할 수 없습니다. 로컬 사용자 컨텍스트에서 실행 되는 서비스는 해당 클라이언트에서 서비스를 인증 하는 Kerberos 상호 인증을 지원할 수 없습니다. 이러한 이유로 로컬 사용자 계정은 일반적으로 디렉터리 사용 서비스에 적합 하지 않습니다.

> [!IMPORTANT]
> 권한 있는 그룹 멤버 자격 권한을 부여 권한이 보안 위험이 될 수 있으므로 서비스 계정은 권한 있는 그룹의 멤버가 될 수 없습니다. 각 서비스에는 감사 및 보안을 위해 자체 서비스 계정이 있어야 합니다.

## <a name="choose-the-right-type-of-service-account"></a>올바른 종류의 서비스 계정 선택


| 조건| gMSA| sMSA| 컴퓨터 계정| 사용자 계정 |
| - | - | - | - | - |
| 단일 서버에서 실행 되는 앱| 예| 예. 가능 하면 gMSA 사용| 예. 가능 하면 MSA 사용| 예. 가능 하면 MSA를 사용 합니다. |
| 앱이 여러 서버에서 실행 됨| 예| 아니요| 아니요. 계정이 서버에 연결 되어 있습니다.| 예. 가능 하면 MSA를 사용 합니다. |
| 앱이 부하 분산 장치 뒤에서 실행 됨| 예| 아니요| 아니요| 예. GMSA를 사용할 수 없는 경우에만 사용 |
| Windows Server 2008 r 2에서 실행 되는 앱| 아니요| 예| 예. 가능 하면 MSA를 사용 합니다.| 예. 가능 하면 MSA를 사용 합니다. |
| Windows server 2012에서 실행| 예| 예. 가능 하면 gMSA 사용| 예. 가능 하면 MSA 사용| 예. 가능 하면 MSA를 사용 합니다. |
| 단일 서버로 서비스 계정을 제한 하기 위한 요구 사항| 아니요| 예| 예. 가능 하면 sMSA 사용| 아니요. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>서버 로그 및 PowerShell을 사용 하 여 조사

서버 로그를 사용 하 여 응용 프로그램이 실행 되는 서버 및 서버 수를 확인할 수 있습니다.

다음 PowerShell 명령을 실행 하 여 네트워크의 모든 서버에 대 한 Windows Server 버전 목록을 가져올 수 있습니다. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>온-프레미스 서비스 계정 찾기

"Svc"와 같은 접두사를 추가 하는 것이 좋습니다. 서비스 계정으로 사용 되는 모든 계정 이 명명 규칙을 사용 하면 쉽게 찾고 관리할 수 있습니다. 또한 서비스 계정 및 서비스 계정의 소유자에 대 한 설명 특성을 사용 하는 것을 고려해 야 합니다 .이 특성은 팀 별칭이 나 보안 팀 소유자 일 수 있습니다.

온-프레미스 서비스 계정을 찾는 것은 보안을 보장 하기 위한 핵심입니다. 또한 MSA가 아닌 계정에 대해서는 어려울 수 있습니다. 중요 한 온-프레미스 리소스에 대 한 액세스 권한이 있는 모든 계정을 검토 하 고 서비스 계정으로 작동할 수 있는 컴퓨터 또는 사용자 계정을 결정 하는 것이 좋습니다. 다음 방법을 사용 하 여 계정을 찾을 수도 있습니다.

* 각 계정 유형에 대 한 문서에는 해당 계정 유형을 찾기 위한 자세한 단계가 있습니다. 이러한 문서에 대 한 링크는이 문서의 다음 단계 섹션을 참조 하세요.

## <a name="document-service-accounts"></a>문서 서비스 계정

온-프레미스 환경에서 서비스 계정을 찾았으면 각 계정에 대해 다음 정보를 문서화 합니다. 

* 소유자입니다. 계정 유지 관리를 담당 하는 사람입니다.

* 용도입니다. 계정이 나타내는 응용 프로그램 또는 기타 용도입니다. 

* 사용 권한 범위. 사용 권한에는 무엇이 포함 되나요? 그룹이 속해 있으면 어떻게 되나요?

* 위험 프로필. 이 계정이 손상 된 경우 비즈니스에 미치는 위험은 무엇입니까? 위험이 높으면 MSA를 사용 합니다.

* 예상 수명 및 정기 증명 이 계정이 살고 있는 기간은 얼마나 되나요? 언제 소유자가 지속적으로 검토 하 고 증명 해야 하나요?

* 암호 보안. 사용자 및 로컬 컴퓨터 계정의 경우 암호를 저장 합니다. 암호를 안전 하 게 유지 하 고 액세스 권한이 있는 문서를 확인 합니다. [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 를 사용 하 여 저장 된 암호를 보호 하는 것이 좋습니다. 

  

## <a name="next-steps"></a>다음 단계

서비스 계정 보안 설정에 대 한 다음 문서를 참조 하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

