---
title: FortiGate 배포 가이드 | Microsoft Docs
description: Fortinet FortiGate 차세대 방화벽 제품을 설정하고 사용합니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273333"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 배포 가이드

이 배포 가이드를 사용하여 Fortinet FortiGate 차세대 방화벽 제품을 설정하고 사용하는 방법을 알아봅니다.

## <a name="redeem-the-fortigate-license"></a>FortiGate 라이선스 교환

Fortinet FortiGate 차세대 방화벽 제품은 Azure IaaS(Infrastructure as a Service)에서 가상 머신으로 사용할 수 있습니다. 이 가상 머신의 라이선스 모드로는 종량제 및 BYOL의 두 가지가 있습니다.

Fortinet에서 Azure AD(Azure Active Directory) "Get to Production SHA(Secure Hybrid Access)" 팀의 멤버에게 라이선스를 제공할 수 있습니다. 라이선스가 제공되지 않은 경우 종량제 배포도 작동합니다.

라이선스가 발급된 경우 Fortinet에서 온라인으로 교환해야 하는 등록 코드를 제공합니다.

![FortiGate SSL VPN 등록 코드의 스크린샷](registration-code.png)

1. https://support.fortinet.com/ 에서 등록합니다.
2. 등록되면 https://support.fortinet.com/ 에서 로그인합니다.
3. **자산** > **등록/활성화**로 이동합니다.
4. Fortinet에서 제공하는 등록 코드를 입력합니다.
5. 등록 코드를 지정하고, **비정부 사용자가 제품을 사용합니다**를 선택하고, **다음**을 선택합니다.
6. 제품 설명(예: FortiGate)을 입력하고, Fortinet 파트너를 **기타** > **Microsoft**로 설정하고, **다음**을 선택합니다.
7. **Fortinet 제품 등록 계약**을 수락하고, **다음**을 선택합니다.
8. **사용 약관**에 동의하고, **확인**을 선택합니다.
9. **라이선스 파일 다운로드**를 선택하고, 나중에 사용할 수 있도록 해당 라이선스를 저장합니다.


## <a name="download-firmware"></a>펌웨어 다운로드

현재 Fortinet FortiGate Azure VM은 SAML 인증에 필요한 펌웨어 버전이 함께 제공되지 않습니다. Fortinet에서 최신 버전을 가져와야 합니다.

1. https://support.fortinet.com/에 로그인합니다.
2. **다운로드** > **펌웨어 이미지**로 이동합니다.
3. **릴리스 정보**의 오른쪽에서 **다운로드**를 선택합니다.
4. **v6.** > **6.** > **6.4.** 를 선택합니다.
5. 같은 행에서 **HTTPS** 링크를 선택하여 **FGT_VM64_AZURE-v6-build1637-FORTINET.out**을 다운로드합니다.
6. 나중에 사용할 수 있도록 해당 파일을 저장합니다.


## <a name="deploy-the-fortigate-vm"></a>FortiGate VM 배포

1. https://portal.azure.com 으로 이동하여 FortiGate 가상 머신을 배포하려는 구독에 로그인합니다.
2. 새 리소스 그룹을 만들거나 FortiGate 가상 머신을 배포하려는 리소스 그룹을 엽니다.
3. **추가**를 선택합니다.
4. **Marketplace 검색**에서 *Forti*를 입력합니다. **Fortinet FortiGate 차세대 방화벽**을 선택합니다.
5. 소프트웨어 플랜을 선택합니다(라이선스가 있으면 BYOL, 없으면 종량제). **만들기**를 선택합니다.
6. VM 구성을 채웁니다.

    ![[가상 머신 만들기]의 스크린샷](virtual-machine.png)

7. **인증 유형**을 **암호**로 설정하고, VM의 관리 자격 증명을 입력합니다.
8. **검토 + 만들기** > **만들기**를 선택합니다.
9. VM 배포가 완료될 때까지 기다립니다.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>고정 공용 IP 주소 설정 및 정규화된 도메인 이름 할당

일관된 사용자 환경을 제공하려면 FortiGate VM의 공용 IP 주소를 고정 주소로 할당하는 것이 좋습니다. 그리고 FQDN(정규화된 도메인 이름)에 매핑합니다.

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. **개요** 화면에서 공용 IP 주소를 선택합니다.

    ![Fortigate SSL VPN의 스크린샷](public-ip-address.png)

3. **고정** > **저장**을 선택합니다.

FortiGate VM이 배포되는 환경에 대해 공개적으로 라우팅할 수 있는 도메인 이름을 소유하고 있는 경우 VM에 대한 호스트(A) 레코드를 만듭니다. 이 레코드는 앞에서 고정 주소로 할당된 공용 IP 주소에 매핑됩니다.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>TCP 포트에 대한 새 인바운드 네트워크 보안 그룹 규칙 만들기

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **네트워킹**을 선택합니다. 네트워크 인터페이스가 나열되고, 인바운드 포트 규칙이 표시됩니다.
3. **인바운드 포트 규칙 추가**를 선택합니다.
4. TCP 8443에 대한 새 인바운드 포트 규칙을 만듭니다.

    ![[인바운드 보안 규칙 추가]의 스크린샷](port-rule.png)

5. **추가**를 선택합니다.


## <a name="create-a-custom-azure-app-for-fortigate"></a>FortiGate용 사용자 지정 Azure 앱 만들기

1. https://portal.azure.com 으로 이동하고, FortiGate 로그인 ID를 제공할 테넌트의 Azure AD 창을 엽니다.
2. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션**을 선택합니다.
3. **새 애플리케이션** > **비갤러리 애플리케이션**을 선택합니다.
4. 이름(예: FortiGate)을 입력하고 **추가**를 선택합니다.
5. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.
6. 로그인할 수 있는 사용자를 추가하고, **할당**을 선택합니다.
7. 왼쪽 메뉴에서 **Single Sign-On**을 선택합니다.
8. **SAML**을 선택합니다.
9. **기본 SAML 구성**에서 연필 아이콘을 선택하여 구성을 편집합니다.
10. 다음을 구성 합니다.
    - **식별자(엔터티 ID)** - `https://<address>/remote/saml/metadata`
    - **회신 URL(Assertion Consumer Service URL)** - `https://<address>/remote/saml/login`
    - **로그아웃 URL** - `https://<address>/remote/saml/logout`

    `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

11. 나중에 사용할 수 있도록 엔터티 ID, 회신 URL 및 로그아웃 URL을 기록해 둡니다.
12. **저장**을 선택하고 **기본 SAML 구성**을 닫습니다.
13. **3 – SAML 서명 인증서**에서 **인증서(Base64)** 를 다운로드하고, 나중에 사용할 수 있도록 저장합니다.
14. **4 – 설정(앱 이름)** 에서 Azure 로그인 URL, Azure AD 식별자 및 Azure 로그아웃 URL을 복사하고, 나중에 사용할 수 있도록 저장합니다.
15. **2 – 사용자 특성 및 클레임**에서 연필 아이콘을 클릭하여 구성을 편집합니다.
16. **새 클레임 추가**를 선택하고 이름을 **username**으로 설정합니다.
17. 원본 특성을 **user.userprincipalname**으로 설정합니다.
18. **저장** > **그룹 클레임 추가** > **모든 그룹**을 선택합니다.
19. **그룹 클레임 이름 사용자 지정**을 선택하고, 이름을 **group**으로 설정합니다.
20. **저장**을 선택합니다.


## <a name="prepare-for-group-matching"></a>그룹 매칭 준비

FortiGate는 로그인 후에 그룹 멤버 자격에 따라 다양한 사용자 포털 환경을 허용합니다. 예를 들어 마케팅 그룹과 재무 그룹에 대해 각각의 서로 다른 환경이 있을 수 있습니다. 사용자 그룹을 만드는 방법은 다음과 같습니다.

1. https://portal.azure.com 으로 이동하고, FortiGate 로그인 ID를 제공할 테넌트의 Azure AD 창을 엽니다.
2. **그룹** > **새 그룹**을 선택합니다.
3. 다음 세부 정보를 사용하여 그룹을 만듭니다.
    - 그룹 유형 = 보안
    - 그룹 이름 = `a meaningful name`
    - 그룹 설명 = `a meaningful description for the group`
    - 멤버 자격 유형 = 할당됨
    - 멤버 = `users for the user experience that will map to this group`
4. 추가 사용자 환경에 대해 3단계와 4단계를 반복합니다.
5. 그룹을 만든 후에는 각 그룹을 선택하고 각 그룹의 **개체 ID**를 기록합니다.
6. 나중에 사용할 수 있도록 이러한 개체 ID 및 그룹 이름을 저장합니다.


## <a name="configure-the-fortigate-vm"></a>FortiGate VM 구성

다음 섹션에서는 FortiGate VM을 설정하는 방법을 안내합니다.

### <a name="install-the-license"></a>라이선스 설치

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 배포에 BYOL 모델을 사용하는 경우 라이선스를 업로드하라는 메시지가 표시됩니다. 앞에서 만든 라이선스 파일을 선택하여 업로드합니다. **확인**을 선택하고 FortiGate VM을 다시 시작합니다.

    ![FortiGate VM 라이선스의 스크린샷](license.png)

5. 다시 부팅되면 관리자 자격 증명으로 다시 로그인하여 라이선스의 유효성을 검사합니다.

### <a name="update-firmware"></a>펌웨어 업데이트

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **펌웨어**를 선택합니다.
5. **펌웨어 관리**에서 **브라우저**를 선택하고, 앞에서 다운로드한 펌웨어 파일을 선택합니다.
6. 경고를 무시하고 **백업 구성 및 업그레이드**를 선택합니다.

    ![펌웨어 관리 스크린샷](backup-configure-upgrade.png)

7. **계속**을 선택합니다.
8. FortiGate 구성을 .conf 파일로 저장하라는 메시지가 표시되면 **저장**을 선택합니다.
9. 펌웨어가 업로드되고 적용될 때까지 기다립니다. FortiGate VM이 다시 부팅될 때까지 기다립니다.
10. FortiGate VM이 다시 부팅되면 관리자 자격 증명으로 다시 로그인합니다.
11. 대시보드를 설정하라는 메시지가 표시되면 **나중에**를 선택합니다.
12. 자습서 비디오가 시작되면 **확인**을 선택합니다.

### <a name="change-the-management-port-to-tcp"></a>관리 포트를 TCP로 변경

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템**을 선택합니다.
5. **관리 설정**에서 HTTPS 포트를 **8443**으로 변경하고 **적용**을 선택합니다.
6. 변경 내용이 적용되면 브라우저에서 [관리] 페이지를 다시 로드하려고 하지만 실패합니다. 지금부터 관리 페이지 주소는 `https://<address>`입니다.

    ![[원격 인증서 업로드]의 스크린샷](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Azure AD SAML 서명 인증서 업로드

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **인증서**를 선택합니다.
5. **가져오기** > **원격 인증서**를 차례로 선택합니다.
6. Azure 테넌트의 FortiGate 사용자 지정 앱 배포에서 다운로드한 인증서를 찾습니다. 이 인증서를 선택하고, **확인**을 선택합니다.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>사용자 지정 SSL 인증서 업로드 및 구성

사용 중인 FQDN을 지원하는 사용자 고유의 SSL 인증서를 사용하여 FortiGate VM을 구성할 수 있습니다. 프라이빗 키와 함께 PFX 형식으로 패키지된 SSL 인증서에 액세스할 수 있는 경우 해당 인증서를 이 용도로 사용할 수 있습니다.

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. 인증서 오류를 무시하고 계속합니다.
3. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
4. 왼쪽 메뉴에서 **시스템** > **인증서**를 선택합니다.
5. **가져오기** > **로컬 인증서** > **PKCS #12 인증서**를 선택합니다.
6. SSL 인증서 및 프라이빗 키가 포함된 .PFX 파일을 찾습니다.
7. .PFX 암호와 의미 있는 인증서 이름을 입력합니다. 그런 다음, **확인**을 선택합니다.
8. 왼쪽 메뉴에서 **시스템** > **설정**을 선택합니다.
9. **관리 설정**에서 **HTTPS 서버 인증서** 옆에 있는 목록을 펼치고, 위에서 가져온 SSL 인증서를 선택합니다.
10. **적용**을 선택합니다.
11. 브라우저 창을 닫고 `https://<address>`로 이동합니다.
12. FortiGate 관리자 자격 증명으로 로그인합니다. 이제 올바른 SSL 인증서가 사용되는 것을 볼 수 있습니다.


### <a name="perform-command-line-configuration"></a>명령줄 구성 수행

다음 섹션에서는 명령줄을 사용하여 다양한 구성 단계를 제공합니다.

#### <a name="for-saml-authentication"></a>SAML 인증

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 선택합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다. 다음 단계에서는 앞에서 기록해 둔 URL이 필요합니다.
    - 엔터티 ID
    - 회신 URL
    - 로그아웃 URL
    - Azure 로그인 URL
    - Azure AD 식별자
    - Azure 로그아웃 URL
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure 로그아웃 URL에는 `?` 문자가 포함되어 있습니다. 이 경우 FortiGate 직렬 콘솔에 올바르게 제공되려면 특수한 키 시퀀스가 필요합니다. URL은 일반적으로 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`입니다. [직렬 콘솔]에서 이 URL을 제공하려면 다음을 입력합니다.
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    그런 다음, 다음과 같이 CTRL+V를 입력하고 URL의 나머지 부분을 붙여넣어 줄을 완성합니다. 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. 구성을 확인하려면 다음 명령을 실행합니다.

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>그룹 매칭

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 선택합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    FortiGate에서 서로 다른 포털 환경을 사용하게 될 추가 그룹마다 이러한 명령을 반복합니다(코드의 두 번째 줄부터 시작).

#### <a name="for-authentication-timeout"></a>인증 제한 시간

1. https://portal.azure.com 으로 이동하여 FortiGate VM에 대한 설정을 엽니다.
2. 왼쪽 메뉴에서 **직렬 콘솔**을 선택합니다.
3. [직렬 콘솔]에서 FortiGate VM 관리자 자격 증명으로 로그인합니다.
4. [직렬 콘솔]에서 다음 명령을 실행합니다.

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN 포털 및 방화벽 정책 만들기

1. `https://<address>`로 이동합니다. 여기서 `<address>`는 FortiGate VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2. FortiGate VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
3. 왼쪽 메뉴에서 **VPN** > **SSL-VPN 포털** > **새로 만들기**를 선택합니다.
6. 이름을 제공합니다(일반적으로 사용자 지정 포털 환경을 제공하는 데 사용되는 Azure 그룹과 일치함).
7. **원본 IP 풀** 옆에 있는 더하기 기호( **+** )를 선택하고 기본 풀을 선택한 다음, **닫기**를 선택합니다.
8. 이 그룹에 대한 환경을 사용자 지정합니다. 테스트를 위해 포털 메시지 및 테마를 사용자 지정할 수 있습니다. 또한 사용자를 내부 리소스로 안내하는 사용자 지정 책갈피를 만들 수도 있습니다.
9. **확인**을 선택합니다.
10. 사용자 지정 포털 환경을 사용할 Azure 그룹마다 5~9단계를 반복합니다.
11. VPN에서 **SSL-VPN 설정**을 선택합니다.
12. **인터페이스에서 수신 대기** 옆에 있는 더하기 기호( **+** )를 선택하고 **Port1**을 선택한 다음, **닫기**를 선택합니다.
14. 이전에 사용자 지정 SSL 인증서를 설치한 경우 드롭다운 메뉴에서 사용자 지정 SSL 인증서를 사용하도록 **서버 인증서**를 변경합니다.
15. **인증/포털 매핑**에서 **새로 만들기**를 선택합니다. 첫 번째 Azure 그룹을 선택하고, 동일한 이름의 포털과 매칭합니다. 그런 다음, **확인**을 선택합니다.
18. Azure 그룹 및 포털의 페어링마다 15-17단계를 반복합니다.
19. **인증/포털 매핑**에서 **다른 모든 사용자/그룹**을 편집합니다.
20. 포털을 **전체 액세스**로 설정하고 **확인** > **적용**을 선택합니다.
23. **SSL-VPN 설정** 페이지의 맨 위로 스크롤하여 **SSL-VPN 정책이 없습니다. 이러한 설정을 사용하여 새 SSL-VPN 정책을 만들려면 여기를 클릭하세요**라는 경고를 선택합니다.
24. 이름을 입력합니다(예: **VPN Grp**). 그런 다음, **송신 인터페이스**를 **포트**로 설정하고 **원본**을 선택합니다.
27. **주소**에서 **모두**를 선택합니다.
28. **사용자**에서 첫 번째 Azure 그룹을 선택합니다.
29. **닫기** > **대상**을 선택합니다. **주소**에서 일반적으로 내부 네트워크입니다. 테스트를 위해 **login.microsoft.com**을 선택합니다.
32. **닫기** > **서비스** > **모두**를 선택합니다. 그런 다음, **닫기** > **확인**을 선택합니다.
37. 왼쪽 메뉴에서 **정책 및 개체** > **방화벽 정책**을 선택합니다.
39. **SSL-VPN 터널 인터페이스(ssl.root)**  > **포트**를 펼칩니다.
40. 이전에 만든 VPN 정책(**VPN Grp 1**)을 마우스 오른쪽 단추로 클릭하고, **복사**를 선택합니다.
41. [VPN 정책] 아래를 마우스 오른쪽 단추로 클릭하고 **붙여넣기** > **아래**를 선택합니다.
42. 새 정책을 편집하여 다른 이름을 제공합니다(예: **VPN Grp2**). 또한 적용되는 그룹을 다른 Azure 그룹으로 변경합니다.
43. 새 정책을 마우스 오른쪽 단추로 클릭하고, 상태를 **사용**으로 설정합니다.


## <a name="test-sign-in-by-using-azure"></a>Azure를 사용하여 로그인 테스트

1. 개인 브라우저 세션을 사용하여 `https://<address>`로 이동합니다.  
2. 로그인할 수 있도록 로그인이 Azure AD로 리디렉션됩니다.
3. Azure 테넌트에서 FortiGate 앱에 할당된 사용자에게 자격 증명이 제공되면 적절한 사용자 포털이 표시됩니다.

    ![FortiGate SSL VPN의 스크린샷](test-sign-in.png)
