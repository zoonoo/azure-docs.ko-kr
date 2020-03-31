---
title: 애플리케이션 프록시 쿠키 설정 - Azure Active Directory | Microsoft Docs
description: Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 이 문서에서는 쿠키 설정을 사용 및 구성하는 방법을 알아봅니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481367"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory에서 온-프레미스 애플리케이션에 액세스하기 위한 쿠키 설정

Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 애플리케이션 프록시 쿠키 설정을 사용하는 방법을 알아봅니다. 

## <a name="what-are-the-cookie-settings"></a>쿠키 설정이란?

[애플리케이션 프록시](application-proxy.md)는 다음 액세스 및 세션 쿠키 설정을 사용합니다.

| 쿠키 설정 | 기본값 | 설명 | 권장 사항 |
| -------------- | ------- | ----------- | --------------- |
| HTTP 전용 쿠키 사용 | **아니요** | **예**는 애플리케이션 프록시가 HTTP 응답 헤더에 HTTPOnly 플래그를 포함하도록 허용합니다. 이 플래그는 추가 보안 이점을 제공합니다. 예를 들어 CSS(클라이언트 쪽 스크립팅)에서 쿠키를 복사 또는 수정하지 못하게 방지합니다.<br></br><br></br>HTTP 전용 설정을 지원하기 전에 응용 프로그램 프록시는 수정으로부터 보호하기 위해 보안 된 TLS 채널을 통해 암호화되고 전송된 쿠키입니다. | 추가 보안 이점이 있는 **예**를 사용하세요.<br></br><br></br>세션 쿠키에 액세스해야 하는 클라이언트 또는 사용자 에이전트에는 **아니요**를 사용하세요. 예를 들어 애플리케이션 프록시를 통해 원격 데스크톱 게이트웨이 서버에 연결하는 RDP 또는 MTSC 클라이언트에는 **아니요**를 사용합니다.|
| 보안 쿠키 사용 | **아니요** | **예**를 사용하면 애플리케이션 프록시가 HTTP 응답 헤더에 보안 플래그를 포함할 수 있습니다. 보안 쿠키는 HTTPS 같은 TLS 보안 채널을 통해 쿠키를 전송하여 보안을 강화합니다. 이렇게 하면 쿠키가 일반 텍스트로 전송되기 때문에 권한 없는 사람이 쿠키를 볼 수 없습니다. | 추가 보안 이점이 있는 **예**를 사용하세요.|
| 영구적 쿠키 사용 | **아니요** | **예**를 사용하면 웹 브라우저를 닫을 때 액세스 쿠키가 만료되지 않도록 애플리케이션 프록시를 설정할 수 있습니다. 액세스 토큰이 만료될 때까지 또는 사용자가 수동으로 영구적 쿠키를 삭제할 때까지 유지됩니다. | 사용자가 인증된 상태로 유지되는 보안 위험이 있으므로 **아니요**를 사용하세요.<br></br><br></br>프로세스 간에 쿠키를 공유할 수 없는 구형 애플리케이션에만 **예**를 사용하는 것이 좋습니다. 영구적 쿠키를 사용하는 대신 프로세스 간 공유 쿠키를 처리하도록 애플리케이션을 업데이트하는 것이 좋습니다. 예를 들어 사용자가 SharePoint 사이트의 탐색기 보기에서 Office 문서를 열 수 있도록 허용하려면 영구적 쿠키가 필요합니다. 영구적 쿠키를 사용하지 않는 경우 브라우저, 탐색기 프로세스 및 Office 프로세스 간에 액세스 쿠키가 공유되지 않으면 이 작업이 실패할 수 있습니다. |

## <a name="samesite-cookies"></a>동일사이트 쿠키
버전 크롬 80에서 시작하여 크롬을 활용하는 브라우저에서 [SameSite](https://web.dev/samesite-cookies-explained) 속성을 지정하지 않는 쿠키는 **SameSite=Lax로**설정된 것처럼 처리됩니다. SameSite 특성은 쿠키를 동일한 사이트 컨텍스트로 제한하는 방법을 선언합니다. Lax로 설정하면 쿠키는 동일한 사이트 요청 또는 최상위 탐색으로만 전송됩니다. 그러나 응용 프로그램 프록시는 사용자가 세션 중에 제대로 로그인된 상태로 유지하기 위해 이러한 쿠키를 타사 컨텍스트에 보존해야 합니다. 이로 인해 이 변경으로 인한 부정적인 영향을 피하기 위해 응용 프로그램 프록시 액세스 및 세션 쿠키를 업데이트하고 있습니다. 업데이트에는 다음이 포함됩니다.

* **동일사이트** 특성을 **없음으로**설정합니다. 이렇게 하면 응용 프로그램 프록시 액세스 및 세션 쿠키가 타사 컨텍스트에서 제대로 전송될 수 있습니다.
* **예를** 기본값으로 사용하도록 **보안 쿠키 사용** 설정을 설정합니다. 또한 Chrome에서는 쿠키가 보안 플래그를 지정하도록 요구하거나 거부됩니다. 이 변경 사항은 응용 프로그램 프록시를 통해 게시된 모든 기존 응용 프로그램에 적용됩니다. 응용 프로그램 프록시 액세스 쿠키는 항상 보안으로 설정되어 있으며 HTTPS를 통해서만 전송됩니다. 이 변경 사항은 세션 쿠키에만 적용됩니다.

응용 프로그램 프록시 쿠키에 대한 이러한 변경 사항은 Chrome 80 릴리스 날짜 이전에 향후 몇 주 동안 출시될 예정입니다.

또한 백 엔드 응용 프로그램에 타사 컨텍스트에서 사용할 수 있어야 하는 쿠키가 있는 경우 이러한 쿠키에 대해 SameSite=None을 사용하도록 응용 프로그램을 변경하여 명시적으로 옵트인해야 합니다. 응용 프로그램 프록시는 Set-Cookie 헤더를 해당 URL로 변환하며 백 엔드 응용 프로그램에서 설정한 이러한 쿠키의 설정을 존중합니다.



## <a name="set-the-cookie-settings---azure-portal"></a>쿠키 설정 - Azure Portal
Azure Portal을 사용하여 쿠키 설정을 지정하려면:

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 
2. Azure **Active Directory** > **엔터프라이즈 응용 프로그램으로** >이동 **모든 응용 프로그램입니다.**
3. 쿠키 설정을 사용할 애플리케이션을 선택합니다.
4. **응용 프로그램 프록시를 클릭합니다.**
5. **추가 설정** 아래에서 쿠키 설정을 **예** 또는 **아니요**로 지정합니다.
6. **저장**을 클릭하여 변경 내용을 적용합니다. 

## <a name="view-current-cookie-settings---powershell"></a>현재 쿠키 설정 보기 - PowerShell

응용 프로그램의 현재 쿠키 설정을 보려면 다음 PowerShell 명령을 사용합니다.  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>쿠키 설정 설정 - 파워쉘

다음 PowerShell ```<ObjectId>``` 명령에서는 응용 프로그램의 ObjectId입니다. 

**Http 전용 쿠키** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**보안 쿠키**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**영구 쿠키**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
