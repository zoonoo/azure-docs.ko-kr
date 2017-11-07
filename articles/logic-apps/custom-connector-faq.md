---
title: "사용자 지정 커넥터 FAQ - Azure Logic Apps | Microsoft Docs"
description: "사용자 지정 커넥터 만들기에 대한 요구 사항, 트리거 등에 대한 FAQ"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>FAQ: 사용자 지정 커넥터

## <a name="requirements"></a>요구 사항

**Q:** REST API 없이 커넥터를 빌드할 수 있나요? </br>
**A:** 아니요, 커넥터를 빌드하려면 서비스에 안정적인 HTTP REST API를 지원해야 합니다. 

**Q:** 커넥터를 만드는 데 어떤 도구를 사용할 수 있습니까? </br>
**A:** Azure는 호스팅, API Management 등을 위한 Azure App Service와 같은 API로 모든 서비스를 노출하는 데 사용할 수 있는 기능과 서비스를 제공합니다.

**Q:** 어떤 인증 유형이 지원되나요? </br>
**A:** 다음과 같이 지원되는 인증 표준을 사용할 수 있습니다.

* [OAuth 2.0](https://oauth.net/2/)([Azure Active Directory](https://azure.microsoft.com/develop/identity/) 또는 SalesForce, GitHub, Dropbox 등의 특정 서비스 포함)
* 제네릭 OAuth 2.0
* [기본 인증](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [API 키](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>트리거

**Q:** 웹후크 없이 트리거를 빌드할 수 있나요? </br>
**A:** 아니요, Azure Logic Apps 및 Microsoft Flow용 사용자 지정 커넥터는 웹후크 기반 트리거만 지원합니다. 구현을 위해 다른 패턴을 요청하려면 API에 대한 자세한 내용과 함께 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)에 문의하십시오.

## <a name="certification"></a>인증

**Q**: 저는 Microsoft 파트너 또는 ISV(독립 소프트웨어 공급업체)가 아닙니다. 그래도 커넥터를 만들 수 있나요? </br>
**A**: 예, 조직에서 내부 용도로 이러한 커넥터를 등록할 수 있지만 커넥터를 인증하고 공개적으로 릴리스하려면 기본 서비스를 소유하거나 API를 사용할 수 있는 명시적인 권한을 제시해야 합니다.

## <a name="other"></a>기타

**Q:** 내 API는 동적 호스트를 사용합니다. OpenAPI로 어떻게 구현하나요? </br>
**A:** 사용자 지정 커넥터는 동적 호스트를 지원하지 않습니다. 대신 개발 및 테스트 목적으로 정적 호스트를 사용하십시오. 커넥터를 인증하려면 동적 구현에 대해 Microsoft 담당자에게 문의하십시오.

**Q:** Postman Collection V2가 지원되나요? </br>
**A:** 아니요, 현재 Postman Collection V1만 지원됩니다.

**Q:** OpenAPI 3.0이 지원되나요? </br>
**A:** 아니요, 현재 OpenAPI 2.0만 지원됩니다.