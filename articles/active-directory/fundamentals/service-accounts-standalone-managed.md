---
title: 독립 실행형 관리 서비스 계정 보안 | Azure Active Directory
description: 독립 실행형 관리 서비스 계정에 보안을 설정 하는 방법을 안내 합니다.
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
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548855"
---
# <a name="securing-standalone-managed-service-accounts"></a>독립 실행형 관리 서비스 계정 보안

SMSAs (독립 실행형 관리 서비스 계정)는 서버에서 실행 되는 하나 이상의 서비스를 보호 하는 데 사용 되는 관리 되는 도메인 계정입니다. 이러한 서버는 여러 서버에서 다시 사용할 수 없습니다. sMSAs는 자동 암호 관리, 간소화 된 SPN (서비스 사용자 이름) 관리 및 다른 관리자에 게 관리를 위임 하는 기능을 제공 합니다. 

Active Directory에서 sMSAs는 서비스를 실행 하는 특정 서버에 연결 됩니다. Microsoft Management Console의 Active Directory 사용자 및 컴퓨터 스냅인에 나열 된 이러한 계정을 찾을 수 있습니다.

![관리 서비스 계정 OU를 표시 하는 Active Directory 사용자 및 컴퓨터 스냅인의 스크린샷](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

관리 서비스 계정은 Windows Server 2008 R2 Active Directory 스키마에 도입 되었으며, Windows Server 2008 r 2의 최소 OS 수준이 필요 합니다. 

## <a name="benefits-of-using-smsas"></a>SMSAs 사용의 이점

sMSAs는 서비스 계정으로 사용 되는 사용자 계정 보다 더 높은 보안을 제공 하 고 관리 오버 헤드를 줄입니다.

* 강력한 암호를 설정 합니다. sMSAs는 240 바이트의 임의로 생성 된 복잡 한 암호를 사용 합니다. SMSA 암호의 복잡성과 길이는 무차별 암호 대입 또는 사전 공격으로 인해 서비스가 손상 될 가능성을 최소화 합니다.

* 정기적으로 암호 순환 Windows에서 자동으로 30 일 마다 sMSA 암호를 변경 합니다. 서비스 및 도메인 관리자는 암호 변경을 예약 하거나 연결 된 가동 중지 시간을 관리할 필요가 없습니다.

* SPN 관리를 간소화 합니다. 서비스 사용자 이름은 DFL (도메인 기능 수준)이 Windows Server 2008 R2 인 경우 자동으로 업데이트 됩니다. 예를 들어 서비스 사용자 이름은 다음과 같은 시나리오에서 자동으로 업데이트 됩니다.

   * 호스트 컴퓨터 계정의 이름이 변경 되었습니다. 

   * 호스트 컴퓨터의 DNS 이름이 변경 됩니다.

   * [PowerShell](/powershell/module/addsadministration/set-adserviceaccount) 을 사용 하 여 추가 sam-accountname 또는 dns 호스트 이름 매개 변수를 추가 하거나 제거 하는 경우

## <a name="when-to-use-smsas"></a>SMSAs를 사용 하는 경우

sMSAs는 관리 및 보안 작업을 간소화할 수 있습니다. 단일 서버에 하나 이상의 서비스를 배포 하 고 gMSA을 사용할 수 없는 경우 sMSAs를 사용 합니다. 

> [!NOTE] 
> 하나 이상의 서비스에 sMSAs를 사용할 수 있지만 각 서비스에는 감사 목적으로 고유한 id가 있는 것이 좋습니다. 

소프트웨어 작성자가 MSA를 사용할 수 있는지 여부를 알 수 없는 경우 응용 프로그램을 테스트 해야 합니다. 이렇게 하려면 테스트 환경을 만들고 필요한 모든 리소스에 액세스할 수 있는지 확인 합니다. 단계별 지침은 [sMSA 만들기 및 설치](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) 를 참조 하세요.

### <a name="assess-security-posture-of-smsas"></a>SMSAs의 보안 상태 평가

sMSAs는 표준 사용자 계정 보다 안전 하며, 계속 해 서 암호를 관리 해야 합니다. 그러나 전체 보안 환경의 일부로 sMSAs의 액세스 범위를 고려 하는 것이 중요 합니다.

다음 표에서는 sMSAs에서 발생 하는 잠재적인 보안 문제를 완화 하는 방법을 보여 줍니다.

| 보안 문제| 해결 방법 |
| - | - |
| sMSA는 권한 있는 그룹의 구성원입니다.|관리자 권한 그룹 (예: Domain Admins)에서 sMSA를 제거 합니다. <br> 최소 권한 모델을 사용 하 고 해당 서비스를 실행 하는 데 필요한 권한 및 사용 권한만 sMSA에 부여 합니다. <br> 필요한 사용 권한을 잘 모르겠으면 서비스 작성자에 게 문의 하세요. |
| sMSA는 중요 한 리소스에 대 한 읽기/쓰기 액세스 권한을 보유 합니다.|중요 한 리소스에 대 한 액세스를 감사 합니다. 분석을 위해 SIEM (Azure Log Analytics 또는 Azure 센티널)에 감사 로그를 보관 합니다. <br> 원치 않는 액세스 수준이 검색 되는 경우 리소스 권한을 수정 합니다. |
| 기본적으로 sMSA 암호 롤오버 빈도는 30 일입니다.| 그룹 정책은 엔터프라이즈 보안 요구 사항에 따라 기간을 조정 하는 데 사용할 수 있습니다. <br> * 다음 경로를 사용 하 여 암호 만료 기간을 설정할 수 있습니다. <br>Computer Configuration\Policies\Windows 설정 \ 보안 설정 \ Options\Domain 구성원: 최대 컴퓨터 계정 암호 사용 기간 |



### <a name="challenges-with-smsas"></a>SMSAs 문제

SMSAs와 관련 된 문제는 다음과 같습니다.

| 과제| 해결 방법 |
| - | - |
| 단일 서버에서 사용할 수 있습니다.| 서버 간에 계정을 사용 해야 하는 것 처럼 gMSAs 사용 합니다. |
| 도메인 간에는 사용할 수 없습니다.| 도메인 간에 계정을 사용 해야 하는 것 처럼 gMSAs 사용 합니다. |
| 모든 응용 프로그램이 sMSAs를 지 원하는 것은 아닙니다.| 가능 하면 gMSAs를 사용 합니다. 응용 프로그램 작성자가 권장 하는 대로 표준 사용자 계정 또는 컴퓨터 계정을 사용 하지 않는 경우 |


## <a name="find-smsas"></a>SMSAs 찾기

모든 도메인 컨트롤러에서 DSA.MSC를 실행 하 고 관리 서비스 계정 컨테이너를 확장 하 여 모든 sMSAs를 확인 합니다. 

다음 PowerShell 명령은 Active Directory 도메인에서 모든 sMSAs 및 gMSAs를 반환 합니다. 

`Get-ADServiceAccount -Filter *`

다음 명령은 Active Directory 도메인의 sMSAs만 반환 합니다.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>SMSAs 관리

SMSAs를 관리 하기 위해 다음 Active Directory PowerShell cmdlet을 사용할 수 있습니다.

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>SMSAs로 이동

응용 프로그램 서비스가 sMSA를 지원 하지만 gMSAs를 지원 하지 않고 현재 보안 컨텍스트에 대 한 사용자 계정 또는 컴퓨터 계정을 사용 하 고 있는 경우 서버에 [sMSA를 만들어 설치](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) 합니다. 

리소스를 Azure로 이동 하 고 Azure 관리 Id 또는 서비스 주체를 사용 하는 것이 가장 좋습니다.

 

## <a name="next-steps"></a>다음 단계
서비스 계정 보안 설정에 대 한 다음 문서를 참조 하세요.

* [온-프레미스 서비스 계정 소개](service-accounts-on-premises.md)

* [보안 그룹 관리 서비스 계정](service-accounts-group-managed.md)

* [독립 실행형 관리 서비스 계정 보안](service-accounts-standalone-managed.md)

* [보안 컴퓨터 계정](service-accounts-computer.md)

* [보안 사용자 계정](service-accounts-user-on-premises.md)

* [온-프레미스 서비스 계정 관리](service-accounts-govern-on-premises.md)

