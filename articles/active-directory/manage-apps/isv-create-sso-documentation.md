---
title: 응용 프로그램에 대 한 Single Sign-On 설명서 만들기 & 게시
description: Azure Active Directory와의 통합에 대 한 독립 소프트웨어 공급 업체 지침
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955984"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>응용 프로그램에 대 한 Single Sign-On 문서 만들기 및 게시   

## <a name="documentation-on-your-site"></a>사이트에 대 한 설명서

도입의 용이성은 엔터프라이즈 소프트웨어 결정의 중요 한 요소입니다. 따르기 쉬운 설명서는 도입 과정에서 고객을 지원 하 고 지원 비용을 절감 합니다. Microsoft는 수천 개의 소프트웨어 공급 업체와 협력 하 여 작동 하는 것을 살펴보았습니다.

사이트에 대 한 설명서는 최소한 다음 항목을 포함 하는 것이 좋습니다.

* SSO 기능 소개

  * 지원되는 프로토콜

  * 버전 및 SKU

  * 설명서 링크를 사용 하 여 지원 되는 Id 공급자 목록

* 응용 프로그램에 대 한 라이선스 정보

* SSO 구성을 위한 역할 기반 액세스 제어

* SSO 구성 단계

  * 공급자의 예상 값을 포함 하는 SAML의 UI 구성 요소

  * Id 공급자에 전달할 서비스 공급자 정보입니다.

* OIDC/OAuth 인 경우

  * 비즈니스 근거 승인에 필요한 사용 권한 목록

* 파일럿 사용자를 위한 테스트 단계

* 오류 코드 및 메시지를 비롯 한 문제 해결 정보

* 고객을 위한 지원 메커니즘

## <a name="documentation-on-the-microsoft-site"></a>Microsoft 사이트 설명서

응용 프로그램을 Azure Marketplace에 게시 하는 Azure Active Directory 응용 프로그램 갤러리를 사용 하 여 응용 프로그램을 나열 하는 경우 Microsoft는 단계별 프로세스를 설명 하는 상호 고객에 대 한 설명서를 생성 합니다. [여기](https://aka.ms/appstutorial)에서 예제를 확인할 수 있습니다. 이 설명서는 갤러리에 대 한 제출에 따라 만들어지며, GitHub 계정을 사용 하 여 응용 프로그램을 변경 하는 경우 쉽게 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 응용 프로그램 갤러리에 응용 프로그램 나열](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
