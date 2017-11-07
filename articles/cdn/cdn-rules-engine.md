---
title: "Azure CDN 규칙 엔진을 사용하여 HTTP 동작 재정의 | Microsoft Docs"
description: "규칙 엔진을 사용하면 특정 유형의 콘텐츠 전달 차단과 같이 Azure CDN에서 HTTP 요청을 처리하는 방식을 사용자 지정하여 캐싱 정책을 정의하고 HTTP 헤더를 수정할 수 있습니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진을 사용하여 HTTP 동작 재정의
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요
규칙 엔진을 사용하면 특정 유형의 콘텐츠 전달 차단과 같이 HTTP 요청이 처리되는 방식을 사용자 지정하여 캐싱 정책을 정의하고 HTTP 헤더를 수정할 수 있습니다.  이 자습서에서는 CDN 자산의 캐싱 동작을 변경하는 규칙을 만드는 방법을 보여 줍니다.  "[참고 항목](#see-also)" 섹션에는 동영상 콘텐츠도 있습니다.

   > [!TIP] 
   > 구문에 대한 자세한 정보는 [규칙 엔진 참조](cdn-rules-engine-reference.md)를 확인하세요.
   > 


## <a name="tutorial"></a>자습서
1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **HTTP Large** 탭, **규칙 엔진**을 차례로 클릭합니다.
   
    새 규칙에 대한 옵션이 표시됩니다.
   
    ![CDN 새 규칙 옵션](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다.
   > 
   > 
3. **이름/설명** 텍스트 상자에 이름을 입력합니다.
4. 규칙이 적용되는 요청의 유형을 식별합니다.  기본적으로 **항상** 일치 조건이 선택되어 있습니다.  이 자습서에서는 **항상** 을 사용하므로 선택된 상태 그대로 두면 됩니다.
   
   ![CDN 일치 조건](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > 드롭다운 목록에서 사용할 수 있는 다양한 유형의 일치 조건이 있습니다.  일치 조건의 왼쪽에 있는 파란색 정보 아이콘을 클릭하면 현재 선택한 조건을 자세히 설명합니다.
   > 
   >  조건식의 전체 목록에 대한 자세한 정보는 [규칙 엔진 조건식](cdn-rules-engine-reference-match-conditions.md)을 참조하세요.
   >  
   > 일치 조건의 전체 목록을 자세하게 보려면 [규칙 엔진 일치 조건 및 기능](cdn-rules-engine-reference-match-conditions.md)을 참조하세요.
   > 
   > 
5. **기능** 옆에 있는 **+** 단추를 클릭하여 새 기능을 추가합니다.  왼쪽의 드롭다운 목록에서 **Force Internal Max-Age**(내부 Max-Age 강제)를 선택합니다.  표시되는 텍스트 상자에 **300**을 입력합니다.  나머지 기본값을 그대로 둡니다.
   
   ![CDN 기능](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > 일치 조건처럼 새 기능의 왼쪽에 있는 파란색 정보 아이콘을 클릭하면 이 기능에 대한 세부 정보가 표시됩니다.  **Force Internal Max-Age**의 경우 자산의 **Cache-Control** 및 **Expires** 헤더를 재정의하여 CDN 에지 노드가 원본에서 자산을 새로 고치는 시기를 제어합니다.  300초의 경우 원본에서 자산을 새로 고치기 전에 CDN 에지 노드가 자산을 5분 동안 캐시한다는 의미입니다.
   > 
   > 기능의 전체 목록을 자세하게 보려면 [규칙 엔진 기능 세부 정보](cdn-rules-engine-reference-features.md)를 참조하세요.
   > 
   > 
6. **추가** 단추를 클릭하여 새 규칙을 저장합니다.  이제 새 규칙이 승인 대기 중입니다. 승인되면 상태가 **보류 중인 XML**에서 **활성 XML**로 변경됩니다.
   
   > [!IMPORTANT]
   > 규칙 변경이 CDN을 통해 전파되기까지 최대 90분이 소요될 수 있습니다.
   > 
   > 

## <a name="see-also"></a>참고 항목
* [Azure CDN 개요](cdn-overview.md)
* [규칙 엔진 참조](cdn-rules-engine-reference.md)
* [규칙 엔진 일치 조건](cdn-rules-engine-reference-match-conditions.md)
* [규칙 엔진 조건식](cdn-rules-engine-reference-conditional-expressions.md)
* [규칙 엔진 기능](cdn-rules-engine-reference-features.md)
* [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-rules-engine.md)
* [Azure Fridays: Azure CDN의 강력하고 새로운 프리미엄 기능](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (동영상)