---
title: "Azure Active Directory 조건부 액세스 | Microsoft Docs"
description: "Azure Active Directory에서 조건부 액세스 제어를 사용하여 응용 프로그램에 대한 액세스를 인증할 때 특정 조건을 확인합니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 31659a7394a34cf8fb5b6ff11c955bdc9f8b65bb
ms.lasthandoff: 03/28/2017


---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 조건부 액세스

> [!div class="op_single_selector"]
> * [Azure 클래식 포털](active-directory-conditional-access.md)
> * [Azure Portal](active-directory-conditional-access-azure-portal.md)

Azure AD(Azure Active Directory)의 조건부 액세스 제어 기능은 클라우드 및 온-프레미스에서 보안 리소스를 보호하는 간단한 방법을 제공합니다. 다단계 인증과 같은 조건부 액세스 정책을 사용하면 자격 증명 도난 및 피싱 위험을 방지할 수 있습니다. 다른 조건부 액세스 정책을 사용하면 조직의 데이터를 안전하게 보관할 수 있습니다. 예를 들어 자격 증명을 요구하는 것 외에도 Microsoft Intune과 같은 모바일 장치 관리 시스템에 등록된 장치만 조직의 중요한 서비스에 액세스할 수 있는 정책이 있을 수도 있습니다.

## <a name="prerequisites"></a>필수 조건
Azure AD 조건부 액세스는 [Azure Active Directory Premium](http://www.microsoft.com/identity)의 한 기능입니다. 조건부 액세스 정책이 적용된 응용 프로그램에 액세스하는 각 사용자에게는 Azure AD Premium 라이선스가 있어야 합니다. [허가되지 않은 사용자 보고서](https://aka.ms/utc5ix)에서 라이선스 요구 사항에 대해 자세히 알아볼 수 있습니다.

## <a name="how-is-conditional-access-control-enforced"></a>조건부 액세스 제어는 어떻게 적용되나요?
준비된 조건부 액세스 제어를 사용하면 Azure AD에서 사용자가 응용 프로그램에 액세스하도록 설정한 특정 조건을 확인합니다. 이러한 액세스 요구 사항이 충족되면 사용자가 인증되고 응용 프로그램에 액세스할 수 있습니다.  

![조건부 액세스 개요](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>조건
조건부 액세스 정책에 포함할 수 있는 조건은 다음과 같습니다.

* **그룹 멤버 자격** - 그룹 멤버 자격에 따라 사용자의 액세스를 제어합니다.
* **위치** - 사용자 위치를 사용하여 다단계 인증을 트리거하고 사용자가 신뢰할 수 있는 네트워크에 없는 경우 차단 제어를 사용합니다.
* **장치 플랫폼** - 정책 적용 조건으로 iOS, Android, Windows Mobile 및 Windows와 같은 장치 플랫폼을 사용합니다.
* **장치 사용** - 정책 평가 시 장치의 사용 여부에 관계없이 장치 상태의 유효성을 검사합니다. 디렉터리에서 분실되거나 도난당한 장치를 사용하지 못하게 하면 더 이상 정책 요구 사항을 충족할 수 없습니다.
* **로그인 및 사용자 위험** - 조건부 액세스 위험 정책에는 [Azure AD ID 보호](active-directory-identityprotection.md)를 사용할 수 있습니다. 조건부 액세스 위험 정책을 사용하면 조직에서 위험 이벤트 및 비정상적인 로그인 활동에 기반한 사전 예방 조치를 취할 수 있습니다.

## <a name="controls"></a>컨트롤
조건부 액세스 정책을 적용하는 데 사용할 수 있는 제어는 다음과 같습니다.

* **다단계 인증** - 다단계 인증을 통해 강력한 인증을 요구할 수 있습니다. Azure Multi-factor Authentication을 통하거나 AD FS(Active Directory Federation Services)와 결합된 온-프레미스 다단계 인증 공급자를 통해 다단계 인증을 사용할 수 있습니다. 다단계 인증을 사용하면 유효한 사용자의 자격 증명에 액세스 할 수 있는 권한이 없는 사용자가 리소스에 액세스하지 못하도록 방지할 수 있습니다.
* **차단** - 사용자 위치와 같은 조건을 적용하여 사용자 액세스를 차단할 수 있습니다. 예를 들어 사용자가 신뢰할 수 있는 네트워크에 있지 않을 경우 액세스를 차단할 수 있습니다.
* **준수 장치** - 장치 수준에서 조건부 액세스 정책을 설정할 수 있습니다. 도메인 가입 컴퓨터 또는 모바일 장치 관리 응용 프로그램에 등록된 모바일 장치만 조직의 리소스에 액세스할 수 있도록 정책을 설정할 수 있습니다. 예를 들어 Intune을 사용하여 장치의 정책 준수를 확인한 다음 사용자가 응용 프로그램에 액세스하려고 할 때 해당 정책을 적용하기 위해 Azure AD에 보고할 수 있습니다. Intune을 사용하여 앱 및 데이터를 보호하는 방법에 대한 자세한 지침은 [Microsoft Intune을 사용하여 앱 및 데이터 보호](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune)를 참조하세요. 또한 Intune을 사용하여 분실되거나 도난당한 장치에 대한 데이터 보호도 적용할 수 있습니다. 자세한 정보는 [Microsoft Intune을 사용하여 전체 또는 선택적 초기화로 데이터 보호](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)를 참조하세요.

## <a name="applications"></a>응용 프로그램
응용 프로그램 수준에서 조건부 액세스 정책을 적용할 수 있습니다. 클라우드 또는 온-프레미스 응용 프로그램 및 서비스에 대한 액세스 수준을 설정합니다. 정책은 웹 사이트 또는 서비스에 직접 적용됩니다. 브라우저 및 서비스에 액세스하는 응용 프로그램에 대한 액세스 정책이 적용됩니다.

## <a name="device-based-conditional-access"></a>장치 기반 조건부 액세스
Azure AD에 등록되어 있고 특정 조건을 충족하는 장치에서 응용 프로그램에 대한 액세스를 제한할 수 있습니다. 장치 기반 조건부 액세스는 다음과 같은 장치의 리소스에 액세스하려는 사용자로부터 조직의 리소스를 보호합니다.

* 알 수 없거나 관리되지 않는 장치
* 조직에서 설정한 보안 정책을 충족하지 않는 장치

다음 요구 사항을 기반으로 하여 정책을 설정할 수 있습니다.

* **도메인 가입 장치** - 온-프레미스 Active Directory 도메인에 가입되어 있고 Azure AD에 등록된 장치에 대한 액세스를 제한하는 정책을 설정합니다. 이 정책은 Windows 데스크톱, 랩톱 및 엔터프라이즈 태블릿에 적용됩니다.
  Azure AD에서 도메인 가입 장치를 자동으로 등록하도록 설정하는 방법에 대한 자세한 내용은 [Windows 도메인 가입 장치에 대한 Azure Active Directory 자동 등록 설정](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.
* **준수 장치** - 관리 시스템 디렉터리에서 **준수**로 표시된 장치에 대한 액세스를 제한하는 정책을 설정합니다. 이 정책을 설정하면 장치에서 파일 암호화를 적용하는 등 보안 정책을 준수하는 장치만 액세스가 허용됩니다. 이 정책을 사용하여 다음 장치의 액세스를 제한할 수 있습니다.
  
  * **Windows 도메인 가입 장치** - 하이브리드 구성에 배포된 System Center Configuration Manager(현재 분기)에서 관리합니다.
  * **Windows 10 모바일 회사 또는 개인 장치** - Intune 또는 지원되는 타사 모바일 장치 관리 시스템에서 관리합니다.
  * **iOS 및 Android 장치** - Intune에서 관리합니다.

장치 기반 인증 기관 정책으로 보호되는 응용 프로그램에 액세스하는 사용자는 이 정책 요구 사항을 충족하는 장치에서 응용 프로그램에 액세스해야 합니다. 정책 요구 사항을 충족하지 않는 장치에서 액세스하려고 하면 액세스가 거부됩니다.

Azure AD에서 장치 기반 인증 기관 정책을 구성하는 방법에 대한 자세한 내용은 [Azure Active Directory 연결 응용 프로그램의 장치 기반 조건부 액세스 정책 설정](active-directory-conditional-access-policy-connected-applications.md)을 참조하세요.

## <a name="resources"></a>리소스
조직의 조건부 액세스 설정에 대한 자세한 내용은 다음에 나오는 리소스 범주 및 항목을 참조하세요.

### <a name="multi-factor-authentication-and-location-policies"></a>다단계 인증 및 위치 정책
* [그룹, 위치 및 다단계 인증 정책에 따라 Azure AD 연결 앱에 대한 조건부 액세스 시작](active-directory-conditional-access-azuread-connected-apps.md)Azure Active Directory 조건부 액세스 시작
* [지원되는 응용 프로그램 및 브라우저](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>장치 기반 조건부 액세스
* [Azure Active Directory 연결 응용 프로그램의 액세스 제어를 위한 장치 기반 조건부 액세스 정책 설정](active-directory-conditional-access-policy-connected-applications.md)
* [Windows 도메인 가입 장치에 대한 Azure Active Directory 자동 등록 설정](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Azure Active Directory 액세스 문제 해결](active-directory-conditional-access-device-remediation.md)
* [Microsoft Intune을 사용하여 분실되거나 도난당한 장치의 데이터 보호](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>로그인 위험에 기반한 리소스 보호
* [Azure AD ID 보호](active-directory-identityprotection.md)

### <a name="next-steps"></a>다음 단계
* [조건부 액세스 FAQ](active-directory-conditional-faqs.md)
* [기술 참조](active-directory-conditional-access-technical-reference.md)


