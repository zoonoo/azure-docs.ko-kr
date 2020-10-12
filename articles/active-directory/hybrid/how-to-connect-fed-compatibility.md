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
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661826"
---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 페더레이션 호환성 목록
Azure Active Directory은 타사 솔루션을 요구 하지 않고 하이브리드 및 클라우드 전용 구현에 대 한 Microsoft 365 및 기타 Microsoft Online services에 대 한 single sign-on 및 향상 된 응용 프로그램 액세스 보안을 제공 합니다. 대부분의 Microsoft 온라인 서비스와 마찬가지로 Microsoft 365는 디렉터리 서비스, 인증 및 권한 부여에 대 한 Azure Active Directory와 통합 됩니다. 또한 Azure Active Directory는 수천 개의 SaaS 애플리케이션 및 온-프레미스 웹 애플리케이션에도 Single Sign-On을 제공합니다. 지원되는 SaaS 애플리케이션에 대한 Azure Active Directory [애플리케이션 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)를 참조하세요. 

## <a name="idp-validation"></a>IDP 유효성 검사
조직에서 타사 페더레이션 솔루션을 사용 하는 경우 타사 페더레이션 솔루션이 Azure Active Directory과 호환 되는 경우 Microsoft 365와 같은 Microsoft Online services를 사용 하 여 온-프레미스 Active Directory 사용자에 대 한 Single Sign-On를 구성할 수 있습니다.  호환성에 대한 질문은 ID 공급자에게 문의하세요.  Microsoft에서 Azure AD와의 호환성에 대해 이전에 테스트 한 id 공급자 목록을 보려면 [AZURE ad id 공급자 호환성 문서](https://www.microsoft.com/download/details.aspx?id=56843)를 참조 하세요. 

>[!NOTE]
>Microsoft는 Azure Active Directory와의 호환성을 위해 독립 ID 공급자에 대한 유효성 검사 테스트를 더 이상 제공하지 않습니다. 상호 운용성을 위해 제품을 테스트하려는 경우 다음 [지침](https://www.microsoft.com/download/details.aspx?id=56843)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory와 온-프레미스 디렉터리 통합](whatis-hybrid-identity.md)
- [Azure AD Connect 및 페더레이션](how-to-connect-fed-whatis.md)
