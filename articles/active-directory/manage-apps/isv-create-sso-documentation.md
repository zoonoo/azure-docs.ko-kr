---
title: 응용 프로그램에 & 단일 사인온 문서 게시
description: Azure Active Directory와의 통합에 대한 독립 소프트웨어 공급업체에 대한 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232275"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>응용 프로그램에 대한 단일 사인온 문서 작성 및 게시   

## <a name="documentation-on-your-site"></a>사이트에 대한 문서

채택의 용이성은 엔터프라이즈 소프트웨어 의사 결정의 중요한 요소입니다. 명확한 따라하기 쉬운 문서는 채택 여정에서 고객을 지원하고 지원 비용을 절감합니다. 수천 개의 소프트웨어 공급업체와 협력하여 Microsoft는 어떤 것이 작동하는지 보았습니다.

최소한 사이트에 있는 설명서에는 다음 항목이 포함되어 두는 것이 좋습니다.

* SSO 기능 소개

  * 지원되는 프로토콜

  * 버전 및 SKU

  * 지원되는 ID 공급자 목록(문서 링크 포함)

* 응용 프로그램에 대한 라이선스 정보

* SSO 구성을 위한 역할 기반 액세스 제어

* SSO 구성 단계

  * 공급자의 예상 값이 있는 SAML용 UI 구성 요소

  * ID 공급자에게 전달할 서비스 공급자 정보

* OIDC/오귀스

  * 비즈니스 적분과 동의에 필요한 권한 목록

* 파일럿 사용자를 위한 테스트 단계

* 오류 코드 및 메시지를 포함한 문제 해결 정보

* 고객을 위한 지원 메커니즘

## <a name="documentation-on-the-microsoft-site"></a>마이크로소프트 사이트에 설명서

응용 프로그램을 Azure Marketplace에 게시하는 Azure Active Directory 응용 프로그램 갤러리를 통해 나열하면 Microsoft는 상호 고객을 위한 설명서를 생성하여 단계별 프로세스를 설명합니다. [여기에서](https://aka.ms/appstutorial)예제를 볼 수 있습니다. 이 설명서는 갤러리에 제출한 내용을 기반으로 작성되며 GitHub 계정을 사용하여 응용 프로그램을 변경하는 경우 쉽게 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 응용 프로그램 갤러리에 응용 프로그램 나열](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)