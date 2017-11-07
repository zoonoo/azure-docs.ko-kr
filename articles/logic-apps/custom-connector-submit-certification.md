---
title: "사용자 지정 커넥터 인증 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps, Microsoft Flow 및 Microsoft PowerApps에서 모든 사용자가 커넥터를 사용할 수 있게 만들기"
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
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Microsoft 인증을 위해 커넥터 제출

Azure Logic Apps, Microsoft Flow 및 Microsoft PowerApps의 모든 사용자가 사용자 지정 커넥터를 공개적으로 사용할 수 있도록 하려면 게시를 위한 검토, 유효성 검사 및 승인을 위해 커넥터를 Microsoft에 제출하십시오. 

## <a name="certification-criteria"></a>인증 조건

| 기능 | 세부 정보 | 필수 또는 권장 |
|------------|---------|-------------------------|
| SaaS(Software-as-a-Service) 앱 | Logic Apps, Flow 및 PowerApps에 적합한 사용자 시나리오를 충족합니다. | 필수 |
| 인증 유형 | API가 OAuth2, API 키 또는 기본 인증을 지원해야 합니다. | 필수 | 
| 지원 | 고객이 도움을 받을 수 있도록 지원 담당자를 제공해야 합니다. | 필수 | 
| 가용성 및 작동 시간 | 앱의 작동 시간은 99.9% 이상입니다. | 권장 | 
|||| 

또한 Microsoft 파트너나 ISV(독립 소프트웨어 공급업체)가 아닌 경우 커넥터를 인증하고 공개적으로 릴리스하려면 기본 서비스를 소유하거나 API를 사용할 수 있는 명시적인 권한을 제시해야 합니다.

## <a name="validation-phases"></a>유효성 검사 단계

Microsoft는 커넥터를 평가할 때 다음 유효성 검사 단계를 사용합니다.

| 유효성 검사 단계 | 설명 | 
| ----- | ----------- |
| 기능 | Microsoft는 다음 오류에 대해 Logic Apps, Flow 및 PowerApps로 커넥터를 테스트합니다. <p>- 사용자 지정 커넥터 마법사의 정의 섹션에 나타나는 잘못된 OpenAPI(Swagger) 또는 JSON 오류 <p>- 사용자 지정 커넥터 마법사의 테스트 섹션에 나타나는 런타임 및 스키마 유효성 검사 오류 | 
| Content | Microsoft는 커넥터의 각 엔터티에 친숙한 이름과 설명이 사용되는지 확인합니다. 커넥터 Swagger의 각 작업, 입력 매개 변수 및 응답 특성에 다음 요소가 있는지 확인하십시오. <p>- [요약](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [설명](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [표시 유형 정보](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>인증을 위해 Microsoft에 커넥터를 추천 및 제출

인증을 신청하려면 다음 단계를 따르십시오.

1. **추천**

   1. [추천을 제출합니다](https://go.microsoft.com/fwlink/?linkid=848754).

      1. 페이지 아래쪽에서 **문의처**를 선택합니다.
      2. 양식을 채우고 **ISV 커넥터 프로그램 및 공동 마케팅에 대한 질문**을 선택합니다.
      3. **제출**을 선택합니다.

   2. 수신한 비밀 유지 계약 및 파트너 규약에 서명합니다. 

      계속하려면 이러한 계약에 서명을 해야 합니다. 
      그런 다음 커넥터가 인증 조건을 충족하는지 확인할 수 있습니다. 
   
   3. 커넥터가 승인되면 Microsoft에서 온보딩을 위한 지침을 알려줍니다.
    
2. **검토**

   1. 검토를 위해 다음 정보를 추천 담당자에게 보냅니다.

      * API를 설명하는 OpenAPI 파일
      * 커넥터를 나타내는 아이콘 파일(.png 또는 .jpg) 
      
        아이콘의 로고는 230픽셀 사각형 내에 ~ 160픽셀까지 허용됩니다. 
        컬러 배경에 흰색 로고를 사용하는 것이 좋습니다.
      
      * 16진수 형식의 아이콘 브랜드 색(아이콘 파일의 컬러 배경과 일치해야 함)

      * 유효성 검사를 위한 테스트 계정
      * 지원 연락처

   2. 추가 정보가 필요하면 연락 드리겠습니다.

3. **게시**

    커넥터의 기능 및 콘텐츠 유효성을 검사한 후에는 모든 제품 및 지역에 배포할 수 있도록 커넥터를 준비합니다.
    
    기본적으로 모든 커넥터는 "프리미엄" 커넥터로 게시됩니다. 
    Azure로 앱을 빌드한 경우 모든 Office 365 Enterprise 요금제 사용자가 사용할 수 있는 "표준" 커넥터에 나열되도록 신청할 수 있습니다. 
    자세한 내용은 추천 담당자에게 문의하십시오.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)
* [사용자 지정 커넥터 개요](../logic-apps/custom-connector-overview.md)