---
title: "비즈니스용 Windows Hello 및 Azure AD를 통해 암호 없이 ID 인증 | Microsoft Docs"
description: "비즈니스용 Windows Hello의 개요와 비즈니스용 Windows Hello를 배포하는 방법에 대한 추가 정보를 제공합니다."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 22534cc5f7f2de235bc1f1212c63ea227083c5a4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>비즈니스용 Windows Hello를 통해 암호 없이 ID 인증
암호만을 사용하는 현재 인증 방법은 사용자의 안전을 유지하는 데 충분하지 않습니다. 사용자는 암호를 다시 사용하고 잊기도 합니다. 암호는 깨질 수도 있고, 피싱될 수도 있고, 공격에 취약하고, 추측될 수도 있습니다. 기억하기 어려울 수도 있고 “[Pass the Hash](https://technet.microsoft.com/dn785092.aspx)”와 같은 공격에 취약하기도 합니다.

## <a name="about-windows-hello-for-business"></a>비즈니스용 Windows Hello 정보
비즈니스용 Windows Hello는 조직과 소비자를 위한 보안 방법으로, 암호 수준을 넘어서는 개인/공개 키 또는 인증서 기반 인증 방법입니다. 이 인증 방식은 암호를 대신할 수 있으며 위반, 도난 및 피싱에 강한 키 쌍 자격 증명에 의존합니다.

 사용자는 비즈니스용 Windows Hello를 사용하여 Microsoft 계정, Windows Server Active Directory 계정, Microsoft Azure AD(Azure Active Directory) 계정 또는 FIDO(Fast ID Online) 인증을 지원하는 Microsoft가 아닌 타사 서비스에 인증할 수 있습니다. 비즈니스용 Windows Hello 등록 중에 진행되는 초기 2단계 인증 후에 비즈니스용 Windows Hello가 사용자의 장치에 설치되며 사용자는 Windows Hello 또는 PIN에 해당하는 제스처를 설정합니다. 사용자는 ID를 확인하는 제스처를 제공합니다. 그러면 Windows에서 비즈니스용 Windows Hello를 사용하여 사용자를 인증하고 사용자가 보호된 리소스 및 서비스에 액세스할 수 있습니다.

개인 키는 사용자가 장치에 로그인하는 데 사용하는 스마트 카드와 같은 PIN, 생체 인식 또는 원격 장치 등의 "사용자 제스처"를 통해서만 사용할 수 있습니다. 이 정보는 인증서 또는 비대칭 키 쌍에 연결됩니다. 개인 키는 장치에 TPM(신뢰할 수 있는 플랫폼 모듈) 칩이 있는지를 입증하는 하드웨어입니다. 개인 키는 절대 장치 밖으로 나가지 않습니다.

공개 키는 Azure Active Directory 및 Windows Server Active Directory에 등록됩니다(온-프레미스용). IDP(ID 공급자)는 사용자의 공개 키를 개인 키에 매핑하여 사용자의 유효성을 검사하고 OTP(일회용 암호), Phonefactor 또는 다른 알림 메커니즘을 통해 로그인 정보를 제공합니다.

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>기업에서 비즈니스용 Windows Hello를 도입해야 하는 이유
기업에서는 비즈니스용 Windows Hello를 사용하여 다음과 같은 방법으로 리소스를 좀 더 안전하게 관리할 수 있습니다.

* 하드웨어 기본 설정 옵션을 통해 Windows Hello 비즈니스를 설정합니다. 즉, TPM이 사용 가능한 경우 키가 TPM 1.2 또는 TPM 2.0에서 생성됩니다. TPM을 사용할 수 없는 경우 소프트웨어에서 키를 생성합니다.
* 조직에서 PIN의 복잡성 및 길이와 Hello의 사용 여부 정의
* 인증서 기반 신뢰를 사용하여 스마트 카드와 같은 시나리오를 지원하도록 비즈니스용 Windows Hello를 구성합니다.

## <a name="how-windows-hello-for-business-works"></a>비즈니스용 Windows Hello의 작동 방식
1. 키는 TPM 또는 소프트웨어에 의해 하드웨어에 생성됩니다. 많은 장치에는 장치에 암호화 키를 통합하여 하드웨어를 보호하는 기본 제공된 TPM 칩이 있습니다. TPM 1.2 또는 TPM 2.0은 생성된 키에서 만들어지는 키 또는 인증서를 생성합니다.
2. TPM은 이러한 하드웨어 바인딩된 키를 입증합니다.
3. 단일 잠금 해제 제스처는 장치의 잠금을 해제합니다. 장치가 도메인 또는 Azure AD에 가입되어 있는 경우 이 제스처를 통해 여러 리소스에 액세스할 수 있습니다.

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>비즈니스용 Windows Hello의 수명 주기 작동 방식
![비즈니스용 Windows Hello 수명 주기](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

앞의 다이어그램에서는 개인/공개 키 쌍과 ID 공급자의 유효성 검사를 보여 줍니다. 각 단계는 여기 자세히 설명되어 있습니다.

1. 사용자는 여러 가지 기본 제공 교정 방법(제스처, 실제 스마트 카드, 다단계 인증)을 통해 자신의 ID를 증명하고 이 정보를 Azure Active Directory 또는 온-프레미스 Active Directory와 같은 IDP(ID 공급자)로 보냅니다.
2. 그러면 장치는 키를 만들고, 키를 증명하고, 이 키의 공개 부분을 스테이션 문에 추가하고, 로그인한 후 이 키를 IDP로 보내 등록합니다.
3. IDP가 키의 공개 부분을 등록하자마자 IDP는 장치에 키의 비공개 부분을 사용하여 서명하도록 요구합니다.
4. 그런 다음 IDP는 유효성을 검사하고 인증 토큰을 발급하여 사용자 및 장치가 보호된 리소스에 액세스할 수 있도록 합니다. IDP는 크로스 플랫폼 앱을 작성하거나 JavaScript/Webcrypto API를 통해 브라우저 지원을 사용하여 해당 사용자에 대한 비즈니스용 Windows Hello 자격 증명을 만들고 사용할 수 있습니다.

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>비즈니스용 Windows Hello의 배포 요구 사항
### <a name="at-the-enterprise-level"></a>엔터프라이즈 수준에서
* 엔터프라이즈에 Azure 구독이 있어야 합니다.

### <a name="at-the-user-level"></a>사용자 수준에서
* 사용자 컴퓨터에 Windows 10 Professional 또는 Enterprise가 실행되어야 합니다.

자세한 배포 지침은 [조직에서 비즈니스용 Windows Hello 사용](active-directory-azureadjoin-passport-deployment.md)을 참조하세요.

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

