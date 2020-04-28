---
title: Azure AD 앱 프록시 및 Qlik Sense | Microsoft Docs
description: Azure Portal에서 애플리케이션 프록시를 설정하고 역방향 프록시에 커넥터를 설치합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79036991"
---
# <a name="application-proxy-and-qlik-sense"></a>애플리케이션 프록시 및 Qlik Sense 
Azure Active Directory 애플리케이션 프록시 및 Qlik Sense를 함께 사용하여 Qlik Sense 배포에 대한 원격 액세스를 제공하기 위해 애플리케이션 프록시를 쉽게 사용할 수 있도록 합니다.  

## <a name="prerequisites"></a>사전 요구 사항 
이 시나리오의 나머지 부분에서는 다음 작업을 완료했다고 가정합니다.
 
- [Qlik Sense](https://community.qlik.com/docs/DOC-19822) 구성 
- [애플리케이션 프록시 커넥터 설치](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Azure에 애플리케이션 게시 
QlikSense를 게시하려면 Azure에서 두 개의 애플리케이션을 게시해야 합니다.  

### <a name="application-1"></a>애플리케이션 #1: 
앱을 게시하려면 다음 단계를 수행합니다. 1 ~ 8 단계에 대한 자세한 연습은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요. 


1. 전역 관리자 권한으로 Azure Portal에 로그인합니다. 
2. **Azure Active Directory** > **엔터프라이즈 응용 프로그램**을 선택 합니다. 
3. 블레이드의 위쪽에서 **추가**를 선택합니다. 
4. **온-프레미스 애플리케이션**을 선택합니다. 
5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다. 
   - **내부 URL**: 이 애플리케이션에는 자체 QlikSense URL인 내부 URL이 있어야 합니다. 예: **https&#58;//demo.qlikemm.com:4244** 
   - **사전 인증 방법**: Azure Active Directory (권장 되지만 필수는 아님) 
1. 블레이드의 아래쪽에서 **추가**를 선택합니다. 애플리케이션이 추가되고 빠른 시작 메뉴가 열립니다. 
2. 빠른 시작 메뉴에서 **테스트할 사용자 지정**을 선택하고 애플리케이션에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 애플리케이션에 대한 액세스 권한이 있는지 확인합니다. 
3. **할당**을 선택하여 테스트 사용자 할당을 저장합니다. 
4. (선택 사항) 앱 관리 블레이드에서 Single Sign-On을 선택합니다. 드롭다운 메뉴에서 **Kerberos 제한된 위임**을 선택하고 Qlik 구성에 따라 필수 필드를 입력합니다. **저장**을 선택합니다. 

### <a name="application-2"></a>애플리케이션 #2: 
다음과 같은 예외가 포함된 애플리케이션 #1과 동일한 단계를 따릅니다. 

**5단계**: 내부 URL은 애플리케이션에서 사용하는 인증 포트를 사용하는 QlikSense URL이어야 합니다. 기본값은 HTTPS의 경우 4244이 고, 4 2018 월 **4244** 일 이전 QlikSense 릴리스의 경우 **4248** 입니다. QlikSense 릴리스에 대 한 기본값은 2018, HTTPS의 경우 **443** , HTTP의 경우 **80** 입니다.  예: **https&#58;//demo.qlik.com:4244**</br></br>
**#10 단계:** SSO를 설정 하지 않고 **Single sign-on을 사용 하지 않도록** 설정 합니다.
 
 
## <a name="testing"></a>테스트 
애플리케이션을 테스트할 준비가 되었습니다. 애플리케이션 #1에 QlikSense를 게시하는 데 사용되는 외부 URL에 액세스하고 두 애플리케이션에 할당된 사용자 권한으로 로그인합니다.  

## <a name="additional-references"></a>추가 참조
응용 프로그램 프록시에 Qlik Sense를 게시 하는 방법에 대 한 자세한 내용은 Qlik 커뮤니티 문서 다음을 참조 하세요. 
- [Qlik Sense를 사용 하는 Kerberos 제한 위임을 사용 하는 Windows 통합 인증을 사용 하는 Azure AD](https://community.qlik.com/docs/DOC-20183)
- [Azure AD 응용 프로그램 프록시와 qlik Sense 통합](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)
- [애플리케이션 프록시 커넥터 작업](application-proxy-connector-groups.md)

