---
title: 특성 매핑에 대 한 Azure AD에 동기화 | Microsoft Docs
description: Azure AD에 온-프레미스 Active Directory에서 특성을 동기화 하는 방법에 알아봅니다. SaaS 앱에 사용자 프로 비전을 구성할 때 기본적으로 동기화 되지 않은 원본 특성을 추가할 디렉터리 확장 기능을 사용 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9460bc924ea662646360d1a3f5087949a39a03f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65806118"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>응용 프로그램에 프로 비전을 위한 Azure AD로 특성을 온-프레미스 Active Directory에서 동기화

사용자 프로 비전 특성 매핑 사용자 지정 하는 경우 볼 수 있습니다 매핑할 특성에 나타나지 않도록 합니다 **원본 특성** 목록입니다. 이 문서에서는 Azure Active Directory (Azure AD)에 온-프레미스 Active Directory (AD)에서 동기화 하 여 누락 된 특성을 추가 하는 방법을 보여 줍니다.

Azure AD는 SaaS 앱에 Azure AD에서 사용자 계정을 프로 비전 할 때 사용자 프로필을 만드는 데 필요한 모든 데이터를 포함 해야 합니다. 경우에 따라 데이터를 사용할 수 있도록 해야 동기화 할 수 있습니다 특성 온-프레미스에서 AD에서 Azure AD로 합니다. Azure AD Connect는 Azure AD에 특정 특성이 있지만 일부 특성에 자동으로 동기화 합니다. 또한 기본적으로 동기화 되는 몇 가지 특성 (예: SAMAccountName)는 Azure AD Graph API를 통해 노출 될 수 있습니다. 이러한 경우에 Azure AD로 특성을 동기화 하려면 Azure AD Connect 디렉터리 확장 기능을 사용할 수 있습니다. 이런 방식으로 특성은 Azure AD Graph API 및 Azure AD 프로 비전 서비스를 볼 수 있습니다.

필요한 데이터를 프로 비전을 위한 Active directory 하지만 위에서 설명한 이유로 인해 프로 비전에 사용할 수 없는 경우 다음이 단계를 수행 합니다.
 
## <a name="sync-an-attribute"></a>특성 동기화 

1. Azure AD Connect 마법사를 열고, 작업을 선택한 다음 **동기화 옵션 사용자 지정**합니다.

   ![Azure Active Directory Connect 마법사 추가 작업 페이지](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD 전역 관리자로 로그인 합니다. 

3. 에 **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화**합니다.
 
   ![Azure Active Directory Connect 마법사 옵션 기능 페이지](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD로 확장 하려는 특성을 선택 합니다.
   > [!NOTE]
   > 검색 **사용 가능한 특성** 대 소문자를 구분 합니다.

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect 마법사를 완료 하 고 실행 하는 전체 동기화 주기를 허용 합니다. 주기가 완료 되 면 스키마를 확장 하 고 새 값은 사이 동기화 온-프레미스 AD와 Azure AD입니다.
 
6. 머무르 시는 동안 Azure portal의 [사용자 특성 매핑 편집](customize-application-attributes.md)서 **원본 특성** 목록 형식으로 추가 된 특성이 포함 됩니다 `<attributename> (extension_<appID>_<attributename>)`합니다. 특성을 선택 하 고 프로 비전을 위한 대상 응용 프로그램에 매핑하십시오.

   ![Azure Active Directory Connect 마법사 디렉터리 확장 선택 페이지](media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 참조 특성을 프로 비전 할 수 온-프레미스 AD와 같은 **managedby** 하거나 **DN/DistinguishedName**, 현재 지원 되지 않습니다. 이 기능을 요청할 수 있습니다 [사용자 의견](https://feedback.azure.com/forums/169401-azure-active-directory)합니다. 

## <a name="next-steps"></a>다음 단계

* [프로 비전 범위에 있는 사용자 정의](define-conditional-rules-for-provisioning-user-accounts.md)
