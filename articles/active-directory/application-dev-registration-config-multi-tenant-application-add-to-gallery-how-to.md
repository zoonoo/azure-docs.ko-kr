---
title: "Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 추가하는 방법 | Microsoft Docs"
description: "Azure AD 응용 프로그램 갤러리에서 사용자 지정 개발 다중 테넌트 응용 프로그램을 나열하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 969273d1bc0750685a0b2010670915f4183ac8ed
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 추가하는 방법

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리란?

Azure AD 응용 프로그램 갤러리는 수백만 명의 Azure Active Directory 고객에게 응용 프로그램을 제공하여 Marketplace에서 응용 프로그램의 영향과 범위를 넓히는 좋은 방법입니다. 아래 단계에서는 Azure AD 응용 프로그램 갤러리에 응용 프로그램을 나열하는 방법을 설명합니다.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>응용 프로그램이 SAML 또는 OpenIDConnect를 지원하는 경우
Azure AD 응용 프로그램 갤러리에 나열하려는 다중 테넌트 응용 프로그램이 있는 경우 먼저 응용 프로그램이 다음 Single Sign-On 기술 중 하나를 지원하는지 확인해야 합니다.

1. **OpenID Connect** - 인증을 위한 OpenID Connect 및 구성을 위한 Azure AD 동의 API를 사용하는 Azure AD과 통합을 지원합니다. 통합을 시작하고 응용 프로그램이 SAML을 지원하지 않으면 이것이 권장 모드입니다.
2. **SAML** – 응용 프로그램에는 이미 SAML 프로토콜을 사용하여 타사 ID 공급자를 구성할 기능이 있습니다.

응용 프로그램에서 이러한 Single Sign-On 모드 중 하나를 지원하고 Azure AD 응용 프로그램 갤러리에 다중 테넌트 응용 프로그램을 나열하려는 경우 아래 문서의 단계를 수행할 수 있습니다. 빠르게 시작하려면 **waadpartners@microsoft.com**으로 메일을 보내세요.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>응용 프로그램이 SAML 또는 OpenIDConnect를 지원하지 않는 경우
응용 프로그램이 이러한 모드 중 하나를 지원하지 않더라도 암호 Single Sign-On 기술을 사용하여 갤러리에 통합할 수 있습니다. 이 옵션을 살펴보려면 **waadpartners@microsoft.com**으로 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 응용 프로그램 갤러리에 응용 프로그램을 나열하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
