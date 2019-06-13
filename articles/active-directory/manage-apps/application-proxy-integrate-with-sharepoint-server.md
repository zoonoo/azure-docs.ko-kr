---
title: Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정 | Microsoft 문서
description: 온-프레미스 SharePoint 서버를 Azure AD 애플리케이션 프록시와 통합하는 방법에 대한 기본 사항을 다룹니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5eff7925599931104440213112ce288fd521b61
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473767"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정

이 문서에서는 온-프레미스 SharePoint 서버를 Azure AD(Azure Active Directory) 애플리케이션 프록시와 통합하는 방법을 설명합니다.

Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정하려면 단계별로 이 문서의 섹션을 따릅니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자 환경에 SharePoint 2013 이상이 이미 있다고 가정합니다. 또한 다음 필수 조건도 고려하세요.

* SharePoint에는 기본 Kerberos 지원을 포함합니다. 따라서 Azure AD 애플리케이션 프록시를 통해 원격으로 내부 사이트에 액세스하는 사용자는 SSO(Single Sign-On) 환경을 사용한다고 가정할 수 있습니다.

* 이 시나리오는 SharePoint 서버에 대한 구성 변경을 포함합니다. 스테이징 환경을 사용하는 것이 좋습니다. 이 방법에서는 스테이징 서버로 먼저 업데이트할 수 있으므로 프로덕션으로 전환하기 전에 테스트 주기를 용이하게 할 수 있습니다.

* 게시된 URL에는 SSL이 필요합니다. 또한 링크가 올바르게 전송/매핑되도록 하려면 내부 URL에 SSL이 필요합니다.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>1단계: KCD(Kerberos 제한된 위임) 구성

Windows 인증을 사용하는 온-프레미스 애플리케이션에 대해 Kerberos 인증 프로토콜 및 KCD(Kerberos 제한 위임)라는 기능을 사용하여 SSO(Single Sign-On)를 획득할 수 있습니다. KCD(구성된 경우)를 통해 애플리케이션 프록시 커넥터는 사용자가 Windows에 직접 로그인하지 않더라도 사용자에 대한 Windows 토큰을 얻을 수 있습니다. KCD에 대한 자세한 내용은 [Kerberos 제한 위임 개요](https://technet.microsoft.com/library/jj553400.aspx)를 참조하세요.

SharePoint 서버에 대해 KCD를 설정하려면 다음에 나오는 순차 섹션의 절차를 사용합니다.

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint 웹 애플리케이션이 도메인 계정으로 실행되고 있는지 확인

먼저 SharePoint 웹 애플리케이션이 로컬 시스템, 로컬 서비스 또는 네트워크 서비스가 아닌 도메인 계정으로 실행되고 있는지 확인합니다. SPN(서비스 사용자 이름)을 이 계정에 연결하려면 이 작업을 수행해야 합니다. SPN은 Kerberos 프로토콜이 서로 다른 서비스를 식별하는 방법입니다. 나중에 KCD를 구성하려면 계정이 필요합니다.

> [!NOTE]
> 서비스에 대해 이전에 생성된 Azure AD 계정이 있어야 합니다. 자동 암호 변경을 허용하는 것이 좋습니다. 전체 단계 집합 및 문제 해결에 대한 자세한 내용은 [SharePoint에서 자동 암호 변경 구성](https://technet.microsoft.com/library/ff724280.aspx)을 참조하세요.

사이트가 정의된 서비스 계정으로 실행되는지 확인하려면 다음 단계를 수행합니다.

1. **SharePoint 중앙 관리** 사이트를 엽니다.
2. **보안**으로 이동하고 **서비스 계정 구성**을 선택합니다.
3. **웹 애플리케이션 풀 – SharePoint – 80**을 선택합니다. 옵션은 기본적으로 웹 풀에서 SSL을 사용하는 경우 또는 웹 풀의 이름에 따라 약간 다를 수 있습니다.

   ![서비스 계정 구성 옵션](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. **이 구성 요소에 대한 계정을 선택하세요.** 필드가 **로컬 서비스** 또는 **네트워크 서비스**인 경우 계정을 만들어야 합니다. 그렇지 않은 경우 작업이 끝났으며 다음 섹션으로 진행하면 됩니다.
5. **새 관리되는 계정을 등록하세요.** 를 선택합니다. 계정이 생성되면 계정을 사용하기 전에 **웹 애플리케이션 풀**을 설정해야 합니다.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>SharePoint 서비스 계정에 대한 서비스 주체 이름 설정

KCD를 구성하기 전에 다음을 수행해야 합니다.

* Azure AD 프록시가 노출하는 SharePoint 웹 애플리케이션을 실행하는 도메인 계정을 식별합니다.
* Azure AD 프록시 및 SharePoint에서 구성할 내부 URL을 선택합니다. 이 내부 URL은 웹 애플리케이션에서 이미 사용된 URL이 아니어야 하며 웹 브라우저에 표시되지 않습니다.

선택한 내부 URL이 <https://sharepoint>이면 SPN은 다음과 같습니다.

```
HTTP/SharePoint
```

> [!NOTE]
> 내부 URL에 대한 다음 권장 사항을 고려하세요.
> * HTTPS 사용
> * 사용자 지정 포트 사용 안 함
> * DNS에서 별칭(CName)이 아닌 SharePoint WFE(또는 Load Balancer)를 가리키는 호스트(A) 만들기

이 SPN을 등록하려면 명령 프롬프트에서 도메인 관리자로 다음 명령을 실행합니다.

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

이 명령은 SharePoint 애플리케이션 풀 계정 _demo\spAppPoolAccount_에 대해 SPN _HTTP/SharePoint_를 설정합니다.

_HTTP/SharePoint_를 내부 URL의 SPN으로 바꾸고, _demo\spAppPoolAccount_를 환경의 애플리케이션 풀 계정으로 바꿉니다. 추가하기 전에 Setspn 명령으로 SPN을 검색합니다. 이미 있으면 **중복된 SPN 값** 오류가 표시됩니다. 이 경우 올바른 애플리케이션 풀 계정 아래에 설정되어 있지 않으면 기존 SPN을 제거하는 것이 좋습니다.

-L 옵션과 함께 Setspn 명령을 실행하여 SPN이 추가되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](https://technet.microsoft.com/library/cc731241.aspx)을 참조하세요.

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>커넥터가 SharePoint 애플리케이션 풀 계정에 추가된 SPN에 위임되도록 신뢰할 수 있는지 확인

Azure AD 애플리케이션 프록시 서비스가 사용자 ID를 SharePoint 애플리케이션 풀 계정에 위임할 수 있도록 KCD를 구성합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음 해당 서버에서 컨텍스트를 대상 애플리케이션(이 경우 SharePoint)에 전달합니다.

KCD를 구성하려면 각 커넥터 컴퓨터에 대해 다음 단계를 반복합니다.

1. 도메인 관리자로 DC에 로그인한 후 **Active Directory 사용자 및 컴퓨터**를 엽니다.
2. 커넥터가 실행 중인 컴퓨터를 찾습니다. 이 예제에서는 동일한 SharePoint 서버입니다.
3. 컴퓨터를 두 번 클릭한 후 **위임** 탭을 클릭합니다.
4. 위임 설정이 **지정된 서비스에 대한 위임용으로만 이 컴퓨터 트러스트**로 설정되어 있는지 확인합니다. 그런 다음 **모든 인증 프로토콜 사용**을 선택합니다.
5. **추가** 단추를 클릭하고 **사용자 또는 컴퓨터**를 클릭한 다음, SharePoint 애플리케이션 풀 계정(예: _demo\spAppPoolAccount_)을 찾습니다.
6. SPN 목록에서 서비스 계정에 대해 이전에 만든 SPN을 선택합니다.
7. **확인**을 클릭합니다. **확인**을 다시 클릭하여 변경 내용을 저장합니다.
  
   ![위임 설정](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>2단계: Azure AD 프록시 구성

KCD를 구성했으므로 이제 Azure AD 애플리케이션 프록시를 구성할 준비가 되었습니다.

1. 다음 설정을 사용하여 SharePoint 사이트를 게시합니다. 단계별 지침은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요.
   * **내부 URL**: **<https://SharePoint/>** 와 같은, 이전에 선택한 SharePoint 내부 URL입니다.
   * **사전 인증 방법**: Azure Active Directory
   * **헤더에서 URL 변환**: 아니요

   >[!TIP]
   >SharePoint에서는 사이트를 조회하기 위해 _호스트 헤더_ 값을 사용합니다. 이 값을 토대로 링크도 생성합니다. 이렇게 하면 SharePoint에서 생성하는 모든 링크가 외부 URL을 사용하도록 올바르게 설정된 게시된 URL입니다. 값을 **예**로 설정하는 방법으로도 커넥터에서 요청을 백 엔드 애플리케이션으로 전달할 수 있습니다. 그러나 이 값을 **아니요**로 설정하면 커넥터가 내부 호스트 이름을 보내지 않습니다. 대신 커넥터는 호스트 헤더를 게시된 URL로 백 엔드 애플리케이션에 보냅니다.

   ![SharePoint를 애플리케이션으로 게시](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. 앱이 게시된 후에는 다음 단계에 따라 Single Sign-On 설정을 구성합니다.

   1. 포털의 애플리케이션 페이지에서 **Single Sign-On**을 선택합니다.
   2. Single Sign-On 모드로 **Windows 통합 인증**을 선택합니다.
   3. 내부 애플리케이션 SPN을 이전에 설정한 값으로 설정합니다. 이 예제에서는 **HTTP/SharePoint**입니다.
   4. “위임된 로그인 ID”에서 **온-프레미스 SAM 계정 이름**을 선택합니다.

   ![SSO용 통합 Windows 인증 구성](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. 애플리케이션 설정을 완료하려면 **사용자 및 그룹** 섹션으로 이동하고 이 애플리케이션에 액세스할 사용자를 할당합니다. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>3단계: Kerberos 및 Azure AD 프록시 URL을 사용하도록 SharePoint 구성

다음 단계는 SharePoint 웹 애플리케이션을 Kerberos 및 적절한 대체 액세스 매핑으로 구성된 새 영역으로 확장하여 SharePoint에서 내부 URL로 전송된 수신 요청을 처리하고 외부 URL에 대해 빌드된 링크로 응답할 수 있도록 하는 것입니다.

1. **SharePoint 관리 셸**을 시작합니다.
2. 다음 스크립트를 실행하여 웹 애플리케이션을 엑스트라넷 영역으로 확장하고 Kerberos 인증을 사용하도록 설정합니다.

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. **SharePoint 중앙 관리** 사이트를 엽니다.
4. **시스템 설정** 아래에서 **대체 액세스 매핑 구성**을 선택합니다. 대체 액세스 매핑 상자가 열립니다.
5. 사이트(예: **SharePoint – 80**)를 선택합니다. 지금은 엑스트라넷 영역에 내부 URL이 제대로 설정되어 있지 않습니다.

   ![대체 액세스 매핑 상자](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. **내부 URL 추가**를 클릭합니다.
7. **URL 프로토콜, 호스트 및 포트** 텍스트 상자에 Azure AD 프록시에 구성된 **내부 URL**(예: <https://SharePoint/>)을 입력합니다.
8. 드롭다운 목록에서 **엑스트라넷** 영역을 선택합니다.
9. **저장**을 클릭합니다.
10. 이제 대체 액세스 매핑이 다음과 같이 표시됩니다.

    ![대체 액세스 매핑 수정](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>4단계: 엑스트라넷 영역의 IIS 사이트에 대해 HTTPS 인증서가 구성되었는지 확인

이제 SharePoint 구성이 완료되었지만 엑스트라넷 영역의 내부 URL이 <https://SharePoint/>이므로 이 사이트에 대해 인증서를 설정해야 합니다.

1. Windows PowerShell 콘솔을 엽니다.
2. 다음 스크립트를 실행하여 자체 서명된 인증서를 생성하고 컴퓨터 MY 저장소에 추가합니다.

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > 자체 서명된 인증서는 테스트 목적으로만 적합합니다. 프로덕션 환경에서는 인증 기관에서 발급한 인증서를 대신 사용하는 것이 좋습니다.

3. “인터넷 정보 서비스 관리자” 콘솔을 엽니다.
4. 트리 뷰에서 서버를 확장하고 “사이트”를 확장한 다음, “SharePoint - AAD 프록시” 사이트를 선택하고 **바인딩**을 클릭합니다.
5. https 바인딩을 선택하고 **편집...** 을 클릭합니다.
6. SSL 인증서 필드에서 **SharePoint** 인증서를 선택하고 확인을 클릭합니다.

Azure AD 애플리케이션 프록시를 통해 SharePoint 사이트를 외부에서 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)
* [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
