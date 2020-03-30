---
title: 매핑을 위해 Azure AD에 특성 동기화 | 마이크로 소프트 문서
description: 온-프레미스 Active Directory에서 Azure AD로 특성을 동기화하는 방법을 알아봅니다. SaaS 앱에 사용자 프로비전을 구성할 때 디렉터리 확장 기능을 사용하여 기본적으로 동기화되지 않은 소스 특성을 추가합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522274"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>온-프레미스 Active Directory에서 응용 프로그램에 프로비전하기 위해 Azure AD에 특성 동기화

사용자 프로비저닝을 위해 특성 매핑을 사용자 지정할 때 매핑하려는 특성이 **소스 특성** 목록에 나타나지 않을 수 있습니다. 이 문서에서는 누락된 특성을 AD(온-프레미스 활성 디렉터리)에서 Azure Active Directory(Azure AD)로 동기화하여 추가하는 방법을 보여 줍니다.

Azure AD에는 Azure AD에서 SaaS 앱으로 사용자 계정을 프로비전할 때 사용자 프로필을 만드는 데 필요한 모든 데이터가 포함되어야 합니다. 경우에 따라 데이터를 사용할 수 있도록 하려면 온-프레미스 AD에서 Azure AD로 특성을 동기화해야 할 수 있습니다. Azure AD Connect는 특정 특성을 Azure AD에 자동으로 동기화하지만 모든 특성은 동기화하지 않습니다. 또한 기본적으로 동기화되는 일부 특성(예: SAMAccountName)은 Microsoft 그래프 API를 사용하여 노출되지 않을 수 있습니다. 이러한 경우 Azure AD Connect 디렉터리 확장 기능을 사용하여 특성을 Azure AD에 동기화할 수 있습니다. 이렇게 하면 특성이 Microsoft 그래프 API 및 Azure AD 프로비저닝 서비스에 표시됩니다.

프로비저닝에 필요한 데이터가 Active Directory에 있지만 위에서 설명한 이유로 인해 프로비저닝에 사용할 수 없는 경우 다음 단계를 따르십시오.
 
## <a name="sync-an-attribute"></a>특성 동기화 

1. Azure AD 연결 마법사를 열고 작업을 선택한 다음 **동기화 옵션 사용자 지정을**선택합니다.

   ![Azure Active Directory 연결 마법사 추가 작업 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD 글로벌 관리자로 로그인합니다. 

3. 선택적 **기능** 페이지에서 **디렉터리 확장 특성 동기화를**선택합니다.
 
   ![Azure Active Directory 연결 마법사 선택적 기능 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD로 확장할 특성을 선택합니다.
   > [!NOTE]
   > 사용 가능한 특성 아래의 검색은 대/소문자를 **구분합니다.**

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect 마법사를 완료하고 전체 동기화 주기를 실행할 수 있습니다. 주기가 완료되면 스키마가 확장되고 온-프레미스 AD와 Azure AD 간에 새 값이 동기화됩니다.
 
6. Azure 포털에서 사용자 특성 [매핑을 편집하는](customize-application-attributes.md)동안 **Source 특성** 목록에 형식에 `<attributename> (extension_<appID>_<attributename>)`추가된 특성이 포함됩니다. 특성을 선택하고 프로비저닝을 위한 대상 응용 프로그램에 매핑합니다.

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> **관리용** 또는 **DN/고유 이름과**같은 온-프레미스 AD의 참조 특성을 프로비전하는 기능은 현재 지원되지 않습니다. [사용자 음성](https://feedback.azure.com/forums/169401-azure-active-directory)에서 이 기능을 요청할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [프로비저닝 범위에 있는 사람 정의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
