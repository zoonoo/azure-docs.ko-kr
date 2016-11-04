---
title: 쿼리 문자열이 포함된 요청의 Azure CDN 캐싱 동작 제어 | Microsoft Docs
description: Azure CDN 쿼리 문자열 캐싱은 파일에 쿼리 문자열이 포함된 경우 파일이 캐시되는 방법을 제어합니다.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: casoper

---
# 쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Verizon의 Azure CDN Premium](cdn-query-string-premium.md)
> 
> 

## 개요
쿼리 문자열 캐싱은 파일에 쿼리 문자열이 들어 있을 때 파일이 캐시되는 방법을 제어합니다.

> [!IMPORTANT]
> 표준 및 프리미엄 CDN 제품은 동일한 쿼리 문자열 캐싱 기능을 제공하지만 사용자 인터페이스는 다릅니다. 이 문서는 **Akamai의 Azure CDN Standard** 및 **Verizon의 Azure CDN Standard**에 대한 인터페이스를 설명합니다. **Verizon의 Azure CDN Premium**을 사용한 쿼리 문자열 캐싱은 [쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어 - 프리미엄](cdn-query-string-premium.md)을 참조하세요.
> 
> 

다음 세 가지 모드를 사용할 수 있습니다.

* **쿼리 문자열 무시**: 기본 모드입니다. CDN 에지 노드가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본으로 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 에지 노드에서 제공되는 해당 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.
* **쿼리 문자열이 포함된 URL에 대한 캐싱 바이패스**:이 모드에서 쿼리 문자열이 포함된 요청은 CDN 에지 노드에서 캐시되지 않습니다. 에지 노드가 원본에서 직접 자산을 검색하고 각 요청과 함께 요청자에게 전달합니다.
* **모든 고유한 URL 캐시**: 이 모드는 쿼리 문자열이 포함된 각 요청을 자체 캐시를 가진 고유한 자산으로 처리합니다. 예를 들어 *foo.ashx?q=bar*에 대한 요청의 경우 원본에서의 응답이 에지 노드에서 캐시되고 그와 동일한 쿼리 문자열을 가진 후속 캐시에 대해 반환됩니다. *foo.ashx?q=somethingelse*에 대한 요청은 자체 TTL(Time To Live)과 함께 별도의 자산으로 캐시됩니다.

## Standard CDN 프로필에 대한 쿼리 문자열 캐싱 설정 변경
1. CDN 프로필 블레이드에서 관리하려는 CDN 끝점을 클릭합니다.
   
    ![CDN 프로필 블레이드 끝점](./media/cdn-query-string/cdn-endpoints.png)
   
    CDN 끝점 블레이드가 열립니다.
2. **구성** 단추를 클릭합니다.
   
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-query-string/cdn-config-btn.png)
   
    CDN 구성 블레이드가 열립니다.
3. **쿼리 문자열 캐싱 동작** 드롭다운에서 설정을 선택합니다.
   
    ![CDN 쿼리 문자열 캐싱 옵션](./media/cdn-query-string/cdn-query-string.png)
4. 선택한 후 **저장** 단추를 클릭합니다.

> [!IMPORTANT]
> 등록이 CDN 전체에 전파되기까지 시간이 걸리기 때문에, 설정 변경은 즉시 보이지 않을 수 있습니다. <b>Akamai의 Azure CDN</b> 프로필의 경우 일반적으로 1분 이내에 전파가 완료됩니다. <b>Verizon의 Azure CDN</b> 프로필의 경우 일반적으로 90분 이내에 전파가 완료되지만 더 오래 소요될 수도 있습니다.
> 
> 

<!---HONumber=AcomDC_0803_2016-->