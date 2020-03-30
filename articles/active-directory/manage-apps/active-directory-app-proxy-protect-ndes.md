---
title: NDES 서버에서 AD 애플리케이션 프록시와 통합
titleSuffix: Azure Active Directory
description: NDES 서버를 보호하기 위해 Azure Active Directory 응용 프로그램 프록시 배포에 대한 지침입니다.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032258"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>네트워크 장치 등록 서비스(NDES) 서버에서 Azure AD 응용 프로그램 프록시와 통합

Azure Active Directory(AD) 응용 프로그램 프록시를 사용하면 네트워크 내에서 응용 프로그램을 게시할 수 있습니다. 이러한 응용 프로그램은 SharePoint 사이트, Microsoft Outlook 웹 앱 및 기타 웹 응용 프로그램과 같은 응용 프로그램입니다. 또한 Azure를 통해 네트워크 외부의 사용자에게 보안 액세스를 제공합니다.

Azure AD 응용 프로그램 프록시를 새로 접하고 자세히 알아보려면 [Azure AD 응용 프로그램 프록시를 통해 온-프레미스 응용 프로그램에 대한 원격 액세스를](application-proxy.md)참조하세요.

Azure AD 응용 프로그램 프록시는 Azure에 빌드됩니다. 분산 서비스 거부(DDOS) 공격 및 최상의 가용성에 대한 더 나은 보호를 위해 방대한 양의 네트워크 대역폭과 서버 인프라를 제공합니다. 또한 온-프레미스 네트워크에 외부 방화벽 포트를 열 필요가 없으며 DMZ 서버가 필요하지 않습니다. 모든 트래픽은 인바운드로 시작됩니다. 아웃바운드 포트의 전체 목록은 [자습서: Azure Active Directory의 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)참조하십시오.

> Azure AD 응용 프로그램 프록시는 Azure Active Directory의 프리미엄 또는 기본 버전을 사용하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요. 
> EMS(엔터프라이즈 모빌리티 제품군) 라이선스가 있는 경우 이 솔루션을 사용할 수 있습니다.
> Azure AD 응용 프로그램 프록시 커넥터는 Windows Server 2012 R2 이상에서만 설치됩니다. 이는 NDES 서버의 요구 사항이기도 합니다.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>NDES 서버에 커넥터 설치 및 등록

1. 애플리케이션 프록시를 사용하는 디렉터리의 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 예를 들어, 테넌트 도메인이 contoso.com이면 관리자는 admin@contoso.com 또는 해당 도메인에 있는 다른 관리자 별칭이어야 합니다.
1. 오른쪽 위 모서리에서 사용자 이름을 선택합니다. 애플리케이션 프록시를 사용하는 디렉터리에 로그인했는지 확인합니다. 디렉터리를 변경해야 할 경우 **디렉터리 전환**을 선택하고 애플리케이션 프록시를 사용하는 디렉터리를 선택합니다.
1. 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
1. **관리** 아래에서 **애플리케이션 프록시**를 선택합니다.
1. **커넥터 서비스 다운로드**를 선택합니다.

    ![커넥터 서비스를 다운로드하여 서비스 약관 확인](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. 서비스 약관을 참고하세요. 준비되면 **사용 약관 동의 및 다운로드**를 선택합니다.
1. Azure AD 응용 프로그램 프록시 커넥터 설정 파일을 NDES 서버에 복사합니다. 
   > NDES에 액세스할 수 있는 회사 네트워크 내의 모든 서버에 커넥터를 설치할 수 있습니다. NDES 서버 자체에 설치할 필요가 없습니다.
1. *AADApplication프록시커넥터 설치자 설치기와*같은 설치 파일을 실행합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치하는 동안 Azure AD 디렉터리의 응용 프로그램 프록시에 커넥터를 등록하라는 메시지가 표시됩니다.
   * Azure AD 디렉터리에서 전역 또는 응용 프로그램 관리자에 대한 자격 증명을 제공합니다. Azure AD 전역 또는 응용 프로그램 관리자 자격 증명은 포털의 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록하는 데 사용되는 전역 또는 응용 프로그램 관리자 계정은 응용 프로그램 프록시 서비스를 사용하도록 설정하는 동일한 디렉터리에 속해야 합니다.
        >
        > 예를 들어 Azure AD 도메인이 *contoso.com*경우 전역/응용 `admin@contoso.com` 프로그램 관리자는 해당 도메인의 다른 유효한 별칭이어야 합니다.

   * 커넥터를 설치하는 서버에 대해 Internet Explorer 고급 보안 구성이 켜져 있으면 등록 화면이 차단될 수 있습니다. 액세스를 허용하려면 오류 메시지의 지침을 따르거나 설치 프로세스 중에 Internet Explorer 고급 보안을 끕니다.
   * 커넥터 등록에 실패하면 [응용 프로그램 프록시 문제를 해결합니다.](application-proxy-troubleshoot.md)
1. 설정이 끝나면 아웃바운드 프록시가 있는 환경에 대한 메모가 표시됩니다. 아웃바운드 프록시를 통해 작동하도록 Azure AD 응용 프로그램 프록시 커넥터를 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`구성하려면 와 같은 제공된 스크립트를 실행합니다.
1. Azure 포털의 응용 프로그램 프록시 페이지에서 새 커넥터는 다음 예제와 같이 *활성*상태입니다.

    ![Azure 포털에서 활성으로 표시된 새 Azure AD 응용 프로그램 프록시 커넥터](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Azure AD 응용 프로그램 프록시를 통해 인증하는 응용 프로그램에 대한 고가용성을 제공하려면 여러 VM에 커넥터를 설치할 수 있습니다. 이전 섹션에 나열된 동일한 단계를 반복하여 Azure AD DS 관리 도메인에 가입한 다른 서버에 커넥터를 설치합니다.

1. 설치에 성공한 후 Azure 포털로 돌아갑니다.

1. **Enterprise 애플리케이션**을 선택합니다.

   ![올바른 이해 관계자에 참여하고 있는지 확인](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **+새 응용 프로그램을**선택한 다음 **온-프레미스 응용 프로그램을**선택합니다. 

1. 사용자 **고유의 온-프레미스 응용 프로그램 추가에서**다음 필드를 구성합니다.

   * **이름**: 응용 프로그램의 이름을 입력합니다.
   * **내부 URL**: 커넥터를 설치한 NDES 서버의 내부 URL/FQDN을 입력합니다.
   * **사전 인증**: **통과를**선택합니다. 어떤 형태의 사전 인증도 사용할 수 없습니다. 인증서 요청(SCEP)에 사용되는 프로토콜은 이러한 옵션을 제공하지 않습니다.
   * 제공된 **외부 URL을** 클립보드에 복사합니다.

1. **+Add를** 선택하여 응용 프로그램을 저장합니다.

1. 10단계에서 복사한 링크를 브라우저에 붙여넣은 다음 Azure AD 응용 프로그램 프록시를 통해 NDES 서버에 액세스할 수 있는지 테스트합니다. 기본 IIS 시작 페이지가 표시됩니다.

1. 최종 테스트로 이전 단계에서 붙여넣은 기존 URL에 *mscep.dll* 경로를 추가합니다.

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. HTTP 오류 **403 – 금지된** 응답이 표시됩니다.

1. Microsoft Intune을 통해 제공된 NDES URL을 장치로 변경하면 이 변경 사항은 Microsoft 엔드포인트 구성 센터 또는 Intune Cloud에 있을 수 있습니다.

   * 구성 센터의 경우 CRP(인증서 등록 지점)로 이동하여 URL을 조정합니다. 이 URL은 장치가 문제를 호출하고 제시하는 것입니다.
   * Intune 클라우드 전용의 경우 Intune 독립 실행형이라고도 하며 새 SCEP 정책을 편집하거나 만들고 새 URL을 추가합니다.

## <a name="next-steps"></a>다음 단계

NDES와 통합된 Azure AD 응용 프로그램 프록시를 사용하면 사용자가 액세스할 수 있는 응용 프로그램을 게시합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시를](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)참조하세요.
