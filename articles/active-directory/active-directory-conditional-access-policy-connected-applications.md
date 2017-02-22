---
title: "Azure Active Directory 연결 응용 프로그램의 장치 기반 조건부 액세스 정책 설정 | Microsoft Docs"
description: "Azure AD 연결 응용 프로그램에 대한 장치 기반 조건부 액세스를 설정합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133


---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Azure Active Directory 연결 응용 프로그램의 장치 기반 조건부 액세스 정책 설정
Azure AD(Azure Active Directory) 장치 기반 조건부 액세스를 사용하면 다음으로부터 조직의 리소스를 보호할 수 있습니다.

* 알 수 없거나 관리되지 않는 장치에서 시도하는 액세스
* 조직의 보안 정책을 준수하지 않는 장치

조건부 액세스에 대한 개요는 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.

장치 기반 조건부 액세스 정책을 설정하여 보호할 수 있는 응용 프로그램은 다음과 같습니다.

* Office 365 SharePoint Online(조직의 사이트와 문서 보호)
* Office 365 Exchange Online(조직의 전자 메일 보호)
* Azure AD 인증에 연결되는 SaaS(Software as a Service)
* Azure AD 응용 프로그램 프록시 서비스를 통해 게시되는 온-프레미스 응용 프로그램

Azure Portal에서 장치 기반 조건부 액세스 정책을 설정하려면 디렉터리의 특정 응용 프로그램으로 이동합니다.

  ![Azure Portal 디렉터리의 응용 프로그램 목록](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")

응용 프로그램을 선택한 다음 **구성** 탭을 클릭하여 조건부 액세스 정책을 설정합니다.  

  ![응용 프로그램 구성](./media/active-directory-conditional-access-policy-connected-applications/02.png "Device based access rules")

장치 기반 조건부 액세스 정책을 설정하려면 **장치 기반 액세스 규칙** 섹션의 **액세스 규칙 사용**에서 **설정**을 선택합니다.

장치 기반 조건부 액세스 정책의 세 가지 구성 요소는 다음과 같습니다.

* **적용 대상** - 정책이 적용되는 사용자의 범위입니다.
* **장치 규칙** - 응용 프로그램에 액세스하기 전에 장치에서 충족해야 하는 조건입니다.
* **응용 프로그램 적용** - 정책이 적용되는 클라이언트 응용 프로그램(네이티브 대 브라우저)입니다.
  
  ![장치 기반 액세스 정책의 세 가지 구성 요소](./media/active-directory-conditional-access-policy-connected-applications/03.png "Device based access rules")

## <a name="select-the-users-the-policy-applies-to"></a>정책이 적용되는 사용자 선택
**적용 대상** 섹션에서 이 정책이 적용되는 사용자 범위를 선택할 수 있습니다.

사용자에 대한 액세스 정책 범위를 만드는 경우 다음 두 가지 옵션이 제공됩니다.

* **모든 사용자** - 응용 프로그램에 액세스하는 모든 사용자에게 정책을 적용합니다.
* **그룹** - 특정 그룹에 속한 사용자에 대해서만 정책을 적용하도록 제한합니다.

![모든 사용자 또는 그룹에 대한 정책 적용](./media/active-directory-conditional-access-policy-connected-applications/11.png "Apply to")

 정책에서 사용자를 제외하려면 **제외** 확인란을 선택합니다. 이는 특정 사용자가 일시적으로 응용 프로그램에 액세스할 수 있도록 권한을 부여해야 하는 경우에 유용합니다. 예를 들어 일부 사용자가 조건부 액세스에 준비되지 않은 장치를 사용하는 경우 이 옵션을 선택합니다. 이러한 장치로는 아직 등록되지 않았거나 조건을 준수하지 않는 장치일 수 있습니다.

## <a name="select-the-conditions-that-devices-must-meet"></a>장치에서 준수해야 하는 조건 선택
**장치 규칙**을 사용하여 장치에서 응용 프로그램에 액세스할 수 있도록 허용하는 조건을 설정합니다.

이러한 장치 종류에 대한 장치 기반 조건부 액세스를 설정할 수 있는 장치 유형은 다음과 같습니다.

* Windows 10 1주년 업데이트, Windows 8.1 및 Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 및 Windows Server 2008 R2
* iOS 장치(iPad, iPhone)
* Android 장치

Mac은 곧 지원될 예정입니다.

  ![장치에 정책 적용](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

> [!NOTE]
> 도메인 가입과 Azure AD 가입의 차이점에 대한 자세한 내용은 [작업 공간에서 Windows 10 장치 사용](active-directory-azureadjoin-windows10-devices.md)을 참조하세요.
> 
> 

장치 규칙에 대한 두 가지 옵션이 있습니다.

* **모든 장치가 준수해야 합니다.** - 응용 프로그램에 액세스하는 모든 장치 플랫폼에서 정책을 준수해야 합니다. 장치 기반 조건부 액세스를 지원하지 않는 플랫폼에서 실행되는 장치의 액세스는 거부됩니다.
* **선택한 장치만 준수해야 합니다.** - 특정 장치 플랫폼에서만 정책을 준수해야 합니다. 선택하지 않은 다른 플랫폼 또는 응용 프로그램에 액세스할 수 있는 다른 플랫폼의 액세스는 허용됩니다.
  
  ![장치 규칙의 범위 설정](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

Intune 또는 Azure AD와 통합된 타사 모바일 장치 관리 시스템의 디렉터리에서 **준수**로 표시된 Azure AD 가입 장치는 해당 정책을 준수합니다.

도메인 가입 장치에서 해당 정책을 준수하는 경우는 다음과 같습니다.

* Azure AD에 등록된 경우 - 대부분의 조직에서 도메인 가입 장치는 신뢰할 수 있는 장치로 간주합니다.
* System Center Configuration Manager의 Azure AD에서 **준수**로 표시됩니다.
  
  ![준수하고 있는 도메인 가입 장치](./media/active-directory-conditional-access-policy-connected-applications/06.png "Device Rules")

Intune 또는 Azure AD와 통합된 타사 모바일 장치 관리 시스템의 디렉터리에서 **준수**로 표시된 Windows 개인 장치는 해당 정책을 준수합니다.

Intune의 디렉터리에서 **준수**로 표시된 비 Windows 장치는 해당 정책을 준수합니다.

> [!NOTE]
> 다른 관리 시스템에서 장치 준수를 위해 Azure AD를 설정하는 방법에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.
> 
> 

장치 기반 액세스 정책에 대해 하나 이상의 장치 플랫폼을 선택할 수 있습니다. 여기에는 Android, iOS, Windows Mobile(Windows 8.1 휴대폰 및 태블릿) 및 Windows(모든 Windows 10 장치를 포함하여 다른 모든 Windows 장치)가 포함됩니다.
선택한 플랫폼에 대해서만 정책 평가가 수행됩니다. 액세스를 시도하는 장치에서 선택한 플랫폼 중 하나를 실행하지 않더라도 사용자에게 액세스 권한이 있으면 장치에서 해당 응용 프로그램에 액세스할 수 있습니다. 어떤 장치 정책도 평가되지 않습니다.

![장치 규칙을 위한 플랫폼 선택](./media/active-directory-conditional-access-policy-connected-applications/07.png "Device Rules")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>응용 프로그램 유형별 정책 평가 설정
**응용 프로그램 적용** 섹션에서 정책을 통해 사용자 또는 장치 액세스에 대해 평가할 응용 프로그램의 유형을 설정합니다.

포함될 응용 프로그램 유형에 대한 두 가지 옵션은 다음과 같습니다.

* 브라우저 및 네이티브 응용 프로그램용
* 네이티브 응용 프로그램 전용

![브라우저 또는 네이티브 응용 프로그램 선택](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

응용 프로그램에 대한 액세스 정책을 적용하려면 **브라우저 및 네이티브 응용 프로그램용**을 선택합니다. 그런 후에 다음 응용 프로그램을 포함할 수 있습니다.

* 브라우저(예: Windows 10의 Microsoft Edge 또는 iOS의 Safari)
* 모든 플랫폼에서 ADAL(Active Directory Authentication Library)을 사용하는 응용 프로그램 또는 Windows 10에서 WAM(WebAccountManager) API를 사용하는 응용 프로그램

> [!NOTE]
> 장치 기반 인증 기관 보호 응용 프로그램에 액세스하는 사용자에 대한 브라우저 지원 및 기타 고려 사항은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)를 참조하세요.
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Exchange ActiveSync 기반 응용 프로그램의 전자 메일 액세스 보호
Office 365 Exchange Online 응용 프로그램에서 Exchange ActiveSync를 사용하면 Exchange ActiveSync 기반 메일 응용 프로그램에 대한 전자 메일 액세스를 차단할 수 있습니다.

![Exchange ActiveSync 준수 장치 옵션](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![전자 메일에 액세스하려면 준수 장치가 필요](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory 조건부 액세스](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO5-->


