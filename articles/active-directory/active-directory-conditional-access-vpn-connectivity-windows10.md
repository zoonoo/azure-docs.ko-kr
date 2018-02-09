---
title: "VPN 연결에 대한 Azure Active Directory 조건부 액세스(미리 보기) | Microsoft Docs"
description: "VPN 연결에 대한 Azure Active Directory 조건부 액세스의 작동 방식에 대해 알아봅니다. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>VPN 연결에 대한 Azure Active Directory 조건부 액세스(미리 보기)

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다. VPN(가상 사설망) 연결에 대한 Azure AD 조건부 액세스를 사용하면 VPN 연결을 보호할 수 있습니다.


VPN 연결에 대한 조건부 액세스를 구성하려면 다음 단계를 완료해야 합니다. 

1.  VPN 서버를 구성합니다.
2.  VPN 클라이언트를 구성합니다.
3.  조건부 액세스 정책을 구성합니다.


## <a name="before-you-begin"></a>시작하기 전에

이 토픽은 사용자가 다음 토픽에 대해 잘 알고 있다고 가정합니다.

- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)
- [VPN 및 조건부 액세스](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Microsoft가 이 기능을 구현하는 방법에 대한 정보를 얻으려면 [자동 VPN 프로필을 통해 Windows 10에서 원격 액세스 향상](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)을 참조하세요.   


## <a name="prerequisites"></a>필수 조건

VPN 연결에 대한 Azure Active Directory 조건부 액세스를 구성하려면 VPN 서버가 구성되어 있어야 합니다. 



## <a name="step-1-configure-your-vpn-server"></a>1단계: VPN 서버 구성 

이 단계는 Azure AD와 VPN 인증에 사용할 루트 인증서를 구성합니다. VPN 연결에 대한 조건부 액세스를 구성하려면 다음을 수행해야 합니다.

1. Azure Portal에서 VPN 인증서를 만듭니다.
2. VPN 인증서를 다운로드합니다.
2. VPN 서버에 인증서를 배포합니다.

Azure AD는 VPN 연결에 대해 Azure AD에 인증할 때 Windows 10 클라이언트에 발급된 인증서를 등록하기 위해 VPN 인증서를 사용합니다. Windows 10 클라이언트 요청이 인증서이고, 이 경우 VPN 서버인 응용 프로그램에 표시되는 토큰입니다.

![조건부 액세스에 대한 인증서 다운로드](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Azure Portal에서 두 가지 인증서를 만들어서 하나의 인증서가 만료될 때 전환을 관리할 수 있습니다. 인증서를 만들 때 연결에 대한 인증서를 등록하기 위해 인증 시 사용되는 기본 인증서인지 여부를 선택할 수 있습니다.

VPN 인증서를 만들려면:

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **Azure Active Directory**를 클릭합니다. 

    ![Azure Active Directory 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스 선택](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. **조건부 액세스** 페이지의 **관리** 섹션에서 **VPN 연결(미리 보기)**를 클릭합니다.

    ![VPN 연결 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. **VPN 연결** 페이지에서 **새 인증서**를 클릭합니다.

    ![새 인증서 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. **새로 만들기** 페이지에서 다음 단계를 수행합니다.

    ![기간 및 기본 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. **기간 선택**에 대해 **1년**을 선택합니다.

    나. **기본**에 대해 **예**를 선택합니다.

    다. **만들기**를 클릭합니다.

7. VPN 연결 페이지에서 **인증서 다운로드**를 클릭합니다.


이제 VPN 서버에 새로 만든 인증서를 배포할 준비가 되어 있습니다. VPN 서버에서 다운로드한 인증서를 *VPN 인증에 대한 신뢰할 수 있는 루트 CA*로 추가합니다.

Windows RRAS 기반 배포의 경우 NPS 서버에서 다음 명령을 실행하여 *Enterprise NTauth* 스토어에 루트 인증서를 추가합니다.

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>2단계: VPN 클라이언트 구성 

이 단계에서는 [VPN 및 조건부 액세스](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)에 설명된 대로 VPN 클라이언트 연결 프로필을 구성합니다.


## <a name="step-3-configure-your-conditional-access-policy"></a>3단계: 조건부 액세스 정책 구성

이 섹션에서는 VPN 연결에 대한 조건부 액세스 정책을 구성하기 위한 지침을 제공합니다.


1. **조건부 액세스** 페이지에서 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![조건부 액세스 페이지에서 추가 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. **새로 만들기** 페이지의 **이름** 상자에서 정책 이름을 입력합니다. 예를 들어, **VPN 정책**을 입력합니다.

    ![조건부 액세스 페이지에서 정책에 대한 이름 추가](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 및 그룹 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. **사용자 및 그룹** 페이지에서 다음 단계를 수행합니다.

    ![테스트 사용자 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. **사용자 및 그룹 선택**을 클릭합니다.

    나. **선택**을 클릭합니다.

    다. **선택** 페이지에서 테스트 사용자를 선택한 다음 **선택**을 클릭합니다.

    d. **사용자 및 그룹** 페이지에서 **완료**를 클릭합니다.

7. **새로 만들기** 페이지에서 다음 단계를 수행합니다.

    ![클라우드 앱 선택](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. **할당** 섹션에서 **클라우드 앱**을 클릭합니다.

    나. **클라우드 앱** 페이지에서 **앱 선택**을 클릭하고 **선택**을 클릭합니다.

    다. **선택** 페이지의 **응용 프로그램** 상자에서 **vpn**을 입력합니다.

    d. **VPN 서버**를 선택합니다.

    e. **선택**을 클릭합니다.


13. **새로 만들기** 페이지에서 **허용** 페이지를 열려면 **제어** 섹션에서 **허용**을 클릭합니다.

    ![권한 부여 선택](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **허용** 페이지에서 다음 단계를 수행합니다.

    ![다단계 인증 필요 선택](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **다단계 인증 필요**를 선택합니다.

    나. **선택**을 클릭합니다.

15. **새로 만들기** 페이지의 **정책 사용**에서 **켜기**를 클릭합니다.

    ![정책 설정](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **새로 만들기** 페이지에서 **만들기**를 클릭합니다.



## <a name="next-steps"></a>다음 단계

Microsoft가 이 기능을 구현하는 방법에 대한 정보를 얻으려면 [자동 VPN 프로필을 통해 Windows 10에서 원격 액세스 향상](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)을 참조하세요.    

