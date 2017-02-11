---
title: "Office 365 사용자 계정으로 Azure RemoteApp을 사용하는 방법 | Microsoft Docs"
description: "Office 365 사용자 계정으로 Azure RemoteApp을 사용하는 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: aba70fd3-60b0-4b44-9321-1ab18760ccd5
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8744c5a133738fc60b86e7abd4b0d4b1cb28314c


---
# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Office 365 사용자 계정으로 Azure RemoteApp을 사용하는 방법
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

Office 365 구독이 있는 경우 Office 365 서비스에 액세스하는 데 사용되는 사용자 이름 및 암호를 저장하는 Azure Active Directory가 있습니다. 예를 들어 사용자가 Office 365 ProPlus를 활성화하면 라이선스를 확인하기 위해 Azure AD에 대해 인증이 수행됩니다. 대부분의 고객은 Azure RemoteApp과 동일한 디렉터리를 사용하기를 원합니다.

Azure RemoteApp을 배포하는 경우 대부분 다른 Azure AD와 연결된 Azure 구독을 사용할 가능성이 높습니다. Office 365 디렉터리를 사용하려면 Azure 구독을 이 디렉터리로 이동해야 합니다.

Office 365 클라이언트 응용 프로그램을 배포하는 방법에 대한 자세한 내용은 [Azure RemoteApp으로 Office 365 구독을 사용하는 방법](remoteapp-officesubscription.md)을 참조하세요.

## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>단계 1: 무료 Office 365 Azure Active Directory 구독 등록
Azure 클래식 포털을 사용하는 경우 [무료 Azure Active Directory 구독 등록](https://technet.microsoft.com/library/dn832618.aspx)의 단계에 따라 Azure 관리 포털을 통해 Azure AD에 대한 관리 액세스 권한을 얻습니다. 이 프로세스를 완료하면 Azure 포털에 로그인하여 사용자의 디렉터리가 있는 것을 확인할 수 있습니다. 이 시점에서는 Azure RemoteApp과 함께 사용하는 전체 Azure 구독이 다른 디렉터리에 있으므로 다른 항목은 보이지 않습니다.

이 단계에서 만든 관리자 계정의 이름과 암호는 2단계에서 필요하므로 기억해 두어야 합니다.

Azure 포털을 사용하는 경우 [Office 365 포털을 사용하여 무료 Azure Active Directory를 등록 및 활성화하는 방법](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/)을 확인하세요.

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>단계 2: Azure 구독과 연결된 Azure AD를 변경합니다.
Azure 구독의 현지 디렉터리를 1단계에서 작업한 Office 365 디렉터리로 변경합니다.

[Azure RemoteApp에서 Azure Active Directory 테넌트 변경](remoteapp-changetenant.md)에 설명된 지침을 다릅니다. 다음 단계에 특히 주의합니다.

* 1단계: 이 구독에서 ARA(Azure RemoteApp)를 배포한 경우 계속 진행하기 전에 먼저 ARA 컬렉션에서 모든 Azure AD 사용자 계정을 제거해야 합니다. 또는 기존 컬렉션을 삭제할 수도 있습니다.
* 2단계: 이 단계가 매우 중요합니다. 구독에서 Microsoft 계정(예: @outlook.com)을 서비스 관리자로 사용해야 합니다. 구독에 연결된 기존 Azure AD에서는 사용자 계정을 사용할 수 없기 때문입니다. 이 계정을 사용하는 경우 다른 Azure AD로 이동할 수 없습니다.
* 4단계: 기존 디렉터리를 추가할 때 해당 디렉터리에 대한 관리자 계정으로 로그인하라는 메시지가 표시됩니다. 단계 1의 관리자 계정을 사용해야 합니다.
* 5단계: 구독의 부모 디렉터리를 Office 365 디렉터리로 변경합니다. 그러면 설정 -> 구독 아래에 Office 365 디렉터리가 나열됩니다. 
  ![구독의 부모 디렉터리 변경](./media/remoteapp-o365user/settings.png)

이제 Azure RemoteApp 구독이 Office 365 Azure AD와 연결되었습니다. Azure RemoteApp에서 기존 Office 365 사용자 계정을 사용할 수 있습니다.




<!--HONumber=Dec16_HO2-->


