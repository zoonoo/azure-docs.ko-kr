---
title: Azure AD에서 Azure AD Connect 클라우드 프로비전의 필수 구성 조건
description: 이 문서에서는 클라우드 프로비저닝에 필요한 필수 구성 조건 및 하드웨어 요구 사항에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332077"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 클라우드 프로비전의 필수 구성 조건
이 문서에서는 Azure Active Directory(Azure AD) Connect 클라우드 프로비전을 ID 솔루션으로 선택하고 사용하는 방법에 대한 지침을 제공합니다.



## <a name="cloud-provisioning-agent-requirements"></a>클라우드 프로비저닝 에이전트 요구 사항
Azure AD Connect 클라우드 프로비전을 사용하려면 다음이 필요합니다.
    
- 게스트 사용자가 아닌 Azure AD 테넌트에 대한 전역 관리자 계정입니다.
- Windows 2012 R2 이상프로프로케이징 에이전트에 대한 온-프레미스 서버입니다.
- 온-프레미스 방화벽 구성.

>[!NOTE]
>프로비저닝 에이전트는 현재 영어 서버에만 설치할 수 있습니다. 영어 이외의 서버에 영어 팩을 설치하는 것은 유효한 해결 방법은 아니며 에이전트가 설치하지 못할 수 있습니다. 

문서의 나머지 부분에서는 이러한 필수 구성 사이트에 대한 단계별 지침을 제공합니다.

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서

1. Azure AD 테넌트에서 클라우드 전용 전역 관리자 계정을 만듭니다. 이렇게 하면 온-프레미스 서비스가 실패하거나 사용할 수 없는 경우 테넌트 구성을 관리할 수 있습니다. 클라우드 전용 [글로벌 관리자 계정을 추가하는](../active-directory-users-create-azure-portal.md)방법에 대해 알아봅니다. 이 단계를 완료하면 테넌트가 잠기지 않도록 하는 것이 중요합니다.
1. Azure AD 테넌트에 [사용자 지정 도메인 이름](../active-directory-domains-add-azure-portal.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나로 로그인할 수 있습니다.

### <a name="in-your-directory-in-active-directory"></a>활성 디렉터리에서 디렉터리

[IdFix 도구를](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) 실행하여 동기화를 위한 디렉터리 특성을 준비합니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. 최소 4GB RAM 및 .NET 4.7.1+ 런타임으로 Windows Server 2012 R2 이상을 실행하는 도메인 에 가입한 호스트 서버를 식별합니다.

1. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성합니다.
   - 에이전트에서 다음 포트를 통해 Azure AD에 대한 *아웃바운드* 요청을 수행할 수 있는지 확인합니다.

        | 포트 번호 | 사용 방법 |
        | --- | --- |
        | **80** | TLS/SSL 인증서의 유효성을 검사하는 동안 인증서 해지 목록(CRL)을 다운로드합니다.  |
        | **443** | 서비스와의 모든 아웃바운드 통신을 처리합니다. |
        | **8080**(선택 사항) | 443 포트를 사용할 수 없는 경우 에이전트는 8080 포트를 통해 10분마다 해당 상태를 보고합니다. 이 상태는 Azure AD 포털에 표시됩니다. |
     
   - 방화벽이 원래 사용자에 따라 규칙에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 이러한 포트를 엽니다.
   - 방화벽 또는 프록시에서 안전한 접미사를 지정할 수 \*있는 경우 \*.msappproxy.net 및 .servicebus.windows.net 연결을 추가합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다.
   - 에이전트는 초기 등록을 위해 login.windows.net 및 login.microsoftonline.com에 액세스해야 합니다. 이러한 URL에 대한 방화벽도 엽니다.
   - 인증서 유효성 검사의 경우 mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 및\.www microsoft.com:80 다음 URL의 차단을 해제합니다. 이러한 URL은 다른 Microsoft 제품에서 인증서 유효성 검사를 위해 사용되므로 이러한 URL이 이미 차단 해제되어 있을 수 있습니다.

### <a name="verify-the-port"></a>포트 확인
Azure가 포트 443에서 수신 대기 중이고 에이전트가 포트 443과 통신할 수 있는지 확인하려면 다음 URL을 사용합니다.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있음을 확인합니다. 브라우저를 열고 에이전트가 설치된 서버에서 이전 URL로 이동합니다.

![포트 접근성 검증](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>추가 요구 사항
- [마이크로 소프트 .NET 프레임 워크 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 요구 사항

>[!NOTE]
>전송 계층 보안(TLS)은 보안 통신을 제공하는 프로토콜입니다. TLS 설정을 변경하면 전체 포리스트에 영향을 줍니다. 자세한 내용은 [Windows의 WinHTTP에서 TLS 1.1 및 TLS 1.2를 기본 보안 프로토콜로 사용하도록 설정하려면 업데이트를](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)참조하십시오.

Azure AD Connect 클라우드 프로비전 에이전트를 호스팅하는 Windows 서버는 설치하기 전에 TLS 1.2를 사용하도록 설정해야 합니다.

TLS 1.2를 사용하려면 다음 단계를 따르십시오.

1. 다음 레지스트리 키를 설정합니다.
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 서버를 다시 시작합니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)

