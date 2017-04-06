---
title: "조직에서 비즈니스용 Microsoft Windows Hello 사용 | Microsoft Docs"
description: "조직에서 Microsoft Passport를 사용하도록 설정하는 배포 지침입니다."
services: active-directory
documentationcenter: 
keywords: "Microsoft Passport 구성, 비즈니스용 Microsoft Windows Hello 배포"
author: MarkusVi
manager: femila
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 6f08fcdb4ec6dd124760cff54c522d998d5a0327
ms.lasthandoff: 12/29/2016


---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>조직에서 비즈니스용 Microsoft Windows Hello 사용
[Windows 10 도메인에 가입된 장치와 Azure Active Directory를 연결한](active-directory-azureadjoin-devices-group-policy.md) 후에 조직에서 비즈니스용 Microsoft Windows Hello를 사용하도록 설정하려면 다음을 수행합니다.

1. System Center Configuration Manager 배포  
2. 정책 설정 구성
3. 인증서 프로필 구성  

## <a name="deploy-system-center-configuration-manager"></a>System Center Configuration Manager 배포
비즈니스용 Windows Hello 키에 따라 사용자 인증서를 배포하려면 다음이 필요합니다.

* **System Center Configuration Manager 현재 분기** - 1606 이상 버전을 설치해야 합니다. 자세한 내용은 [System Center Configuration Manager용 문서](https://technet.microsoft.com/library/mt346023.aspx) 및 [System Center Configuration Manager 팀 블로그](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)를 참조하세요.
* **PKI(공개 키 인프라)** - 사용자 인증서를 사용하여 비즈니스용 Microsoft Windows Hello를 사용하려면 PKI가 제 위치에 있어야 합니다. PKI 인프라가 없거나 사용자 인증에 대해 사용하지 않으려는 경우 Windows Server 2016 빌드 10551(또는 그 이상)이 설치된 새 도메인 컨트롤러를 배포할 수 있습니다. 단계를 따라 [기존 도메인에 복제본 도메인 컨트롤러를 설치](https://technet.microsoft.com/library/jj574134.aspx)하거나 [새 환경을 만드는 경우 새 Active Directory 포리스트를 설치](https://technet.microsoft.com/library/jj574166)합니다. (ISO는 [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)에서 다운로드할 수 있음)

## <a name="configure-policy-settings"></a>정책 설정 구성
비즈니스용 Microsoft Windows Hello 정책 설정을 구성하려면 다음 두 가지 옵션을 사용할 수 있습니다.

* Active Directory에서 그룹 정책 
* System Center Configuration Manager 

Active Directory에서 그룹 정책 사용은 비즈니스용 Microsoft Windows Hello 정책 설정을 구성하는 바람직한 방법입니다. 

System Center Configuration Manager 사용은 인증서를 배포하기 위해 이를 사용하는 경우 선호되는 방법입니다. 이 시나리오의 경우

* 새로운 배포 시나리오와의 호환성을 보장하고
* 클라이언트 쪽 Windows 10 버전 1607 또는 그 이상이 필요합니다.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Active Directory에서 그룹 정책을 통해 비즈니스용 Microsoft Windows Hello 구성
**단계**:

1. 서버 관리자를 열고 **도구** > **그룹 정책 관리**로 이동합니다.
2. 그룹 정책 관리에서 Azure AD 연결을 사용하도록 설정하려는 도메인에 해당하는 도메인 노드로 이동합니다.
3. **그룹 정책 개체**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 선택합니다. 예를 들어 그룹 정책 개체에 이름을 지정하고 비즈니스용 Windows Hello를 사용하도록 설정합니다. **확인**을 클릭합니다.
4. 새 그룹 정책 개체를 마우스 오른쪽 단추로 클릭하고 **편집**을 선택합니다.
5. **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **비즈니스용 Windows Hello**로 이동합니다.
6. **비즈니스용 Windows Hello 사용**을 마우스 오른쪽 단추로 클릭한 다음 **편집**을 선택합니다.
7. **사용** 옵션 단추를 선택하고 **적용**을 클릭합니다. **확인**을 클릭합니다.
8. 이제 그룹 정책 개체를 선택한 위치에 연결할 수 있습니다. 조직의 모든 Windows 10 도메인에 가입된 장치에 대해 이 정책을 사용하도록 설정하려면 그룹 정책을 도메인에 연결합니다. 예:
   * Windows 10 도메인에 가입된 컴퓨터가 배치될 Active Directory의 특정 OU(조직 구성 단위)입니다.
   * Azure AD에 자동 등록될 Windows 10 도메인에 가입된 컴퓨터를 포함하는 특정 보안 그룹입니다.

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>System Center Configuration Manager를 사용하여 비즈니스용 Windows Hello 구성
**단계**:

1. **System Center Configuration Manager**를 열고 **자산 및 규정 준수 > 규정 준수 설정 > 회사 리소스 액세스 > 비즈니스용 Windows Hello 프로필**로 이동합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. 상단 도구 모음에서 **비즈니스용 Windows Hello 프로필**을 클릭합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. **일반** 대화 상자에서 다음 단계를 수행합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. **이름** 텍스트 상자에 프로필의 이름을 입력합니다(예: **내 WHfB 프로필**).
   
    b. **다음**을 클릭합니다.
4. **지원되는 플랫폼** 대화 상자에서 이 비즈니스용 Windows Hello 프로필로 프로비전된 플랫폼을 선택하고 **다음**을 클릭합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. **설정** 대화 상자에서 다음 단계를 수행합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. **비즈니스용 Windows Hello 구성**에서 **사용**을 선택합니다.
   
    b. **TPM(신뢰할 수 있는 플랫폼 모듈) 사용**에서 **필수**를 선택합니다. 
   
    c. **인증 방법**에서 **인증서 기반**을 선택합니다.
   
    d. **다음**을 클릭합니다.
6. **요약** 대화 상자에서 **다음**을 클릭합니다.
7. **완료** 대화 상자에서 **닫기**를 클릭합니다.
8. 위쪽의 도구 모음에서 **배포**를 클릭합니다.
   
    ![비즈니스용 Windows Hello 구성](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>인증서 프로필 구성
온-프레미스 인증을 위해 인증서 기반 인증을 사용하는 경우 인증서 프로필을 구성하고 배포해야 합니다. 이 태스크에서는 System Center Configuration Manager에서 NDES 서버 및 인증서 등록 지점 사이트 역할을 설정해야 합니다. 자세한 내용은 [Configuration Manager에서 인증서 프로필에 대한 필수 구성 요소](https://technet.microsoft.com/library/dn261205.aspx)를 참조하세요.

1. **System Center Configuration Manager**를 열고 **자산 및 규정 준수 > 규정 준수 설정 > 회사 리소스 액세스 > 인증서 프로필**로 이동합니다.
2. 스마트 카드 로그인 EKU(확장 키 사용)가 있는 템플릿을 선택합니다.

인증서 프로필의 **SCEP 등록** 페이지에서 **Passport for Work에 설치하지 않으면 실패**를 **키 저장소 공급자**로 선택해야 합니다.

## <a name="next-steps"></a>다음 단계
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)


