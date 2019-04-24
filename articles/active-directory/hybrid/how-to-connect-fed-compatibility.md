---
title: Azure AD 페더레이션 호환성 목록
description: 이 페이지에 Single Sign-On을 구현하는 데 사용할 수 있는 타사 ID 공급자가 있습니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f5090101c486562e33de56402db348c6038c8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244754"
---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 페더레이션 호환성 목록
Azure Active Directory에서는 임의 타사 솔루션을 요구하지 않고 Office 365용 Single Sign-On과 강화된 애플리케이션 액세스 보안 및 하이브리드와 클라우드 전용 구현에 대한 기타 Microsoft Online Services를 제공합니다. 대부분의 Microsoft Online Services와 마찬가지로 Office 365는 디렉터리 서비스, 인증 및 권한 부여에 대해 Azure Active Directory와 통합되어 있습니다. 또한 Azure Active Directory는 수천 개의 SaaS 애플리케이션 및 온-프레미스 웹 애플리케이션에도 Single Sign-On을 제공합니다. 지원되는 SaaS 애플리케이션에 대한 Azure Active Directory [애플리케이션 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)를 참조하세요. 

## <a name="idp-validation"></a>IDP 유효성 검사
조직에서 타사 페더레이션 솔루션을 사용하는 경우 Office 365와 같은 Microsoft Online 서비스로 온-프레미스 Active Directory 사용자에 대한 Single Sign-On을 구성할 수 있습니다. 제공되는 타사 페더레이션 솔루션은 Azure Active Directory와 호환됩니다.  호환성에 대한 질문은 ID 공급자에게 문의하세요.  Microsoft에서 Azure AD와의 호환성에 대해 이전에 테스트한 ID 공급자의 목록을 보려는 경우 [여기](https://www.microsoft.com/download/details.aspx?id=56843)를 클릭합니다. 

>[!NOTE]
>Microsoft는 Azure Active Directory와의 호환성에 대해 독립적인 ID 공급자를 테스트하는 유효성 검사를 더 이상 제공하지 않습니다. 상호 운용성을 위해 제품을 테스트하려는 경우 다음 [지침](https://www.microsoft.com/download/details.aspx?id=56843)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory와 온-프레미스 디렉터리 통합](whatis-hybrid-identity.md)
- [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)
