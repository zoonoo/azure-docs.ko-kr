---
title: "Azure Logic Apps 통합 계정 메타데이터 | Microsoft Docs"
description: "통합 계정 메타데이터 개요"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 4faf01ca10f263a5ecc18f51659b5004bf4f7c36
ms.openlocfilehash: 47a081e04c30f6d2c6c5e92cf82085e2d56478cf


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Azure Logic Apps 통합 계정 메타데이터 

## <a name="overview"></a>개요

파트너, 규약, 스키마, 통합 계정에 추가된 맵, 키-값 쌍이 포함된 저장소 메타데이터 런타임 동안 검색할 수 있는 사용자 지정 메타데이터를 정의할 수 있습니다.  현재 아티팩트에는 UI에서 메타데이터를 만들 수 있는 기능이 없으므로 REST API를 사용하여 만들 수 있습니다.  파트너, 규약 및 스키마의 **JSON으로 편집** 기능을 사용하여 메타데이터 정보를 입력할 수 있습니다.  Logic Apps에서 **통합 계정 아티팩트 조회**는 메타데이터 정보를 검색하는 데 도움이 됩니다.

## <a name="how-to-store-metadata"></a>메타데이터를 저장하는 방법 

1. [통합 계정](app-service-logic-enterprise-integration-create-integration-account.md) 만들기   

2. 통합 계정에 [파트너](app-service-logic-enterprise-integration-partners.md#how-to-create-a-partner) 또는 [규약](app-service-logic-enterprise-integration-agreements.md#how-to-create-agreements) 또는 [스키마](app-service-logic-enterprise-integration-schemas.md) 추가

3. 파트너, 규약 또는 스키마 선택 **JSON으로 편집**을 선택하고 메타데이터 정보 입력    
![메타데이터 입력](./media/app-service-logic-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Logic Apps에서 **통합 계정 아티팩트 조회** 호출

1. [Logic Apps](app-service-logic-create-a-logic-app.md) 만들기

2. Logic Apps를 통합 계정에 [연결](app-service-logic-enterprise-integration-create-integration-account.md#how-to-link-an-integration-account-to-a-logic-app)    

3. 예를 들어 **통합 계정 아티팩트 조회**를 검색하기 전에 *요청* 또는 *HTTP*를 사용하여 트리거를 만듭니다.  **통합**을 검색하여 **통합 계정 아티팩트 조회** 
![검색 조회](./media/app-service-logic-enterprise-integration-metadata/image2.png) 찾기 

3. **통합 계정 아티팩트 조회** 선택  

4. **아티팩트 유형**을 선택하고 **아티팩트 이름** 제공  
![검색 조회](./media/app-service-logic-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>파트너 메타데이터 검색 예제 

1. 파트너 메타데이터에는 라우팅 URL에 세부 정보가 포함되어 있음    
![검색 조회](./media/app-service-logic-enterprise-integration-metadata/image6.png)

2. Logic Apps에서 **통합 계정 아티팩트 조회** 및 **HTTP**  구성  
![검색 조회](./media/app-service-logic-enterprise-integration-metadata/image4.png)

3. URI를 검색하려는 경우 코드 보기는 다음과 같습니다.    
![검색 조회](./media/app-service-logic-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>다음 단계
* [규약에 대해 자세히 알아보기](app-service-logic-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  


<!--HONumber=Nov16_HO4-->


