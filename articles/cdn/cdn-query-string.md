---
title: "쿼리 문자열을 사용하여 Azure Content Delivery Network 캐싱 동작 제어 | Microsoft Docs"
description: "Azure CDN 쿼리 문자열 캐싱은 파일에 쿼리 문자열이 포함된 경우 파일이 캐시되는 방식을 제어합니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 04c9ad5e58af073204eb6a16df96f0517a0ee668
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>쿼리 문자열을 사용하여 Azure Content Delivery Network 캐싱 동작 제어
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon의 Azure CDN Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>개요
Azure CDN(Content Delivery Network)을 사용하면 쿼리 문자열이 포함된 웹 요청에 대해 파일이 캐시되는 방식을 제어할 수 있습니다. 쿼리 문자열이 있는 웹 요청에서 쿼리 문자열은 물음표(?) 다음에 나오는 요청 부분입니다. 쿼리 문자열은 필드 이름 및 해당 값이 등호(=)로 구분된 하나 이상의 키-값 쌍을 포함할 수 있습니다. 각 키-값 쌍은 앰퍼샌드(&)로 구분됩니다. 예: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. 요청의 쿼리 문자열에 둘 이상의 키-값 쌍이 있는 경우 해당 순서는 중요하지 않습니다. 

> [!IMPORTANT]
> 표준 및 프리미엄 CDN 제품은 동일한 쿼리 문자열 캐싱 기능을 제공하지만 사용자 인터페이스는 다릅니다.  이 문서는 **Akamai의 Azure CDN Standard** 및 **Verizon의 Azure CDN Standard**에 대한 인터페이스를 설명합니다. **Verizon의 Azure CDN Premium**을 사용한 쿼리 문자열 캐싱은 [쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어 - 프리미엄](cdn-query-string-premium.md)을 참조하세요.

세 가지 쿼리 문자열 모드를 사용할 수 있습니다.

- **쿼리 문자열 무시**: 기본 모드입니다. 이 모드에서는 CDN 에지 노드가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본으로 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 에지 노드에서 제공되는 해당 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.
- **쿼리 문자열에 대한 캐싱 우회**:이 모드에서 쿼리 문자열이 포함된 요청은 CDN 에지 노드에서 캐시되지 않습니다. 에지 노드가 원본에서 직접 자산을 검색하고 각 요청과 함께 요청자에게 전달합니다.
- **각 고유한 URL 캐시**: 이 모드에서는 쿼리 문자열을 포함하여 고유한 URL이 있는 각 요청이 고유 캐시가 있는 고유 자산으로 처리됩니다. 예를 들어 `example.ashx?q=test1`에 대한 요청의 경우 원본에서의 응답이 에지 노드에서 캐시되고 그와 동일한 쿼리 문자열을 가진 후속 캐시에 대해 반환됩니다. `example.ashx?q=test2`에 대한 요청은 자체 TTL(Time To Live) 설정과 함께 별도의 자산으로 캐시됩니다.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Standard CDN 프로필에 대한 쿼리 문자열 캐싱 설정 변경
1. CDN 프로필을 연 다음 관리할 CDN 끝점을 선택합니다.
   
   ![CDN 프로필 끝점](./media/cdn-query-string/cdn-endpoints.png)
   
2. 설정에서 **캐시**를 클릭합니다.
   
    ![CDN 프로필 캐시 단추](./media/cdn-query-string/cdn-cache-btn.png)
   
3. **쿼리 문자열 캐시 동작** 목록에서 쿼리 문자열 모드를 선택한 다음 **저장**을 클릭합니다.
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> 등록이 CDN 전체에 전파되기까지 시간이 걸리기 때문에, 캐시 문자열 설정 변경이 즉시 표시되지 않을 수 있습니다. **Akamai의 Azure CDN** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. **Verizon의 Azure CDN** 프로필의 경우 일반적으로 90분 이내에 전파가 완료되지만 더 오래 소요될 수도 있습니다.


