---
title: "쿼리 문자열이 포함된 요청 중 Verizon의 Azure CDN 프리미엄 캐싱 동작 제어 | Microsoft Docs"
description: "Azure CDN 쿼리 문자열 캐싱은 파일에 쿼리 문자열이 포함된 경우 파일이 캐시되는 방법을 제어합니다."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 20efef4e60da6a0ab07eab22ff18e7ae9ab12a30


---
# <a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어 - 프리미엄
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon의 Azure CDN Premium](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>개요
쿼리 문자열 캐싱은 파일에 쿼리 문자열이 들어 있을 때 파일이 캐시되는 방법을 제어합니다.

> [!IMPORTANT]
> 표준 및 프리미엄 CDN 제품은 동일한 쿼리 문자열 캐싱 기능을 제공하지만 사용자 인터페이스는 다릅니다.  이 문서는 **Verizon의 Azure CDN Premium**에 대한 인터페이스를 설명합니다.  **Akamai의 Azure CDN Standard** 및 **Verizon의 Azure CDN Standard**를 사용한 쿼리 문자열 캐싱에 대해서는 [쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어](cdn-query-string.md)를 참조하세요.
> 
> 

다음 세 가지 모드를 사용할 수 있습니다.

* **standard-cache**: 기본 모드입니다.  CDN 에지 노드가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본으로 전달하고 자산을 캐시합니다.  캐시된 자산이 만료될 때까지 에지 노드에서 제공되는 해당 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.
* **no-cache**:이 모드에서 쿼리 문자열이 포함된 요청은 CDN 에지 노드에서 캐시되지 않습니다.  에지 노드가 원본에서 직접 자산을 검색하고 각 요청과 함께 요청자에게 전달합니다.
* **unique-cache**: 이 모드는 쿼리 문자열이 포함된 각 요청을 자체 캐시를 가진 고유한 자산으로 처리합니다.  예를 들어 *foo.ashx?q=bar* 에 대한 요청의 경우 원본에서의 응답이 에지 노드에서 캐시되고 그와 동일한 쿼리 문자열을 가진 후속 캐시에 대해 반환됩니다.  *foo.ashx?q=somethingelse* 에 대한 요청은 자체 TTL(Time To Live)과 함께 별도의 자산으로 캐시됩니다.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Premium CDN 프로필에 대한 쿼리 문자열 캐싱 설정 변경
1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **HTTP Large** 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다.  **Query-String Caching**(쿼리 문자열 캐싱)을 클릭합니다.
   
    쿼리 문자열 캐싱 옵션이 표시됩니다.
   
    ![CDN 쿼리 문자열 캐싱 옵션](./media/cdn-query-string-premium/cdn-query-string.png)
3. 선택한 후 **업데이트** 단추를 클릭합니다.

> [!IMPORTANT]
> 등록이 CDN 전체에 전파되기까지 시간이 걸리기 때문에, 설정 변경은 즉시 보이지 않을 수 있습니다.  <b>Verizon의 Azure CDN</b> 프로필의 경우 일반적으로 90분 이내에 전파가 완료되지만 더 오래 소요될 수도 있습니다.
> 
> 




<!--HONumber=Nov16_HO3-->


