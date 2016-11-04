---
title: 'Azure Active Directory Domain Services: 문제 해결 가이드 | Microsoft Docs'
description: Azure AD 도메인 서비스에 대한 문제 해결 가이드
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: maheshu

---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services - 문제 해결 가이드
이 문서는 Azure AD(Active Directory) 도메인 서비스를 설치하거나 관리할 때 발생할 수 있는 문제에 대한 문제 해결 힌트를 제공합니다.

### <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Azure AD 디렉터리에 대해 Azure AD Domain Services를 사용할 수 없습니다.
디렉터리에 대해 Azure AD Domain Services를 사용하도록 설정했지만 실패하거나 '사용 안 함'으로 다시 전환되면 다음 문제 해결 단계를 수행합니다.

* 해당 가상 네트워크에서 동일한 도메인 이름을 사용하는 기존 도메인이 없는지 확인합니다. 예를 들어, 선택한 가상 네트워크에서 'contoso.com'이라는 도메인을 이미 사용한다고 가정합니다. 나중에 해당 가상 네트워크에서 동일한 도메인 이름(즉, 'contoso.com')을 가진 Azure AD Domain Services 관리되는 도메인을 사용하도록 설정하려고 합니다. Azure AD 도메인 서비스를 사용하도록 설정하면 오류가 발생합니다. 이 오류는 해당 가상 네트워크에서 도메인 이름이 충돌하기 때문입니다. 이 경우 다른 이름을 사용하여 Azure AD 도메인 서비스 관리되는 도메인을 설정해야 합니다. 또는 기존 도메인을 프로비전 해제한 후 Azure AD 도메인 서비스를 사용하도록 설정할 수 있습니다.
* Azure AD 디렉터리에서 'Azure AD 도메인 서비스 동기화'라는 이름의 응용 프로그램이 있는지 확인합니다. 이 응용 프로그램이 있는 경우 삭제한 다음 Azure AD Domain Services를 다시 설정해야 합니다. 다음 단계를 수행하여 응용 프로그램이 있는지 확인하고 있는 경우 삭제합니다.
  
  1. **Azure 클래식 포털** ([https://manage.windowsazure.com](https://manage.windowsazure.com))로 이동합니다.
  2. 왼쪽 창에서 **Active Directory** 노드를 선택합니다.
  3. Azure AD 도메인 서비스를 사용하도록 설정할 Azure AD 테넌트(디렉터리)를 선택합니다.
  4. **응용 프로그램** 탭으로 이동합니다.
  5. 드롭다운에서 **회사가 보유한 응용 프로그램** 옵션을 선택합니다.
  6. **Azure AD Domain Services 동기화**라는 응용 프로그램이 있는지 확인합니다. 응용 프로그램이 있는 경우 삭제를 진행합니다.
  7. 응용 프로그램을 삭제한 후에는 Azure AD 도메인 서비스를 다시 사용하도록 설정합니다.

### <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>사용자는 Azure AD 도메인 서비스 관리된 도메인에 로그인할 수 없습니다.
Azure AD 테넌트에서 하나 이상의 사용자가 새로 만든 관리되는 도메인에 로그인할 수 없는 경우 다음 문제 해결 단계를 수행합니다.

* SAMAccountName 형식('CONTOSO\joeuser') 대신 UPN 형식(예: 'joeuser@contoso.com'))을 사용하여 로그인하려고 합니다. 경우에 따라 UPN 접두사가 너무 길거나 관리되는 도메인에 있는 다른 사용자와 같은 사용자의 경우 SAMAccountName이 자동으로 생성될 수 있습니다. UPN 형식은 Azure AD 테넌트 내에서 고유하도록 보장됩니다.

> [!NOTE]
> Azure AD Domain Services 관리되는 도메인에 로그인하는 데 UPN 형식을 사용하는 것이 좋습니다.
> 
> 

* 시작 가이드에 설명된 단계에 따라 [암호 동기화를 사용하도록 설정](active-directory-ds-getting-started-password-sync.md) 했는지 확인합니다.
* **외부 계정:** 영향을 받는 사용자 계정이 Azure AD 테넌트에서 외부 계정이 아닌지 확인합니다. 외부 계정의 예는 Microsoft 계정(예: 'joe@live.com')) 또는 외부 Azure AD 디렉터리에서 사용자 계정을 포함합니다. Azure AD 도메인 서비스에는 이러한 사용자 계정에 대한 자격 증명이 없으므로 이러한 사용자는 관리된 도메인에 로그인할 수 없습니다.
* **동기화된 계정:** 영향을 받는 사용자 계정이 온-프레미스 디렉터리에서 동기화되는 경우 다음을 확인합니다.
  
  * [Azure AD Connect의 최신 권장 사항](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect)으로 배포하거나 업데이트했습니다.
  * [전체 동기화를 수행](active-directory-ds-getting-started-password-sync.md)하도록 Azure AD Connect를 구성했습니다.
  * 디렉터리의 크기에 따라 사용자 계정 및 해시 자격 증명이 Azure AD 도메인 서비스에서 사용할 수 있도록 하는 데 시간이 걸릴 수 있습니다. 인증을 다시 시도하기 전에 충분한 시간 동안 대기합니다(디렉터리 크기에 따라 몇 시간에서 큰 디렉터리는 하루나 이틀까지).
  * 앞의 단계를 확인한 후에도 문제가 지속되면 Microsoft Azure AD Sync 서비스를 다시 시작해 봅니다. 동기화 컴퓨터에서 명령 프롬프트를 시작하고 다음 명령을 실행합니다.
    
    1. net stop 'Microsoft Azure AD Sync'
    2. net start 'Microsoft Azure AD Sync'
* **클라우드 전용 계정**: 영향을 받는 사용자 계정이 클라우드 전용 사용자 계정인 경우 사용자는 Azure AD 도메인 서비스를 사용하도록 설정한 후에 자신의 암호를 변경하도록 합니다. 이 단계를 수행하면 Azure AD 도메인 서비스가 생성되는 데 필요한 자격 증명 해시가 발생합니다.

### <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](active-directory-ds-contact-us.md) Azure Active Directory Domain Services 제품 팀에 문의하세요.

<!--HONumber=Oct16_HO2-->


