---
title: 컴퓨터 계정 보안 설정 | Azure Active Directory
description: 온-프레미스 컴퓨터 계정을 보호 하는 방법에 대 한 가이드입니다.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417512"
---
# <a name="securing-computer-accounts"></a>컴퓨터 계정 보안

컴퓨터 계정 (또는 LocalSystem 계정)은 로컬 컴퓨터의 거의 모든 리소스에 대 한 액세스 권한이 있는 기본 제공 되 고 높은 권한의 계정입니다. 이 계정은 로그온 한 사용자 계정과 연결 되어 있지 않습니다. LocalSystem으로 실행 되는 서비스는 원격 서버에 컴퓨터의 자격 증명을 제공 하 여 네트워크 리소스에 액세스 합니다. <domain_name>$ 형식으로 자격 증명을 제공 \<computer_name> 합니다. 컴퓨터 계정의 미리 정의 된 이름은 NT AUTHORITY\SYSTEM입니다. 서비스를 시작 하 고 해당 서비스에 대 한 보안 컨텍스트를 제공 하는 데 사용할 수 있습니다.

![[그림 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>컴퓨터 계정 사용의 이점

컴퓨터 계정은 다음과 같은 이점을 제공 합니다.

* **무제한 로컬 액세스**: 컴퓨터 계정은 컴퓨터의 로컬 리소스에 대 한 완전 한 액세스를 제공 합니다.

* **자동 암호 관리**: 컴퓨터 계정을 통해 수동으로 암호를 변경할 필요가 없습니다. 대신이 계정은 Active Directory의 구성원이 며 계정 암호가 자동으로 변경 됩니다. 또한 서비스에 대 한 서비스 사용자 이름을 등록 하지 않아도 됩니다.

* **제한 된 액세스 권한-컴퓨터**: Active Directory Domain Services의 기본 ACL (Access Control 목록)은 컴퓨터 계정에 대 한 최소 액세스를 허용 합니다. 이 서비스를 해킹 하는 경우 네트워크에 있는 리소스에만 액세스할 수 있습니다.

## <a name="assess-security-posture-of-computer-accounts"></a>컴퓨터 계정의 보안 상태 평가

컴퓨터 계정을 사용 하는 경우 잠재적인 문제 및 관련 완화 

| 문제| 해결 방법 |
| - | - |
| 컴퓨터가 도메인을 떠나는 후 다시 가입 하면 컴퓨터 계정에 삭제 및 다시 만들기가 적용 됩니다.| AD 그룹에 컴퓨터를 추가 해야 하는지 확인 하 고이 페이지에서 제공 하는 예제 스크립트를 사용 하 여 그룹에 추가 된 컴퓨터 계정을 확인 합니다.| 
| 그룹에 컴퓨터 계정을 추가 하면 해당 컴퓨터에서 LocalSystem으로 실행 되는 모든 서비스에 그룹의 액세스 권한이 부여 됩니다.| 컴퓨터 계정의 그룹 멤버 자격을 선택 합니다. 연결 된 서비스에 Active Directory Domain Services에 대 한 완전 한 액세스 권한이 있으므로 도메인 관리자 그룹의 컴퓨터 계정 구성원을 만들지 마십시오. |
| LocalSystem의 네트워크 기본값이 잘못 되었습니다.| 컴퓨터 계정에 네트워크 리소스에 대 한 기본 제한 된 액세스 권한이 있다고 가정 하지 마십시오. 대신이 계정에 대 한 그룹 멤버 자격을 신중 하 게 확인 하십시오. |
| LocalSystem으로 실행 되는 알 수 없는 서비스| LocalSystem 계정으로 실행 되는 모든 서비스가 타사의 Microsoft 서비스 또는 신뢰할 수 있는 서비스 인지 확인 합니다. |


## <a name="find-services-running-under-the-computer-account"></a>컴퓨터 계정에서 실행 되는 서비스 찾기

다음 PowerShell cmdlet을 사용 하 여 LocalSystem 컨텍스트에서 실행 중인 서비스를 찾습니다.

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**특정 그룹의 구성원 인 컴퓨터 계정 찾기**

다음 PowerShell cmdlet을 사용 하 여 특정 그룹의 구성원 인 컴퓨터 계정을 찾을 수 있습니다.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**권한 있는 그룹의 구성원 인 컴퓨터 계정 찾기**

다음 PowerShell cmdlet을 사용 하 여 Id 관리자 그룹 (Domain Admins, Enterprise Admins, Administrators)의 구성원 인 컴퓨터 계정을 찾을 수 있습니다.

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>컴퓨터 계정에서 이동

> [!IMPORTANT]
> 컴퓨터 계정은 매우 특권 수준의 계정이 며, 서비스에서 컴퓨터의 로컬 리소스에 대 한 무제한 액세스를 필요로 하는 경우에만 사용 해야 하며, MSA (관리 서비스 계정)를 사용할 수 없습니다.

* MSA를 사용 하 여 서비스를 실행할 수 있는 경우 서비스 소유자에 게 문의 하 고, 서비스에서 지 원하는 경우 gMSA (그룹 관리 서비스 계정) 또는 sMSA (독립 실행형 관리 서비스 계정)를 사용 합니다.

* 서비스를 실행 하는 데 필요한 권한만 있는 도메인 사용자 계정을 사용 합니다.

## <a name="next-steps"></a>다음 단계 

서비스 계정 보안 설정에 대 한 다음 문서를 참조 하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

 

 
