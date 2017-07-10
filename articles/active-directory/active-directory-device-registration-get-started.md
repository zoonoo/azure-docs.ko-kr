---
title: "Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법 | Microsoft Docs"
description: "Azure Active Directory에서 Windows 도메인 가입 장치를 자동으로 등록하도록 설정합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.contentlocale: ko-kr
ms.lasthandoff: 03/10/2017


---
<a id="get-started-with-azure-active-directory-device-registration" class="xliff"></a>
# Azure Active Directory 장치 등록 시작

Azure Active Directory 장치 등록은 장치 기반 조건부 액세스 시나리오의 기초입니다. 장치가 등록될 경우 Azure Active Directory 장치 등록은 사용자가 로그인할 때 장치를 인증하는 데 사용되는 ID로 장치를 제공합니다. 그런 다음 인증된 장치 및 그 장치의 특성을 사용하여 클라우드 및 온-프레미스에 호스트되는 응용 프로그램에 조건부 액세스 정책을 적용할 수 있습니다.

Microsoft Intune과 같은 MDM(모바일 장치 관리) 솔루션과 함께 사용할 경우 Azure Active Directory의 장치 특성이 장치에 대한 추가 정보로 업데이트됩니다. 이렇게 하면 장치의 액세스를 적용하여 보안 및 규정 준수에 대한 표준을 충족하는 조건부 액세스 규칙을 만들 수 있습니다. Microsoft Intune에서 장치를 등록하는 방법에 대한 자세한 내용은 Intune에서 관리에 대해 장치 등록을 참조하세요.
Azure Active Directory Device Registration으로 사용할 수 있는 시나리오. Azure Active Directory 장치 등록에서는 iOS, Android 및 Windows 장치를 지원합니다. Azure AD 장치 등록을 활용하는 개별 시나리오에는 보다 구체적인 요구 사항 및 플랫폼 지원이 있을 수도 있습니다. 

이러한 시나리오는 다음과 같습니다.

- **Microsoft Intune을 사용한 Office 365 응용 프로그램에 대한 조건부 액세스:** IT 관리자는 조건부 액세스 장치 정책을 프로비전하여 규격 장치를 사용하는 정보 근로자가 서비스에 액세스할 수 있도록 하는 동시에 회사 리소스를 보호할 수 있습니다. 자세한 내용은 Office 365 서비스에 대한 조건부 액세스 장치 정책을 참조하세요.

- **온-프레미스에서 호스트되는 응용 프로그램에 대한 조건부 액세스:** Windows Server 2012 R2에서 AD FS를 사용하도록 구성된 응용 프로그램에 대한 액세스 정책이 있는 등록된 장치를 사용할 수 있습니다. 온-프레미스에 대해 조건부 액세스를 설정하는 방법에 대한 자세한 내용은 [Azure Active Directory 장치 등록을 사용하여 온-프레미스 조건부 액세스 설정](active-directory-device-registration-on-premises-setup.md)을 참조하세요.

<a id="setting-up-azure-active-directory-device-registration" class="xliff"></a>
## Azure Active Directory Device Registration 설정

장치 등록을 설정하려면 다음과 같은 여러 옵션을 사용할 수 있습니다.

- 장치는 Azure AD 도메인에 조인될 경우 등록될 수 있습니다. 이 항목에 대한 자세한 내용은 Azure AD 조인 및 사용자가 Azure AD 도메인에 조입하는 데 필요한 설정을 알아보세요.

- 사용자가 회사 또는 학교 계정을 개인 장치의 Windows에 추가하거나 모바일 장치가 등록을 요구하는 작업 리소스에 연결될 때 장치가 등록될 수 있습니다. 이 작업이 수행되려면 Azure Portal에서 Azure AD 장치 등록을 사용하도록 설정해야 합니다. 

- 기존 도메인 조인 컴퓨터에 대해서는 자동 장치 등록을 사용하여 장치를 등록할 수 있습니다. 먼저 Azure AD Connect를 설정해야만 자동 장치 등록이 진행될 수 있습니다.

최신 지침을 보려면 [Windows 도메인 가입 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.  
Azure Active Directory에서 관리자 포털을 사용하여 등록된 장치를 보고 사용하거나 사용하지 않도록 설정할 수도 있습니다.

<a id="enable-the-azure-active-directory-device-registration-service" class="xliff"></a>
## Azure Active Directory 장치 등록 서비스 사용

**Azure Active Directory 장치 등록 서비스를 사용하도록 설정하려면**

1.  관리자 권한으로 Microsoft Azure Portal에 로그인합니다.

2.  왼쪽 창에서 **Active Directory**를 선택합니다.

3.  디렉터리 탭에서 해당 디렉터리를 선택합니다.

4.  **Configure**를 클릭합니다.

5.  **장치**로 스크롤합니다.

6.  “사용자가 장치를 Azure AD에 등록할 수 있습니다.”에 대해 모두를 선택합니다.

7.  사용자당 권한을 부여하려는 최대 장치 수를 선택합니다.

Office 365에 대한 모바일 장치 관리 및 Microsoft Intune에 등록하려면 장치가 등록되어야 합니다. 이러한 서비스 중 하나를 구성한 경우 **모두**가 선택되고 **없음** 단추는 사용할 수 없습니다. 이러한 서비스가 올바르게 구성되어 있는지와 적절한 라이선스가 있는지 확인하세요.

기본적으로 2단계 인증은 서비스에 대해 사용되지 않습니다. 그러나 장치를 등록하는 경우 2단계 인증을 사용하는 것이 좋습니다.

- 이 서비스에 대해 2단계 인증을 요구하기 전에 Azure Active Directory에서 2단계 인증 공급자를 구성하고 Multi-Factor Authentication에 대해 사용자 계정을 구성해야 합니다. Azure Active Directory에 Multi-Factor Authentication 추가를 참조하세요.

- Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS에서 2단계 인증 모듈을 구성해야 합니다. Active Directory Federation Services로 Multi-Factor Authentication 사용을 참조하세요.

<a id="view-and-manage-device-objects-in-azure-active-directory" class="xliff"></a>
## Azure Active Directory에서 장치 개체 보기 및 관리

Azure 관리자 포털에서 장치를 보고 차단 및 차단 해제할 수 있습니다. 차단된 장치는 등록된 장치만 허용하도록 구성된 응용 프로그램에 더 이상 액세스할 수 없습니다.

**Azure Active Directory에서 장치 개체를 보고 관리하려면**
 
1.  관리자 권한으로 Microsoft Azure Portal에 로그온합니다.

2.  왼쪽 창에서 **Active Directory**를 선택합니다.

3.  디렉터리를 선택합니다.

4.  **사용자**를 선택합니다. 

5.  장치를 보도록 허용할 사용자를 클릭합니다.

6.  **장치**를 선택합니다.

7.  **등록된 장치**를 선택합니다.

이제 사용자가 등록한 장치를 보거나 차단 또는 차단 해제할 수 있습니다.
온-프레미스 도메인에 조인되고 자동으로 등록된 Windows 10 장치가 사용자 탭에 표시되지 않습니다. 모든 엔터프라이즈 장치를 찾으려면 Get-MsolDevice PowerShell 명령을 사용하세요. 


<a id="next-steps" class="xliff"></a>
## 다음 단계

자동 장치 등록을 설정하려면 [Windows 도메인 조인 장치의 Azure Active Directory 자동 등록을 구성하는 방법](active-directory-conditional-access-automatic-device-registration-setup.md)을 참조하세요.



