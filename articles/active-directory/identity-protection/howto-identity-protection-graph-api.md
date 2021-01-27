---
title: PowerShell SDK 및 Azure Active Directory Identity Protection Microsoft Graph
description: Azure Active Directory에서 Microsoft Graph 위험 검색 및 관련 정보를 쿼리 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880239"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection 및 Microsoft Graph PowerShell SDK

Microsoft Graph는 Microsoft의 통합된 API 엔드포인트이며 [Azure Active Directory ID 보호](./overview-identity-protection.md) API의 시작점입니다. 이 문서에서는 PowerShell을 사용 하 여 위험한 사용자 정보를 얻기 위해 [Microsoft Graph POWERSHELL SDK](/graph/powershell/get-started) 를 사용 하는 방법을 보여 줍니다. Microsoft Graph Api를 직접 쿼리해야 하는 조직에서는 [자습서: Microsoft Graph api를 사용 하 여 위험을 식별 하 고](/graph/tutorial-riskdetection-api) 수정 하 여 해당 과정을 시작 합니다.


## <a name="connect-to-microsoft-graph"></a>Microsoft Graph에 연결

Microsoft Graph를 통해 ID 보호 데이터에 액세스하려면 네 가지 단계가 있습니다.

- [인증서 만들기](#create-a-certificate)
- [새 앱 등록 만들기](#create-a-new-app-registration)
- [API 권한 구성](#configure-api-permissions)
- [유효한 자격 증명 구성](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>인증서 만들기

프로덕션 환경에서는 프로덕션 인증 기관의 인증서를 사용 하지만이 샘플에서는 자체 서명 된 인증서를 사용 합니다. 다음 PowerShell 명령을 사용 하 여 인증서를 만들고 내보냅니다.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>새 앱 등록 만들기

1. Azure Portal에서 **Azure Active Directory**  >  **앱 등록** 으로 이동 합니다.
1. **새 등록** 을 선택합니다.
1. **만들기** 페이지에서 다음 단계를 수행 합니다.
   1. **이름** 텍스트 상자에 응용 프로그램의 이름을 입력 합니다 (예: Azure AD 위험 검색 API).
   1. **지원 되는 계정 유형** 에서 api를 사용 하는 계정 유형을 선택 합니다.
   1. **등록** 을 선택합니다.
1. 나중에 이러한 항목이 필요 하므로 **응용 프로그램 (클라이언트) id** 및 **디렉터리 (테 넌 트) id** 를 기록해 둡니다.

### <a name="configure-api-permissions"></a>API 권한 구성

이 예제에서는이 샘플을 무인 모드로 사용할 수 있도록 응용 프로그램 권한을 구성 합니다. 로그온 한 사용자에 게 권한을 부여 하는 경우 대신 위임 된 권한을 선택 합니다. 다른 권한 형식에 대 한 자세한 내용은 [Microsoft id 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md#permission-types)문서에서 찾을 수 있습니다.

1. 만든 **응용 프로그램** 에서 **API 권한** 을 선택 합니다.
1. 구성 된 **사용 권한** 페이지의 위쪽에 있는 도구 모음에서 **사용 권한 추가** 를 클릭 합니다.
1. **API 액세스 추가** 페이지에서 **API 선택** 을 클릭합니다.
1. **API 선택** 페이지에서 **Microsoft Graph** 를 선택한 다음, **선택** 을 클릭합니다.
1. **API 권한 요청** 페이지에서 다음을 수행 합니다. 
   1. **애플리케이션 권한** 을 선택합니다.
   1. 및 옆의 확인란을 `IdentityRiskEvent.Read.All` 선택 `IdentityRiskyUser.Read.All` 합니다.
   1. **모든 권한** 을 선택합니다.
1. **도메인에 대 한 관리자 동의 부여를 선택 합니다** . 

### <a name="configure-a-valid-credential"></a>유효한 자격 증명 구성

1. 만든 **응용 프로그램** 에서 **인증서 & 암호** 를 선택 합니다.
1. **인증서** 에서 **인증서 업로드** 를 선택 합니다.
   1. 열리는 창에서 이전에 내보낸 인증서를 선택 합니다.
   1. **추가** 를 선택합니다.
1. 다음 단계에서이 정보가 필요 하므로 인증서의 **지문을** 기록해 둡니다.

## <a name="list-risky-users-using-powershell"></a>PowerShell을 사용 하 여 위험한 사용자 나열

Microsoft Graph를 쿼리 하는 기능을 사용 하도록 설정 하려면 `Microsoft.Graph` 명령을 사용 하 여 PowerShell 창에 모듈을 설치 해야 합니다 `Install-Module Microsoft.Graph` .

이전 단계에서 생성 된 정보를 포함 하도록 다음 변수를 수정한 다음 전체로 실행 하 여 PowerShell을 사용 하 여 위험한 사용자 세부 정보를 가져옵니다.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>다음 단계

- [Microsoft Graph PowerShell SDK 시작](/graph/powershell/get-started)
- [자습서: Microsoft Graph Api를 사용 하 여 위험 식별 및 재구성](/graph/tutorial-riskdetection-api)
- [Microsoft Graph 개요](https://developer.microsoft.com/graph/docs)
- [사용자 없이 액세스 권한 얻기](/graph/auth-v2-service)
- [Azure AD ID 보호 서비스 루트](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory ID 보호](./overview-identity-protection.md)
