---
title: 컴퓨터 계정 보안 | Azure Active Directory
description: 온-프레미스 컴퓨터 계정 보안 지침
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417512"
---
# <a name="securing-computer-accounts"></a>컴퓨터 계정 보안

컴퓨터 계정(또는 LocalSystem 계정)은 로컬 컴퓨터의 거의 모든 리소스에 액세스할 수 있는 높은 권한의 기본 제공 계정입니다. 이 계정은 로그온한 사용자 계정과 연결되어 있지 않습니다. LocalSystem으로 실행되는 서비스는 원격 서버에 컴퓨터의 자격 증명을 제공하여 네트워크 리소스에 액세스합니다. 자격 증명을 <domain_name>\<computer_name>$ 형식으로 제공합니다. 컴퓨터 계정의 미리 정의된 이름은 NT AUTHORITY\SYSTEM입니다. 이 계정은 서비스를 시작하고 해당 서비스에 대한 보안 컨텍스트를 제공하는 데 사용할 수 있습니다.

![[그림 4](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>컴퓨터 계정을 사용하는 이점

컴퓨터 계정은 다음과 같은 이점을 제공합니다.

* **무제한 로컬 액세스**: 컴퓨터 계정은 컴퓨터의 로컬 리소스에 대한 완전한 액세스를 제공합니다.

* **자동 암호 관리**: 컴퓨터 계정을 사용하면 수동으로 암호를 변경할 필요가 없습니다. 대신, 이 계정은 Active Directory의 멤버이며 계정 암호가 자동으로 변경됩니다. 또한 서비스의 서비스 사용자 이름을 등록하지 않아도 됩니다.

* **제한된 컴퓨터 외부 액세스 권한**: Active Directory Domain Services의 기본 ACL(액세스 제어 목록)은 컴퓨터 계정에 최소한의 액세스를 허용합니다. 이 서비스가 해킹되는 경우 네트워크에 있는 리소스에만 액세스할 수 있습니다.

## <a name="assess-security-posture-of-computer-accounts"></a>컴퓨터 계정의 보안 태세 평가

컴퓨터 계정을 사용하는 경우 잠재적 문제 및 관련 완화 방법 

| 문제| 해결 방법 |
| - | - |
| 컴퓨터가 도메인에서 탈퇴 후 다시 참가하면 컴퓨터 계정은 삭제 후 다시 만들어집니다.| AD 그룹에 컴퓨터를 추가해야 하는지 확인하고 이 페이지에서 제공하는 예제 스크립트를 사용하여 그룹에 추가된 컴퓨터 계정을 확인합니다.| 
| 그룹에 컴퓨터 계정을 추가하면 해당 컴퓨터에서 LocalSystem으로 실행되는 모든 서비스에 그룹의 액세스 권한이 부여됩니다.| 컴퓨터 계정의 그룹 멤버 자격은 신중히 선택해야 합니다. 연결된 서비스에 Active Directory Domain Services에 대한 완전한 액세스 권한이 있으므로 컴퓨터 계정을 도메인 관리자 그룹의 멤버로 만들지 마세요. |
| LocalSystem의 잘못된 네트워크 기본값| 컴퓨터 계정에 네트워크 리소스에 대한 제한된 기본 액세스 권한이 있다고 가정하지 마세요. 대신, 이 계정에 대한 그룹 멤버 자격을 신중하게 확인해야 합니다. |
| LocalSystem으로 실행되는 알 수 없는 서비스| LocalSystem 계정에서 실행되는 모든 서비스가 Microsoft 서비스 또는 타사의 신뢰할 수 있는 서비스인지 확인하세요. |


## <a name="find-services-running-under-the-computer-account"></a>컴퓨터 계정에서 실행되는 서비스 찾기

다음 PowerShell cmdlet을 사용하여 LocalSystem 컨텍스트에서 실행 중인 서비스를 찾습니다.

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**특정 그룹의 멤버인 컴퓨터 계정 찾기**

다음 PowerShell cmdlet을 사용하여 특정 그룹의 멤버인 컴퓨터 계정을 찾을 수 있습니다.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**권한 있는 그룹의 멤버인 컴퓨터 계정 찾기**

다음 PowerShell cmdlet을 사용하여 ID 관리자 그룹(도메인 관리자, 엔터프라이즈 관리자, 관리자)의 멤버인 컴퓨터 계정을 찾을 수 있습니다.

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>컴퓨터 계정에서 이동

> [!IMPORTANT]
> 컴퓨터 계정은 높은 권한의 계정이므로 서비스에서 컴퓨터의 로컬 리소스에 대한 무제한 액세스를 필요로 하는 경우에만 사용해야 하며, MSA(관리 서비스 계정)를 사용할 수 없습니다.

* MSA를 사용하여 서비스를 실행할 수 있는 경우 서비스 소유자에게 문의하고, 서비스에서 지원하는 경우 gMSA(그룹 관리 서비스 계정) 또는 sMSA(독립 실행형 관리 서비스 계정)를 사용합니다.

* 서비스를 실행하는 데 필요한 권한만 있는 도메인 사용자 계정을 사용합니다.

## <a name="next-steps"></a>다음 단계 

서비스 계정 보안에 대해서는 다음 문서를 참조하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [그룹 관리 서비스 계정 보안](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [컴퓨터 계정 보안](service-accounts-computer.md)

* [사용자 계정 보안](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

 

 
