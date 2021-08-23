---
title: Microsoft Graph PowerShell SDK와 Azure Active Directory Identity Protection
description: Azure Active Directory에서 Microsoft Graph의 위험 검색과 관련 정보를 쿼리하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21274636a38cb37ac62f2b73112d9346482fe573
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536312"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection과 Microsoft Graph PowerShell SDK

Microsoft Graph는 Microsoft의 통합된 API 엔드포인트이며 [Azure Active Directory ID 보호](./overview-identity-protection.md) API의 시작점입니다. 이 문서에서는 PowerShell을 사용하여 위험한 사용자의 세부 정보를 확보하기 위해 [Microsoft Graph PowerShell SDK](/graph/powershell/get-started)를 사용하는 방법을 보여 줍니다. Microsoft Graph API를 직접 쿼리하고자 하는 조직에서는 [Tutorial: Identify and remediate risks using Microsoft Graph APIs](/graph/tutorial-riskdetection-api)(자습서: Microsoft Graph API를 사용하여 위험 식별 및 수정) 문서를 참조하여 관련 과정을 시작할 수 있습니다.


## <a name="connect-to-microsoft-graph"></a>Microsoft Graph에 연결

Microsoft Graph를 통해 ID 보호 데이터에 액세스하려면 네 가지 단계가 있습니다.

- [인증서 만들기](#create-a-certificate)
- [새 앱 등록 생성](#create-a-new-app-registration)
- [API 권한 구성](#configure-api-permissions)
- [유효한 자격 증명 구성](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>인증서 만들기

프로덕션 환경에서는 프로덕션 인증 기관의 인증서를 사용하지만 이 샘플에서는 자체 서명된 인증서를 사용합니다. 다음 PowerShell 명령을 사용하여 인증서를 만들고 내보냅니다.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>새 앱 등록 생성

1. Azure Portal에서 **Azure Active Directory** > **앱 등록** 으로 이동합니다.
1. **새 등록** 을 선택합니다.
1. **생성** 페이지에서 다음 단계를 수행합니다.
   1. **이름** 텍스트 상자에 애플리케이션 이름을 입력합니다(예: Azure AD 위험 검색 API).
   1. **지원되는 계정 유형** 에서 API를 사용할 계정 유형을 선택합니다.
   1. **등록** 을 선택합니다.
1. 나중에 해당 항목이 필요하므로 **애플리케이션(클라이언트) ID** 와 **디렉터리(테넌트) ID** 를 기록해 둡니다.

### <a name="configure-api-permissions"></a>API 권한 구성

이 예제에서는 샘플을 무인 모드로 사용할 수 있도록 애플리케이션 권한을 구성합니다. 로그온 상태의 사용자에게 권한을 부여하려면 대신 위임된 권한을 선택합니다. 다른 권한 유형에 대한 자세한 내용은 [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md#permission-types) 문서에서 확인할 수 있습니다.

1. 직접 생성된 **애플리케이션** 에서 **API 권한** 을 선택합니다.
1. **구성된 권한** 페이지의 위쪽 도구 모음에서 **권한 추가** 를 클릭합니다.
1. **API 액세스 추가** 페이지에서 **API 선택** 을 클릭합니다.
1. **API 선택** 페이지에서 **Microsoft Graph** 를 선택한 다음, **선택** 을 클릭합니다.
1. **API 권한 요청** 페이지에서: 
   1. **애플리케이션 권한** 을 선택합니다.
   1. `IdentityRiskEvent.Read.All`과 `IdentityRiskyUser.Read.All` 옆의 확인란을 선택합니다.
   1. **권한 추가** 를 선택합니다.
1. **도메인에 대한 관리자 동의 권한 부여** 를 선택합니다. 

### <a name="configure-a-valid-credential"></a>유효한 자격 증명 구성

1. 직접 생성된 **애플리케이션** 에서 **인증서 및 비밀** 을 선택합니다.
1. **인증서** 에서 **인증서 업로드** 를 선택합니다.
   1. 열리는 창에서 이전에 내보낸 인증서를 선택합니다.
   1. **추가** 를 선택합니다.
1. 다음 단계에서 이 정보가 필요하므로 인증서의 **지문** 을 기록해 둡니다.

## <a name="list-risky-users-using-powershell"></a>PowerShell을 사용하여 위험 사용자 나열

Microsoft Graph를 쿼리하는 기능을 사용하도록 설정하려면 `Install-Module Microsoft.Graph` 명령을 사용하여 PowerShell 창에 `Microsoft.Graph` 모듈을 설치해야 합니다.

이전 단계에서 생성된 정보를 포함하도록 다음 변수를 수정한 다음, 변수를 전체로 실행하면 PowerShell을 사용하여 위험 사용자의 세부 정보를 확보할 수 있습니다.

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
- [자습서: Microsoft Graph API를 사용하여 위험 식별 및 수정](/graph/tutorial-riskdetection-api)
- [Microsoft Graph 개요](https://developer.microsoft.com/graph/docs)
- [사용자 없이 액세스 권한 얻기](/graph/auth-v2-service)
- [Azure AD ID 보호 서비스 루트](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory ID 보호](./overview-identity-protection.md)
