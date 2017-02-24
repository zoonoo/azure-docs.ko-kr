---
title: "Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결 | Microsoft Docs"
description: "관리자가 기업 네트워크에 도메인이 가입되도록 장치를 활성화하는 그룹 정책을 구성할 수 있는 방법에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: markvi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 7d141adf04cfb99e57c63ba62de4a7dad9ab8326
ms.openlocfilehash: 290645b920bc4a83c610e80266854450b6e1509a


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결
도메인 가입은 조직에서 15년이 넘도록 작업하기 위해 장치를 연결해 온 일반적인 방식입니다. 사용자가 Windows Server Active Directory(Active Directory) 회사 또는 학교 계정을 사용하여 장치에 로그인할 수 있도록 설정했으며 IT에서 이러한 장치를 완전히 관리할 수 있도록 했습니다. 조직에서는 일반적으로 사용자에 게 장치를 프로비전하는 이미징 메서드에 의존하고 이를 관리하는 데 System Center 구성 관리자(SCCM) 또는 그룹 정책을 사용합니다.

Windows 10에서 도메인 가입을 사용하면 장치를 Azure AD(Azure Active Directory)에 연결한 후 다음과 같은 이점이 있습니다.

* 어디에서나 Azure AD 리소스에 SSO(single Sign-On)
* 회사 또는 학교 계정을 사용하여 엔터프라이즈 Windows 스토어에 액세스(Microsoft 계정 필요 없음)
* 회사 또는 학교 계정을 사용하여 장치 간 사용자 설정의 엔터프라이즈 규격 로밍(Microsoft 계정 필요 없음)
* Microsoft Passport 및 Windows Hello를 사용하는 회사 또는 학교 계정에 대한 강력한 인증 및 손쉬운 로그인
* 조직 장치의 그룹 정책 설정을 준수하는 장치 액세스 할 수 있도록 제한하는 기능

## <a name="prerequisites"></a>필수 조건
도메인 가입이 계속 유용합니다. 그러나 SSO에 대한 Azure AD 혜택을 얻고 회사 또는 학교 계정으로 설정을 로밍하고 회사 또는 학교 계정으로 Windows 스토어에 액세스하려면 다음이 필요합니다.

* Azure AD 구독
* Azure AD에 온-프레미스 디렉터리를 확장하는 Azure AD Connect
* Azure AD에 도메인 가입 장치를 연결하도록 설정한 정책
* 장치에 Windows 10 빌드(빌드 10551 또는 이후 버전)

또한 Microsoft Passport for Work 및 Windows Hello를 사용하도록 하려면 다음과 같은 사항도 필요합니다.

* 사용자 인증서를 발급하기 위한 PKI(공개 키 인프라)입니다.
* Technical Preview용 System Center Configuration Manager 버전 1509. 자세한 내용은 [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) 및 [System Center Configuration Manager 팀 블로그](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)를 참조하세요. Microsoft Passport 키 기반의 사용자 인증서를 배포하려는 데 필요합니다.

PKI 배포 요구 사항에 대한 대안으로 다음을 수행할 수 있습니다.

* Windows Server 2016 Active Directory 도메인 서비스와 몇 가지 도메인 컨트롤러를 보유합니다.

조건부 액세스를 활성화하려면 추가 배포 없이 ‘도메인 가입된’ 장치에 대한 액세스를 허용하는 그룹 정책 설정을 만들 수 있습니다. 장치의 규정 준수에 따른 액세스 제어를 관리하려면 다음이 필요합니다.

* Passport 시나리오를 위한 Technical Preview용 System Center Configuration Manager 버전 1509

## <a name="deployment-instructions"></a>배포 지침

배포하려면 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)에 나와 있는 단계를 따르세요.

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)




<!--HONumber=Dec16_HO4-->


