---
title: 애플리케이션 프록시 쿠키 설정 - Azure Active Directory | Microsoft Docs
description: Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 이 문서에서는 쿠키 설정을 사용 및 구성하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 656841fc8e62e81318ffd568069c0664192b1747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764896"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory에서 온-프레미스 애플리케이션에 액세스하기 위한 쿠키 설정

Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 애플리케이션 프록시 쿠키 설정을 사용하는 방법을 알아봅니다. 

## <a name="what-are-the-cookie-settings"></a>쿠키 설정이란?

[애플리케이션 프록시](application-proxy.md)는 다음 액세스 및 세션 쿠키 설정을 사용합니다.

| 쿠키 설정 | 기본값 | 설명 | 권장 사항 |
| -------------- | ------- | ----------- | --------------- |
| HTTP 전용 쿠키 사용 | ‘아니요’ | **예**는 애플리케이션 프록시가 HTTP 응답 헤더에 HTTPOnly 플래그를 포함하도록 허용합니다. 이 플래그는 추가 보안 이점을 제공합니다. 예를 들어 CSS(클라이언트 쪽 스크립팅)에서 쿠키를 복사 또는 수정하지 못하게 방지합니다.<br></br><br></br>HTTP-Only 설정을 지원 하기 전에 응용 프로그램 프록시가 보안 TLS 채널을 통해 쿠키를 암호화 하 고 전송 하 여 수정 으로부터 보호 합니다. | 추가 보안 이점이 있는 **예**를 사용하세요.<br></br><br></br>세션 쿠키에 액세스해야 하는 클라이언트 또는 사용자 에이전트에는 **아니요**를 사용하세요. 예를 들어 애플리케이션 프록시를 통해 원격 데스크톱 게이트웨이 서버에 연결하는 RDP 또는 MTSC 클라이언트에는 **아니요**를 사용합니다.|
| 보안 쿠키 사용 | ‘아니요’ | **예**를 사용하면 애플리케이션 프록시가 HTTP 응답 헤더에 보안 플래그를 포함할 수 있습니다. 보안 쿠키는 HTTPS 같은 TLS 보안 채널을 통해 쿠키를 전송하여 보안을 강화합니다. 이렇게 하면 쿠키가 일반 텍스트로 전송되기 때문에 권한 없는 사람이 쿠키를 볼 수 없습니다. | 추가 보안 이점이 있는 **예**를 사용하세요.|
| 영구적 쿠키 사용 | ‘아니요’ | **예**를 사용하면 웹 브라우저를 닫을 때 액세스 쿠키가 만료되지 않도록 애플리케이션 프록시를 설정할 수 있습니다. 액세스 토큰이 만료될 때까지 또는 사용자가 수동으로 영구적 쿠키를 삭제할 때까지 유지됩니다. | 사용자가 인증된 상태로 유지되는 보안 위험이 있으므로 **아니요**를 사용하세요.<br></br><br></br>프로세스 간에 쿠키를 공유할 수 없는 구형 애플리케이션에만 **예**를 사용하는 것이 좋습니다. 영구적 쿠키를 사용하는 대신 프로세스 간 공유 쿠키를 처리하도록 애플리케이션을 업데이트하는 것이 좋습니다. 예를 들어 사용자가 SharePoint 사이트의 탐색기 보기에서 Office 문서를 열 수 있도록 허용하려면 영구적 쿠키가 필요합니다. 영구적 쿠키를 사용하지 않는 경우 브라우저, 탐색기 프로세스 및 Office 프로세스 간에 액세스 쿠키가 공유되지 않으면 이 작업이 실패할 수 있습니다. |

## <a name="samesite-cookies"></a>SameSite 쿠키
버전 Chrome 80부터 Chromium를 활용 하는 브라우저에서 [SameSite](https://web.dev/samesite-cookies-explained) 특성을 지정 하지 않는 쿠키는 **SameSite = 느슨한**로 설정 된 것 처럼 처리 됩니다. SameSite 특성은 쿠키를 동일한 사이트 컨텍스트로 제한 하는 방법을 선언 합니다. 비 수준으로 설정 된 경우 쿠키는 동일한 사이트 요청 또는 최상위 탐색으로만 전송 됩니다. 그러나 응용 프로그램 프록시를 사용 하려면 사용자가 세션 중에 올바르게 로그인 상태를 유지 하기 위해 이러한 쿠키가 타사 컨텍스트에서 유지 되어야 합니다. 이로 인해 이러한 변경으로 인 한 영향을 피하기 위해 응용 프로그램 프록시 액세스 및 세션 쿠키를 업데이트 합니다. 업데이트는 다음과 같습니다.

* **SameSite** 특성을 **None**으로 설정 합니다. 이를 통해 응용 프로그램 프록시 액세스 및 세션 쿠키를 타사 컨텍스트에서 적절히 전송할 수 있습니다.
* **보안 쿠키 사용** 설정을 기본값으로 설정 하 여 **예** 를 기본값으로 설정 합니다. 또한 Chrome에는 보안 플래그를 지정 하는 쿠키가 필요 하거나 거부 됩니다. 이 변경 내용은 응용 프로그램 프록시를 통해 게시 된 모든 기존 응용 프로그램에 적용 됩니다. 응용 프로그램 프록시 액세스 쿠키는 항상 보안으로 설정 되 고 HTTPS를 통해서만 전송 됩니다. 이 변경 내용은 세션 쿠키에만 적용 됩니다.

응용 프로그램 프록시 쿠키에 대 한 이러한 변경 내용은 Chrome 80 릴리스 날짜 이전의 다음 몇 주 동안 진행 됩니다.

또한 백 엔드 응용 프로그램에 타사 컨텍스트에서 사용할 수 있어야 하는 쿠키가 있는 경우 이러한 쿠키에 대해 SameSite = None을 사용 하도록 응용 프로그램을 변경 하 여 명시적으로 옵트인 해야 합니다. 응용 프로그램 프록시는 Set-Cookie 헤더를 해당 URL로 변환 하 고 백 엔드 응용 프로그램에서 설정 하는 이러한 쿠키에 대 한 설정을 적용 합니다.



## <a name="set-the-cookie-settings---azure-portal"></a>쿠키 설정 - Azure Portal
Azure Portal을 사용하여 쿠키 설정을 지정하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory**   >  **Enterprise 응용 프로그램**   >  **모든 응용 프로그램**으로 이동 합니다.
3. 쿠키 설정을 사용할 애플리케이션을 선택합니다.
4. **응용 프로그램 프록시**를 클릭 합니다.
5. **추가 설정** 아래에서 쿠키 설정을 **예** 또는 **아니요**로 지정합니다.
6. **저장**을 클릭하여 변경 내용을 적용합니다. 

## <a name="view-current-cookie-settings---powershell"></a>현재 쿠키 설정 보기-PowerShell

응용 프로그램에 대 한 현재 쿠키 설정을 확인 하려면 다음 PowerShell 명령을 사용 합니다.  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>쿠키 설정 설정-PowerShell

다음 PowerShell 명령에서 ```<ObjectId>``` 는 응용 프로그램의 ObjectId입니다. 

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
