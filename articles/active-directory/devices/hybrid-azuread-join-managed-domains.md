---
title: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성 | Microsoft Docs
description: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e45b3b19821644142176c5c0cc7646c4643fe17c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235218"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>자습서: 관리되는 도메인용 하이브리드 Azure Active Directory 조인 구성

사용자와 비슷한 방식으로 디바이스는 보호하려는 다른 ID가 되고, 언제 어디서나 리소스를 보호하는 데도 사용됩니다. 다음 방법 중 하나를 사용하여 디바이스의 ID를 Azure AD로 가져와서 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. 이와 동시에 [조건부 액세스](../active-directory-conditional-access-azure-portal.md)를 사용하여 클라우드 및 온-프레미스 리소스에 대한 액세스를 보호할 수 있습니다.

이 자습서에서는 관리되는 도메인에서 디바이스에 대해 하이브리드 Azure AD 조인을 구성하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 하이브리드 Azure AD 조인 구성
> * Windows 하위 수준 디바이스 설정
> * 가입 장치 확인 
> * 문제 해결 


## <a name="prerequisites"></a>필수 조건

이 자습서에서는 사용자가 다음 항목에 대해 잘 알고 있다고 가정합니다.
    
-  [Azure Active Directory의 장치 관리 소개](../device-management-introduction.md)
    
-  [하이브리드 Azure Active Directory 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)

-  [디바이스의 하이브리드 Azure AD 조인을 제어하는 방법](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.


이 문서의 시나리오를 구성하려면 다음이 필요합니다.

- 설치할 [최신 버전의 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)(1.1.819.0 이상) 

Azure AD Connect에서 Azure AD에 조인된 하이브리드 Azure AD가 되려는 디바이스의 컴퓨터 개체를 동기화했는지 확인합니다. 컴퓨터 개체가 특정 OU(조직 구성 단위)에 속한 경우 Azure AD Connect에서 이러한 OU를 동기화하기 위해 구성해야 합니다.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 관련 마법사는 디바이스 등록을 위한 SCP(서비스 연결 지점)를 구성합니다.

이 문서의 구성 단계는 이 마법사를 기준으로 합니다. 

하이브리드 Azure AD 조인을 위해서는 디바이스가 조직의 네트워크 내에서 다음 Microsoft 리소스에 액세스할 수 있어야 합니다.  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- [https://autologon.microsoftazuread-sso.com](https://autologon.microsoftazuread-sso.com)(Seamless SSO를 사용 중이거나 사용할 예정인 경우)

조직에서 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우, Windows 10 1709부터 [GPO(그룹 정책 개체)를 사용하여 컴퓨터에서 프록시 설정을 구성](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)할 수 있습니다. 컴퓨터에서 Windows 10 1709 이전 버전이 실행 중인 경우 WPAD(웹 프록시 자동 검색)을 구현하여 Windows 10 컴퓨터가 Azure AD에 장치를 등록하도록 해야 합니다. 

조직에서 인증된 아웃바운드 프록시를 통해 인터넷에 액세스해야 하는 경우 Windows 10 컴퓨터에서 아웃바운드 프록시를 성공적으로 인증할 수 있는지 확인해야 합니다. Windows 10 컴퓨터는 머신 컨텍스트를 사용하여 디바이스 등록을 실행하므로 머신 컨텍스트를 사용하여 아웃바운드 프록시 인증을 구성해야 합니다. 아웃바운드 프록시 공급자와 함께 구성 요구 사항을 준수하세요. 



## <a name="configure-hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인 구성

Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면 다음이 필요합니다.

- Azure AD 테넌트에 대한 전역 관리자의 자격 증명  

- 각 포리스트에 대한 엔터프라이즈 관리자 자격 증명


**Azure AD Connect를 사용하여 하이브리드 Azure AD 조인을 구성하려면**

1. Azure AD Connect를 시작하고 **구성**을 클릭릭합니다.

    ![시작](./media/hybrid-azuread-join-managed-domains/11.png)

2. **추가 작업** 페이지에서 **장치 옵션 구성**을 선택하고 **다음**을 클릭합니다. 

    ![추가 작업](./media/hybrid-azuread-join-managed-domains/12.png)

3. **개요** 페이지에서 **다음**을 클릭합니다. 

    ![개요](./media/hybrid-azuread-join-managed-domains/13.png)

4. **Azure AD에 연결** 페이지에서 Azure AD 테넌트에 대한 전역 관리자 자격 증명을 입력합니다.  

    ![Azure에 연결](./media/hybrid-azuread-join-managed-domains/14.png)

5. **장치 옵션** 페이지에서 **하이브리드 Azure AD 조인 구성**을 선택하고 **다음**을 클릭합니다. 

    ![장치 옵션](./media/hybrid-azuread-join-managed-domains/15.png)

6. **SCP** 페이지에서 Azure AD Connect로 SCP를 구성하려는 각 포리스트에 대해 다음 단계를 수행한 다음, **다음**을 클릭합니다. 

    ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

    a. 포리스트를 선택합니다.

    b. 인증 서비스를 선택합니다.

    다. **추가**를 클릭하여 엔터프라이즈 관리자 자격 증명을 입력합니다.


7. **장치 운영 체제** 페이지에서 Active Directory 환경의 장치에서 사용되는 운영 체제를 선택하고 **다음**을 클릭합니다. 

    ![디바이스 운영 체제](./media/hybrid-azuread-join-managed-domains/17.png)


8. **구성 준비** 페이지에서 **구성**을 클릭합니다. 

    ![구성 준비](./media/hybrid-azuread-join-managed-domains/19.png)

9. **구성 완료** 페이지에서 **끝내기**를 클릭릭합니다. 

    ![구성 완료](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Windows 하위 수준 디바이스 설정

도메인에 가입된 장치 중 일부가 Windows 하위 수준 장치인 경우 다음을 수행해야 합니다.

- 장치 설정 업데이트
 
- 장치 등록에 대한 로컬 인트라넷 설정 구성

- Seamless SSO(Single Sign-On) 구성

- Windows 하위 수준 장치 제어 


### <a name="update-device-settings"></a>장치 설정 업데이트 

Windows 하위 수준 디바이스를 등록하려면 사용자가 Azure AD에서 디바이스를 등록할 수 있도록 허용하는 디바이스 설정을 선택해야 합니다. Azure Portal의 다음 위치에서 이러한 값을 확인할 수 있습니다.

`Home > [Name of your tenant] > Devices - Device settings`  


    
다음 정책을 **모두**: **사용자가 디바이스를 Azure AD에 등록할 수 있습니다.** 로 설정해야 합니다.

![디바이스 등록](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>장치 등록에 대한 로컬 인트라넷 설정 구성

Windows 하위 수준 장치의 하이브리드 Azure AD 조인을 성공적으로 완료하고 장치가 Azure AD를 인증할 때 인증서 프롬프트를 표시하지 않으려면 도메인에 가입된 장치에 정책을 푸시하여 Internet Explorer의 로컬 인트라넷 영역에 다음 URL을 추가할 수 있습니다.

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`

또한, 사용자의 로컬 인트라넷 영역에서 **Allow updates to status bar via script**(스크립트를 통해 상태 표시줄 업데이트 허용)을 사용하도록 설정해야 합니다.


### <a name="configure-seamless-sso"></a>Seamless SSO 구성

Azure AD 클라우드 인증 방법으로 PTA(통과 인증) 또는 PHS(암호 해시 동기화)를 사용 중인 관리되는 도메인에서 Windows 하위 수준 디바이스의 하이브리드 Azure AD 조인을 성공적으로 완료하려면 [Seamless SSO도 구성](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature)해야 합니다. Seamless SSO를 구성하는 데 문제가 있는 경우 [Azure Active Directory Seamless Single Sign-On 문제 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)을 참조하세요.


### <a name="control-windows-down-level-devices"></a>Windows 하위 수준 디바이스 제어 

Windows 하위 수준 디바이스를 등록하려면 다운로드 센터에서 Windows Installer 패키지(.msi)를 다운로드하여 설치해야 합니다. 자세한 내용은 [디바이스의 하이브리드 Azure AD 조인 제어](hybrid-azuread-join-control.md#control-windows-down-level-devices) 문서를 참조하세요. 


## <a name="verify-the-registration"></a>등록 확인

Azure 테넌트에서 디바이스 등록 상태를 확인하려면 **[Azure Active Directory PowerShell 모듈](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** 에서 **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** cmdlet을 사용할 수 있습니다.

**Get-MSolDevice** cmdlet을 사용하여 서비스 세부 정보를 확인하려는 경우 다음이 적용됩니다.

- Windows 클라이언트의 ID와 일치하는 **디바이스 ID**를 갖는 개체가 있어야 합니다.
- **DeviceTrustType** 값은 **도메인 가입됨**이어야 합니다. 이 값은 Azure AD 포털에서 장치 페이지의 **하이브리드 Azure AD 가입**과 같습니다.
- 조건부 액세스에 사용되는 장치의 경우 **Enabled** 값이 **True**이고 **DeviceTrustLevel**이 **Managed**여야 합니다. 


**서비스 세부 정보를 확인하려면**

1. 관리자 권한으로 **Windows PowerShell**을 엽니다.

2. `Connect-MsolService`를 입력하여 Azure 테넌트에 연결합니다.  

3. `get-msoldevice -deviceId <deviceId>`을 입력합니다.

6. **Enabled**가 **True**로 설정되어 있는지 확인인합니다.





## <a name="troubleshoot-your-implementation"></a>구현 문제 해결

도메인 가입 Windows 디바이스에 대한 하이브리드 Azure AD 조인은 완료할 때 문제가 발생하는 경우 다음을 참조하세요.

- [Windows 최신 장치의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [Windows 하위 수준 장치의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [페더레이션된 도메인에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
> [수동으로 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-manual.md)

