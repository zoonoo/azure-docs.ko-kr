<properties
   pageTitle="Azure AD Connect: 자동 업그레이드 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect 동기화의 기본 제공 자동 업그레이드 기능을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: 자동 업그레이드
이 기능은 빌드 1.1.105.0(2016년 2월에 발표됨)에서 도입되었습니다.

## 개요
**자동 업그레이드** 기능을 통해 아주 쉽게 Azure AD Connect 설치를 항상 최신 상태로 유지할 수 있습니다. 이 기능은 Express 설치 시 기본적으로 사용하도록 설정되며 새 버전이 발표되면 설치가 자동으로 업그레이드되도록 합니다.

다음과 같은 경우에 기본적으로 자동 업그레이드가 사용됩니다.

- Express 설정 설치
- SQL Express LocalDB(Express 설정에서 항상 사용)를 사용하는 경우
- AD 계정이 Express 설정에서 만들어지는 기본 MSOL\_ 계정인 경우
- 메타버스에 10만 개 미만의 개체가 있는 경우

자동 업그레이드의 현재 상태는 PowerShell cmdlet `Get-ADSyncAutoUpgrade`로 확인할 수 있습니다. 아래와 같은 상태가 표시됩니다.

| 시스템 상태 | 주석 |
| ---- | ---- |
| 사용 | 자동 업그레이드를 사용할 수 있습니다. |
| 일시 중단 | 시스템에서만 설정합니다. 시스템에서 더 이상 자동 업그레이드를 받을 수 없습니다. |
| 사용 안 함 | 자동 업그레이드를 사용할 수 없습니다. |

`Set-ADSyncAutoUpgrade`로 **사용**과 **사용 안 함** 사이를 전환할 수 있습니다. 시스템에서만 **일시 중단** 상태를 설정합니다.

자동 업그레이드는 Azure AD Connect Health를 업그레이드 인프라로 사용합니다. 자동 업그레이드가 올바르게 작동하도록 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에서 설명하는 대로 **Azure AD Connect Health**용 프록시에서 URL을 열었는지 확인합니다.

**Synchronization Service Manager** UI가 서버에서 실행되고 있는 경우에는 UI를 닫을 때까지 업그레이드가 일시 중단됩니다.

## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0218_2016-->