---
title: "Azure AD에서 LinkedIn 통합 구성 | Microsoft Docs"
description: "Azure Active Directory에서 Microsoft 앱용 LinkedIn 통합을 사용하거나 사용하지 않도록 설정하는 방법에 대해 설명합니다."
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>Azure Active Directory에서 LinkedIn 통합 사용
LinkedIn 통합을 사용하면 사용자가 선택할 경우 Microsoft 앱 내의 개인 LinkedIn 네트워크 및 공용 LinkedIn 데이터에 모두 액세스할 수 있습니다. 각 사용자가 독립적으로 선택하여 LinkedIn 계정에 회사 계정을 연결할 수 있습니다.

### <a name="linkedin-integration-from-your-end-users-perspective"></a>최종 사용자의 관점에서 LinkedIn 통합
조직의 최종 사용자가 자신의 LinkedIn 계정을 회사계정에 연결하면 조직 내부 및 외부의 작업자를 더 잘 식별할 수 있습니다. 두 계정을 연결하면 사용자의 LinkedIn 연결 데이터 및 프로필 데이터가 조직의 Microsoft 앱에서 사용될 수 있지만 해당 데이터를 공유할 LinkedIn에 대한 권한을 제거하여 언제든지 옵트아웃할 수 있습니다. 또한 통합은 사용 가능한 프로필 정보를 공개적으로 사용하며 사용자가 LinkedIn 개인 정보 설정을 통해 Microsoft 응용 프로그램에서 공개 프로필 및 네트워크 정보를 공유할지 여부를 선택할 수 있습니다.

>[!NOTE]
> Azure AD에서 LinkedIn 통합을 사용하도록 설정하면 앱 및 서비스에서 최종 사용자의 정보 중 일부에 액세스할 수 있습니다. Azure AD에서 LinkedIn 통합 사용 시 개인정보 취급 방침에 대한 자세한 내용은 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/privacystatement/)을 참조하세요. 

## <a name="enable-linkedin-integration"></a>LinkedIn 통합 사용
Azure AD에서 엔터프라이즈에 대한 LinkedIn 통합은 기본적으로 사용됩니다. 따라서 테넌트에 대해 이 기능을 사용할 수 있으면 조직의 모든 사용자가 LinkedIn 기능 및 프로필을 볼 수 있습니다. LinkedIn 통합을 사용하면 조직의 사용자가 Microsoft 서비스 내에서 LinkedIn 기능을 사용할 수 있습니다(예: Outlook에서 메일 수신 시 프로필 보기). 이 기능을 사용하지 않도록 설정하면 LinkedIn 기능에 액세스할 수 없으며 사용자 계정 연결이 중지되고 Microsoft 서비스를 통해 조직과 LinkedIn 간의 데이터 공유가 중지됩니다.

> [!IMPORTANT]
> 이 기능은 지역, 독립적 및 정부 테넌트에 사용할 수 없습니다. 또한 LinkedIn 통합 기능과 같은 Azure AD 서비스 업데이트는 모든 Azure 테넌트에 동시에 배포되지 않습니다. LinkedIn 통합이 Azure 테넌트에 출시될 때까지는 Azure AD와 LinkedIn 통합을 사용할 수 없습니다.

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Active Directory 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
2. **사용자 및 그룹**을 선택합니다.
3. **사용자 및 그룹** 블레이드에서 **사용자 설정**을 선택합니다.
4. **LinkedIn 통합**에서 예 또는 아니요를 선택하여 LinkedIn 통합을 사용 또는 사용하지 않도록 설정합니다.
   ![LinkedIn 통합 사용](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>자세한 정보 
* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>다음 단계
다음 링크를 사용하여 Azure Portal에서 Azure AD와 LinkedIn 통합을 사용 또는 사용하지 않도록 설정할 수 있습니다.

[LinkedIn 통합 구성](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 
