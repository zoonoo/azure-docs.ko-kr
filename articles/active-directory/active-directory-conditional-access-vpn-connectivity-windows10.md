---
title: "가상 개인 네트워크 연결에 대한 Azure Active Directory 조건부 액세스(미리 보기) | Microsoft Docs"
description: "가상 개인 네트워크 연결에 대한 Azure Active Directory 조건부 액세스의 작동 방식에 대해 알아봅니다. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>가상 개인 네트워크 연결에 대한 Azure Active Directory 조건부 액세스(미리 보기)

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 리소스를 액세스하는 방법을 미세 조정할 수 있습니다. VPN(가상 사설망) 연결에 대한 Azure AD 조건부 액세스를 사용하면 조건부 액세스를 통해 VPN 연결을 보호할 수 있습니다.


VPN 연결에 대한 Azure AD 조건부 액세스를 구성하려면 다음 단계를 완료해야 합니다. 

1.  VPN 서버 구성
2.  VPN 클라이언트 구성 
3.  조건부 액세스 정책 구성
4.  확인


## <a name="before-you-begin"></a>시작하기 전에

이 토픽은 사용자가 다음 토픽에 대해 잘 알고 있다고 가정합니다.

- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)
- [VPN 및 조건부 액세스](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

[자동 VPN 프로필을 통해 Windows 10에서 원격 액세스 향상](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)에 대해 살펴보고 Microsoft가 이 기능을 구현하는 방법에 대 한 정보를 파악할 수도 있습니다.   


## <a name="prerequisites"></a>필수 조건

가상 개인 네트워크 연결에 대한 Azure Active Directory 조건부 액세스를 구성하려면 VPN 서버가 구성되어 있어야 합니다. 



## <a name="step-1---configure-your-vpn-server"></a>1단계 - VPN 서버 구성 

이 단계의 목적은 Azure AD와 VPN 인증에 사용할 루트 인증서를 구성하는 것입니다. 가상 개인 네트워크 연결에 대한 조건부 액세스를 구성하려면 다음을 수행해야 합니다.

1. Azure Portal에서 VPN 인증서를 만들기
2. VPN 인증서 다운로드
2. VPN 서버에 인증서 배포

VPN 인증서는 VPN 연결에 대해 Azure AD에 인증할 때 Windows 10 클라이언트에 발급된 인증서를 등록하기 위해 Azure AD에서 사용하는 발급자입니다. 토큰 Windows 10 클라이언트 요청이 인증서이고, 이 경우 VPN 서버인 응용 프로그램에 표시된다고 가정합니다.

![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Azure Portal에서 두 가지 인증서를 만들어서 인증서가 만료될 때 전환을 관리할 수 있습니다. 인증서를 만들 때 인증서에 기본 인증서가 있는지 여부를 선택할 수 있습니다. 기본 인증서는 실제로 인증에서 연결을 위해 인증서에 서명하는 동안 사용됩니다.


**VPN 인증서를 만들려면:**

1. 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다. 

    ![VPN 연결](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![VPN 연결](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. **조건부 액세스** 페이지의 **관리** 섹션에서 **VPN 연결(미리 보기)**를 클릭합니다.

    ![VPN 연결](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. **VPN 연결** 페이지에서 **새 인증서**를 클릭합니다.

    ![VPN 연결](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. **새로 만들기** 페이지에서 다음 단계를 수행합니다.

    ![VPN 연결](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. **기간**으로 **1년**을 선택합니다.

    b. **기본**으로 **예**를 선택합니다.

    c. **만들기**를 클릭합니다.

7. VPN 연결 페이지에서 **인증서 다운로드**를 클릭합니다.


이 시점에서 사용자가 VPN 서버에 새로 만든 인증서를 배포할 준비가 되어 있습니다. VPN 서버에서 다운로드한 인증서를 *VPN 인증에 대한 신뢰할 수 있는 루트 CA*로 추가해야 합니다.

Windows RRAS 기반 배포의 경우 NPS 서버에서 다음 명령을 실행하는 *Enterprise NTauth* 스토어에 루트 인증서를 추가해야 합니다.

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>2단계 - VPN 클라이언트 구성 

이 단계에서는 [VPN 및 조건부 액세스](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)에 설명된 대로 VPN 클라이언트 연결 프로필을 구성해야 합니다.


## <a name="step-3---configure-your-conditional-access-policy"></a>3단계 - 조건부 액세스 정책 구성

이 섹션에서는 VPN 연결에 대한 조건부 액세스 정책을 구성하기 위한 지침을 제공합니다.

**조건부 액세스 정책을 구성하려면:** 

1. **조건부 액세스** 페이지에서 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. **새로 만들기** 페이지의 **이름** 텍스트 상자에서 **VPN 정책**과 같은 정책 이름을 입력합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. **사용자 및 그룹** 페이지에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. **사용자 및 그룹 선택**을 클릭합니다.

    b. **선택**을 클릭합니다.

    c. **선택** 페이지에서 테스트 사용자를 선택한 다음 **선택**을 클릭합니다.

    d. **사용자 및 그룹** 페이지에서 **완료**를 클릭합니다.

7. **새로 만들기** 페이지에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. **할당** 섹션에서 **클라우드 앱**을 클릭합니다.

    b. **클라우드 앱** 페이지에서 **앱 선택**을 클릭하고 **선택**을 클릭합니다.

    c. **선택** 페이지의 **응용 프로그램** 텍스트 상자에서 **vpn**을 입력합니다.

    d. **VPN 서버**를 선택합니다.

    e. **선택**을 클릭합니다.


13. **새로 만들기** 페이지에서 **허용** 페이지를 열려면 **제어** 섹션에서 **허용**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **허용** 페이지에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **다단계 인증 필요**를 선택합니다.

    b. **선택**을 클릭합니다.

15. **새로 만들기** 페이지의 **정책 사용**에서 **켜기**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **새로 만들기** 페이지에서 **만들기**를 클릭합니다.



## <a name="next-steps"></a>다음 단계

Microsoft가 이 기능을 구현하는 방법에 대한 정보를 얻으려면 [자동 VPN 프로필을 통해 Windows 10에서 원격 액세스 향상](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)을 참조하세요.    

