---
title: Azure Active Directory에 Connect Health agent 설치
description: 이 Azure AD Connect Health 문서에서는 Active Directory Federation Services (AD FS) 및 동기화를 위한 에이전트 설치에 대해 설명 합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b680c275b92340cc7efba187769cb17602b08b45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973289"
---
# <a name="azure-ad-connect-health-agent-installation"></a>에이전트 설치 Azure AD Connect Health

이 문서에서는 Azure Active Directory (Azure AD) Connect Health agent를 설치 하 고 구성 하는 방법을 알아봅니다. 에이전트를 다운로드 하려면 [다음 지침](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)을 참조 하세요.

## <a name="requirements"></a>요구 사항

다음 표에서는 Azure AD Connect Health을 사용 하기 위한 요구 사항을 나열 합니다.

| 요구 사항 | Description |
| --- | --- |
| Azure AD Premium 설치 되었습니다. |Azure AD Connect Health은 Azure AD Premium의 기능입니다. 자세한 내용은 [Azure AD Premium에 등록](../fundamentals/active-directory-get-started-premium.md)을 참조 하세요. <br /><br />30 일 무료 평가판을 시작 하려면 [평가판 시작](https://azure.microsoft.com/trial/get-started-active-directory/)을 참조 하세요. |
| Azure AD의 전역 관리자입니다. |기본적으로 전역 관리자만 상태 에이전트를 설치 및 구성 하 고, 포털에 액세스 하 고, Azure AD Connect Health 내에서 작업을 수행할 수 있습니다. 자세한 내용은 [Azure AD 디렉터리 관리](../fundamentals/active-directory-whatis.md)를 참조하세요. <br /><br /> Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 조직의 다른 사용자가 Azure AD Connect Health에 액세스할 수 있도록 허용할 수 있습니다. 자세한 내용은 [Azure AD Connect Health에 대 한 AZURE RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 참조 하세요. <br /><br />**중요**: 회사 또는 학교 계정을 사용 하 여 에이전트를 설치 합니다. Microsoft 계정를 사용할 수 없습니다. 자세한 내용은 [조직으로 Azure 등록](../fundamentals/sign-up-organization.md)을 참조 하세요. |
| Azure AD Connect Health 에이전트는 각 대상 서버에 설치 됩니다. | 상태 에이전트는 데이터를 수신 하 고 모니터링 및 분석 기능을 제공할 수 있도록 대상 서버에 설치 되 고 구성 되어야 합니다. <br /><br />예를 들어 Active Directory Federation Services (AD FS) 인프라에서 데이터를 가져오려면 AD FS 서버와 웹 응용 프로그램 프록시 서버에 에이전트를 설치 해야 합니다. 마찬가지로 온-프레미스 Azure AD Domain Services (Azure AD DS) 인프라에서 데이터를 가져오려면 도메인 컨트롤러에 에이전트를 설치 해야 합니다.  |
| Azure 서비스 끝점에는 아웃 바운드 연결이 있습니다. | 에이전트는 설치 및 런타임 중에 Azure AD Connect Health 서비스 엔드포인트에 연결되어야 합니다. 방화벽이 아웃 바운드 연결을 차단 하는 경우에는 [아웃 바운드 연결 끝점](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) 을 허용 목록에 추가 합니다. |
|아웃 바운드 연결은 IP 주소를 기반으로 합니다. | IP 주소를 기반으로 하는 방화벽 필터링에 대 한 자세한 내용은 [AZURE ip 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요.|
| 아웃 바운드 트래픽에 대 한 TLS 검사가 필터링 되거나 사용 되지 않습니다. | 네트워크 계층에서 아웃 바운드 트래픽에 대 한 TLS 검사 또는 종료가 있으면 에이전트 등록 단계 또는 데이터 업로드 작업이 실패할 수 있습니다. 자세한 내용은 [TLS 검사 설정](/previous-versions/tn-archive/ee796230(v=technet.10))을 참조 하세요. |
| 서버의 방화벽 포트에서 에이전트가 실행 되 고 있습니다. |에이전트는 Azure AD Connect Health 서비스 끝점과 통신할 수 있도록 다음 방화벽 포트가 열려 있어야 합니다. <br /><li>TCP 포트 443</li><li>TCP 포트 5671</li> <br />최신 버전의 에이전트에는 포트 5671이 필요 하지 않습니다. 포트 443만 필요 하도록 최신 버전으로 업그레이드 합니다. 자세한 내용은 [포트 및 프로토콜이 필요한 하이브리드 id](./reference-connect-ports.md)를 참조 하세요. |
| Internet Explorer 보안 강화를 사용 하는 경우 지정 된 웹 사이트를 허용 합니다.  |Internet Explorer 보안 강화를 사용 하는 경우 에이전트를 설치 하는 서버에서 다음 웹 사이트를 허용 합니다.<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>Azure AD에서 신뢰할 수 있는 조직의 페더레이션 서버 (예: https: \/ /sts.contoso.com)</li> <br />자세한 내용은 [Internet Explorer를 구성 하는 방법](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)을 참조 하세요. 네트워크에 프록시가 있는 경우이 표 끝에 표시 되는 정보를 참조 하세요.|
| PowerShell 버전 4.0 이상이 설치 되어 있습니다. | Windows Server 2012에는 PowerShell 버전 3.0이 포함 되어 있습니다. 이 버전은 에이전트에 충분 *하지 않습니다* .</br></br> Windows Server 2012 R2 이상에는 PowerShell의 최신 버전이 포함 되어 있습니다.|
|FIPS (FIPS(Federal Information Processing Standard))를 사용할 수 없습니다.|Azure AD Connect Health 에이전트는 FIPS를 지원 하지 않습니다.|

> [!IMPORTANT]
> Windows Server Core는 Azure AD Connect Health 에이전트 설치를 지원 하지 않습니다.


> [!NOTE]
> 매우 잠금 및 제한 된 환경을 사용할 경우에는 테이블이 Internet Explorer 보안 강화를 위해 나열 하는 것 보다 많은 Url을 추가 해야 합니다. 다음 섹션의 표에 나와 있는 Url도 추가 합니다.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure 서비스 엔드포인트에 대한 아웃바운드 연결

설치 및 런타임 중에 에이전트는 Azure AD Connect Health 서비스 끝점에 연결 되어야 합니다. 방화벽이 아웃 바운드 연결을 차단 하는 경우에는 다음 표에 나와 있는 Url이 기본적으로 차단 되지 않아야 합니다. 

이러한 Url의 보안 모니터링 또는 검사를 사용 하지 않도록 설정 하지 마세요. 대신 다른 인터넷 트래픽을 허용 하는 것으로 허용 합니다. 

이러한 Url을 통해 Azure AD Connect Health 서비스 끝점과 통신할 수 있습니다. 이 문서의 뒷부분에서는를 사용 하 여 [아웃 바운드 연결을 확인](#test-connectivity-to-azure-ad-connect-health-service) 하는 방법을 알아봅니다 `Test-AzureADConnectHealthConnectivity` .

| 도메인 환경 | 필수 Azure 서비스 엔드포인트 |
| --- | --- |
| 일반 공용 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42; servicebus.windows.net: 5671 (이 끝점은 최신 버전의 에이전트에서 필요 하지 않습니다.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.office.com (이 끝점은 등록 중에 검색 목적 으로만 사용 됩니다.)</li> |
| Azure 독일 | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.office.de (이 끝점은 등록 중에 검색 목적 으로만 사용 됩니다.)</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.office.com (이 끝점은 등록 중에 검색 목적 으로만 사용 됩니다.)</li> |


## <a name="install-the-agent"></a>에이전트 설치

Azure AD Connect Health 에이전트를 다운로드 하 여 설치 하려면 다음을 수행 합니다. 

* Azure AD Connect Health에 대 한 [요구 사항을](how-to-connect-health-agent-install.md#requirements) 충족 하는지 확인 합니다.
* AD FS에 대 한 Azure AD Connect Health 사용 시작:
    * [AD FS에 대 한 Azure AD Connect Health 에이전트를 다운로드](https://go.microsoft.com/fwlink/?LinkID=518973)합니다.
    * [설치 지침](#install-the-agent-for-ad-fs)을 참조 하세요.
* 동기화를 위해 Azure AD Connect Health 사용 시작:
    * [최신 버전의 Azure AD Connect 다운로드 및 설치](https://go.microsoft.com/fwlink/?linkid=615771). 동기화에 대 한 상태 에이전트는 Azure AD Connect 설치 (버전 1.0.9125.0 이상)의 일부로 설치 됩니다.
* Azure AD DS에 대 한 Azure AD Connect Health 사용 시작:
    * [Azure AD DS에 대 한 Azure AD Connect Health 에이전트를 다운로드](https://go.microsoft.com/fwlink/?LinkID=820540)합니다.
    * [설치 지침](#install-the-agent-for-azure-ad-ds)을 참조 하세요.

## <a name="install-the-agent-for-ad-fs"></a>AD FS 용 에이전트를 설치 합니다.

> [!NOTE]
> AD FS 서버는 동기화 서버와 달라 야 합니다. 동기화 서버에 AD FS 에이전트를 설치 하지 마세요.
>

에이전트를 설치 하기 전에 AD FS 서버 호스트 이름이 고유 하 고 AD FS 서비스에 존재 하지 않는지 확인 합니다.
에이전트 설치를 시작 하려면 다운로드 한 *.exe* 파일을 두 번 클릭 합니다. 첫 번째 창에서 **설치** 를 선택 합니다.

![Azure AD Connect Health AD FS 에이전트의 설치 창을 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/install1.png)

설치가 완료 되 면 **지금 구성** 을 선택 합니다.

![Azure AD Connect Health AD FS 에이전트 설치에 대 한 확인 메시지를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/install2.png)

PowerShell 창이 열리고 에이전트 등록 프로세스가 시작 됩니다. 메시지가 표시 되 면 에이전트를 등록할 수 있는 권한이 있는 Azure AD 계정을 사용 하 여 로그인 합니다. 기본적으로 전역 관리자 계정에는 권한이 있습니다.

![Azure AD Connect Health AD FS의 로그인 창을 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/install3.png)

로그인 하면 PowerShell이 계속 됩니다. 완료 되 면 PowerShell을 닫을 수 있습니다. 구성이 완료 되었습니다.

이 시점에서 에이전트가 필요한 데이터를 클라우드 서비스에 안전 하 게 업로드할 수 있도록 에이전트 서비스가 자동으로 시작 됩니다.

모든 필수 구성 요소를 충족 하지 않은 경우에는 경고가 PowerShell 창에 표시 됩니다. 에이전트를 설치 하기 전에 [요구 사항을](how-to-connect-health-agent-install.md#requirements) 완료 해야 합니다. 다음 스크린샷에서는 이러한 경고의 예를 보여 줍니다.

![스크립트 구성 AD FS Azure AD Connect Health을 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/install4.png)

에이전트가 설치 되어 있는지 확인 하려면 서버에서 다음 서비스를 찾으십시오. 구성을 완료했다면 해당 서비스가 실행 중이어야 합니다. 그렇지 않으면 구성이 완료 될 때까지 중지 됩니다.

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health AD FS 서비스를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>AD FS에 대 한 감사 사용

> [!NOTE]
> 이 섹션은 AD FS 서버에만 적용 됩니다. 웹 응용 프로그램 프록시 서버에서는 이러한 단계를 수행할 필요가 없습니다.
>

사용 현황 분석 기능은 데이터를 수집 하 고 분석 해야 합니다. 따라서 Azure AD Connect Health 에이전트에는 AD FS 감사 로그의 정보가 필요 합니다. 이러한 로그는 기본적으로 사용 하도록 설정 되어 있지 않습니다. AD FS 감사를 사용 하도록 설정 하 고 AD FS 서버에서 AD FS 감사 로그를 찾으려면 다음 절차를 따르십시오.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자** 를 연 후 **로컬 보안 정책** 을 엽니다. 또는 작업 표시줄에서 **서버 관리자** 를 연 후 **도구/로컬 보안 정책** 을 선택 합니다.
2. *보안 정책 \ 로컬 정책 \ 보안 정책 \ 보안 정책* 그런 다음 **보안 감사 생성** 을 두 번 클릭 합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 나열 되지 않으면 **사용자 또는 그룹 추가** 를 선택 하 고 목록에 추가 합니다. 그런 다음, **확인** 을 선택합니다.
4. 감사를 사용 하려면 상승 된 권한으로 명령 프롬프트 창을 엽니다. 그런 후 다음 명령을 실행합니다. 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **로컬 보안 정책** 을 닫습니다.
    >[!Important]
    >다음 단계는 기본 AD FS 서버에만 필요 합니다. 
1. **AD FS 관리** 스냅인을 엽니다. **서버 관리자** 에서 **도구**  >  를 선택 합니다. **AD FS 관리**
1. **작업** 창에서 **편집 페더레이션 서비스 속성** 을 선택 합니다.
1. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 선택 합니다.
1. **성공 감사 및 실패 감사** 확인란을 선택 하 고 **확인** 을 선택 합니다.
1. PowerShell을 통해 자세한 정보 로깅을 사용 하도록 설정 하려면 다음 명령을 사용 합니다. 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016에서 AD FS에 대해 감사를 사용하도록 설정하려면

1. 시작 화면에서 **서버 관리자** 를 연 후 **로컬 보안 정책** 을 엽니다. 또는 작업 표시줄에서 **서버 관리자** 를 연 후 **도구/로컬 보안 정책** 을 선택 합니다.
2. *보안 정책 \ 로컬 정책 \ 보안 정책 \ 보안 정책 \* 보안 감사 폴더로 이동한 다음 **보안 감사 생성** 을 두 번 클릭 합니다.
3. **로컬 보안 설정** 탭에서 AD FS 서비스 계정이 목록에 있는지 확인합니다. 나열 되지 않으면 **사용자 또는 그룹 추가** 를 선택 하 고 목록에 AD FS 서비스 계정을 추가 합니다. 그런 다음, **확인** 을 선택합니다.
4. 감사를 사용 하려면 상승 된 권한으로 명령 프롬프트 창을 엽니다. 그런 후 다음 명령을 실행합니다. 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **로컬 보안 정책** 을 닫습니다.
    >[!Important]
    >다음 단계는 기본 AD FS 서버에만 필요 합니다.
1. **AD FS 관리** 스냅인을 엽니다. **서버 관리자** 에서 **도구**  >  를 선택 합니다. **AD FS 관리**
1. **작업** 창에서 **편집 페더레이션 서비스 속성** 을 선택 합니다.
1. **페더레이션 서비스 속성** 대화 상자에서 **이벤트** 탭을 선택 합니다.
1. **성공 감사 및 실패 감사** 확인란을 선택 하 고 **확인** 을 선택 합니다. 성공 감사 및 실패 감사는 기본적으로 사용 하도록 설정 되어야 합니다.
1. PowerShell 창을 열고 다음 명령을 실행 합니다. 

    `Set-AdfsProperties -AuditLevel Verbose`

기본적으로 "기본" 감사 수준이 사용 됩니다. 자세한 내용은 [Windows Server 2016의 AD FS 감사 기능 향상](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)을 참조 하세요.


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS 감사 로그를 찾으려면

1. **이벤트 뷰어** 를 엽니다.
2. **Windows 로그** 로 이동한 다음 **보안** 을 선택 합니다.
3. 오른쪽에서 **현재 로그 필터링** 을 선택 합니다.
4. **이벤트 원본** 에서 **AD FS 감사** 를 선택 합니다.

    감사 로그에 대 한 자세한 내용은 [작업 질문](reference-connect-health-faq.md#operations-questions)을 참조 하세요.

    ![현재 로그 필터링 창을 보여 주는 스크린샷 "이벤트 원본" 필드에서 "AD FS 감사"를 선택 합니다.](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> 그룹 정책은 AD FS 감사를 사용하지 않도록 설정할 수 있습니다. AD FS 감사가 사용 하지 않도록 설정 된 경우 로그인 활동에 대 한 사용 현황 분석을 사용할 수 없습니다. AD FS 감사를 사용 하지 않도록 설정 하는 그룹 정책이 없는지 확인 합니다.
>


## <a name="install-the-agent-for-sync"></a>동기화를 위한 에이전트 설치

동기화에 대 한 Azure AD Connect Health 에이전트는 최신 버전의 Azure AD Connect에 자동으로 설치 됩니다. 동기화를 위해 Azure AD Connect를 사용 하려면 [최신 버전의 Azure AD Connect를 다운로드](https://www.microsoft.com/download/details.aspx?id=47594) 하 여 설치 합니다.

에이전트가 설치되었는지 확인하려면 서버에서 다음 서비스를 살펴봅니다. 구성을 완료 한 경우 서비스가 이미 실행 중 이어야 합니다. 그렇지 않으면 구성이 완료 될 때까지 서비스가 중지 됩니다.

* Azure AD Connect Health Sync Insights Service
* Azure AD Connect Health Sync Monitoring Service

![서버의 동기화 서비스에 대해 실행 중인 Azure AD Connect Health를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health를 사용 하려면 Azure AD Premium 있어야 합니다. Azure AD Premium 없는 경우 Azure Portal에서 구성을 완료할 수 없습니다. 자세한 내용은 [요구 사항](how-to-connect-health-agent-install.md#requirements)을 참조 하세요.
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>동기화를 위해 수동으로 Azure AD Connect Health 등록

Azure AD Connect를 성공적으로 설치한 후에 동기화 에이전트 등록에 대 한 Azure AD Connect Health 실패 하면 PowerShell 명령을 사용 하 여 수동으로 에이전트를 등록할 수 있습니다.

> [!IMPORTANT]
> Azure AD Connect를 설치한 후 에이전트 등록에 실패 하는 경우에만이 PowerShell 명령을 사용 합니다.
>
>

다음 PowerShell 명령을 사용 하 여 동기화에 대 한 Azure AD Connect Health 에이전트를 수동으로 등록 합니다. Azure AD Connect Health 서비스는 에이전트가 성공적으로 등록된 후에 시작됩니다.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

이 명령은 다음 매개 변수를 사용합니다.

* **Attributefiltering**: `$true` (기본값) Azure AD Connect 기본 특성 집합을 동기화 하지 않으며 필터링 된 특성 집합을 사용 하도록 사용자 지정 되었습니다. 그렇지 않으면 `$false`를 사용합니다.
* **StagingMode**: `$false` Azure AD Connect 서버가 준비 모드에 *있지 않은* 경우에는 (기본값)입니다. 서버가 준비 모드에 있도록 구성 된 경우를 사용 `$true` 합니다.

인증 하 라는 메시지가 표시 되 면 admin@domain.onmicrosoft.com Azure AD Connect를 구성 하는 데 사용한 것과 동일한 전역 관리자 계정 (예:)을 사용 합니다.

## <a name="install-the-agent-for-azure-ad-ds"></a>Azure AD DS에 대 한 에이전트 설치

에이전트 설치를 시작 하려면 다운로드 한 *.exe* 파일을 두 번 클릭 합니다. 첫 번째 창에서 **설치** 를 선택 합니다.

![AD DS 설치 창의 Azure AD Connect Health 에이전트를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

설치가 완료 되 면 **지금 구성** 을 선택 합니다.

![Azure AD DS에 대 한 Azure AD Connect Health 에이전트의 설치를 완료 하는 창을 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

명령 프롬프트 창이 열립니다. PowerShell 실행 `Register-AzureADConnectHealthADDSAgent` 메시지가 표시 되 면 Azure에 로그인 합니다.

![Azure AD DS에 대 한 Azure AD Connect Health 에이전트의 로그인 창을 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

로그인 하면 PowerShell이 계속 됩니다. 완료 되 면 PowerShell을 닫을 수 있습니다. 구성이 완료 되었습니다.

이 시점에서 서비스를 자동으로 시작 하 여 에이전트가 데이터를 모니터링 하 고 수집할 수 있도록 합니다. 이전 섹션에 설명 된 모든 필수 구성 요소를 충족 하지 않은 경우에는 경고가 PowerShell 창에 표시 됩니다. 에이전트를 설치 하기 전에 [요구 사항을](how-to-connect-health-agent-install.md#requirements) 완료 해야 합니다. 다음 스크린샷에서는 이러한 경고의 예를 보여 줍니다.

![Azure AD DS 구성에 대 한 Azure AD Connect Health 에이전트에 대 한 경고를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

에이전트가 설치 되어 있는지 확인 하려면 도메인 컨트롤러에서 다음 서비스를 찾습니다.

* Azure AD Connect Health AD DS Insights 서비스
* Azure AD Connect Health AD DS 모니터링 서비스

구성을 완료했다면 이러한 서비스가 이미 실행 중이어야 합니다. 그렇지 않으면 구성이 완료 될 때까지 중지 됩니다.

![도메인 컨트롤러에서 실행 중인 서비스를 보여 주는 스크린샷](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>여러 서버에 에이전트를 빠르게 설치

1. Azure AD에서 사용자 계정을 만듭니다. 암호를 사용 하 여 보안을 유지 합니다.
2. 포털을 사용 하 여 Azure AD Connect Health에서이 로컬 Azure AD 계정에 대 한 **소유자** 역할을 할당 합니다. [이러한 단계](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 수행합니다. 모든 서비스 인스턴스에 역할을 할당 합니다. 
3. 설치를 위해 로컬 도메인 컨트롤러에서 *.Exe* MSI 파일을 다운로드 합니다.
4. 다음 스크립트를 실행합니다. 매개 변수를 새 사용자 계정 및 암호로 바꿉니다. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

완료 되 면 다음 작업 중 하나 이상을 수행 하 여 로컬 계정에 대 한 액세스 권한을 제거할 수 있습니다. 
* Azure AD Connect Health에 대 한 로컬 계정에 대 한 역할 할당을 제거 합니다.
* 로컬 계정에 대 한 암호를 회전 합니다. 
* Azure AD 로컬 계정을 사용 하지 않도록 설정 합니다.
* Azure AD 로컬 계정을 삭제 합니다.  

## <a name="register-the-agent-by-using-powershell"></a>PowerShell을 사용 하 여 에이전트 등록

적절 한 에이전트 *setup.exe* 파일을 설치한 후에는 역할에 따라 다음 PowerShell 명령을 사용 하 여 에이전트를 등록할 수 있습니다. PowerShell 창을 열고 적절 한 명령을 실행 합니다.

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

이러한 명령은 `Credential` 등록을 비 대화형으로 완료 하거나 Server Core를 실행 하는 컴퓨터에서 등록을 완료 하는 매개 변수로 받아들입니다. 다음 사항에 유의하세요.
* `Credential`매개 변수로 전달 된 PowerShell 변수에서 캡처할 수 있습니다.
* 에이전트를 등록할 수 있는 권한이 있는 Azure AD id를 제공할 수 있으며 다단계 인증을 사용 하도록 설정할 *수 없습니다.*
* 기본적으로 전역 관리자는 에이전트를 등록할 수 있는 권한을 가집니다. 이 단계를 수행 하는 데 권한이 적은 id를 허용할 수도 있습니다. 자세한 내용은 [AZURE RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac)를 참조 하세요.

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>HTTP 프록시를 사용 하도록 Azure AD Connect Health 에이전트 구성

HTTP 프록시를 사용 하도록 Azure AD Connect Health 에이전트를 구성할 수 있습니다.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress`은 지원되지 않습니다. 에이전트는 Windows HTTP 서비스 대신 System.Net를 사용 하 여 웹 요청을 만듭니다.
> * 구성 된 HTTP 프록시 주소는 암호화 된 HTTPS 메시지를 통과 하는 데 사용 됩니다.
> * 인증된 프록시(HTTPBasic 사용)는 지원되지 않습니다.
>
>

### <a name="change-the-agent-proxy-configuration"></a>에이전트 프록시 구성 변경

HTTP 프록시를 사용 하도록 Azure AD Connect Health 에이전트를 구성 하려면 다음을 수행할 수 있습니다.
* 기존 프록시 설정을 가져옵니다.
* 프록시 주소를 수동으로 지정 하십시오.
* 기존 프록시 구성의 선택을 취소 합니다.

> [!NOTE]
> 프록시 설정을 업데이트 하려면 모든 Azure AD Connect Health 에이전트 서비스를 다시 시작 해야 합니다. 다음 명령 실행:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>기존 프록시 설정 가져오기

Azure AD Connect Health 에이전트가 설정을 사용할 수 있도록 Internet Explorer HTTP 프록시 설정을 가져올 수 있습니다. 상태 에이전트를 실행 하는 각 서버에서 다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Azure AD Connect Health 에이전트에서 사용할 수 있도록 WinHTTP 프록시 설정을 가져올 수 있습니다. 상태 에이전트를 실행 하는 각 서버에서 다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>수동으로 프록시 주소 지정

프록시 서버를 수동으로 지정할 수 있습니다. 상태 에이전트를 실행 하는 각 서버에서 다음 PowerShell 명령을 실행 합니다.

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

예를 들면 다음과 같습니다. 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

이 예제에 대한 설명:
* `address`설정은 DNS가 확인할 수 있는 서버 이름 또는 IPv4 주소일 수 있습니다.
* 를 생략할 수 있습니다 `port` . 이렇게 하면 443가 기본 포트입니다.

#### <a name="clear-the-existing-proxy-configuration"></a>기존 프록시 구성 지우기

다음 명령을 실행하여 기존 프록시 구성을 지울 수 있습니다.

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>현재 프록시 설정 읽기

다음 명령을 실행 하 여 현재 프록시 설정을 읽을 수 있습니다.

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에 대 한 연결 테스트

경우에 따라 Azure AD Connect Health 에이전트가 Azure AD Connect Health 서비스와의 연결이 끊어질 수 있습니다. 이러한 연결 손실의 원인에는 네트워크 문제, 사용 권한 문제 및 기타 여러 가지 문제가 있을 수 있습니다.

에이전트가 2 시간 이상 Azure AD Connect Health 서비스로 데이터를 보낼 수 없는 경우 포털에 "상태 관리 서비스 데이터가 최신 상태가 아닙니다." 경고가 표시 됩니다. 

다음 PowerShell 명령을 실행 하 여 영향을 받는 Azure AD Connect Health 에이전트가 Azure AD Connect Health 서비스에 데이터를 업로드할 수 있는지 여부를 확인할 수 있습니다.

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

역할 매개 변수가 현재 다음 값을 사용합니다.

* ADFS
* 동기화
* ADDS

> [!NOTE]
> 연결 도구를 사용 하려면 먼저 에이전트를 등록 해야 합니다. 에이전트 등록을 완료할 수 없는 경우 Azure AD Connect Health에 대 한 모든 [요구 사항을](how-to-connect-health-agent-install.md#requirements) 충족 하는지 확인 합니다. 연결은 에이전트를 등록 하는 동안 기본적으로 테스트 됩니다.
>
>

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 확인 하세요.

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health 작업](how-to-connect-health-operations.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [동기화에 대한 Azure AD Connect Health 사용](how-to-connect-health-sync.md)
* [Azure AD DS에서 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)
