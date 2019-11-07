---
title: Azure CDN 표준 규칙 엔진을 사용 하 여 HTTPS 적용 | Microsoft Docs
description: 표준 규칙 엔진을 사용 하면 특정 유형의 콘텐츠 배달 차단, 캐싱 정책 정의, HTTP 헤더 수정 등 Microsoft에서 Azure CDN 하 여 HTTP 요청을 처리 하는 방법을 사용자 지정할 수 있습니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615924"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN 표준 규칙 엔진

> [!NOTE]
> 표준 규칙 엔진은 Microsoft에서 Azure CDN 하는 경우에만 사용할 수 있습니다. 

Azure CDN 표준 규칙 엔진을 사용 하 여 HTTP 요청을 처리 하는 방법을 사용자 지정할 수 있습니다. 예를 들어 특정 프로토콜을 통해 콘텐츠 전달을 적용 하거나, 캐싱 정책을 정의 하거나, HTTP 헤더를 수정 합니다. 이 자습서에서는 사용자를 HTTPS로 자동으로 리디렉션하는 규칙을 만드는 방법을 보여 줍니다. 


## <a name="tutorial"></a>자습서

1. Microsoft 프로필에서 Azure CDN의 **CDN 프로필** 페이지에서 규칙을 구성 하려는 끝점을 선택 합니다.
  
2. 왼쪽에서 **규칙 엔진** 탭을 선택 합니다.
   
    전역 규칙을 사용 하 여 규칙 엔진 블레이드가 표시 됩니다. 
   
    [![CDN 새 규칙 페이지](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다.
   >

3. **규칙 추가** 단추를 클릭 하 고 규칙 이름을 제공 합니다. 규칙 이름은 문자로 시작 하 고 숫자와 문자만 포함 해야 합니다.

4. 규칙이 적용되는 요청의 형식을 식별합니다. 드롭다운을 사용 하 여 **요청 프로토콜** 일치 조건을 선택 하 **고 값이** **HTTP**인 경우를 사용 합니다.
   
   [![CDN 규칙 일치 조건](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > 여러 일치 조건이 드롭다운 목록에서 제공됩니다. 일치 조건의 자세한 목록은 [규칙 엔진 일치 조건](cdn-standard-rules-engine-match-conditions.md)을 참조하세요.
   
5. 식별 된 요청에 적용 되는 작업을 선택 합니다. 드롭다운을 사용 하 여 **URL 리디렉션** 작업을 선택 하 고 유형에 대해 발견 된 값 **(302)** 및 프로토콜에 대 한 **HTTPS** 를 사용 합니다. 들어오는 값을 사용 하려면 다른 모든 필드를 비워 둡니다.
   
   [CDN 규칙 작업 ![](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > 드롭다운 목록에서 여러 동작을 사용할 수 있습니다. 자세한 작업 목록은 [규칙 엔진 작업](cdn-standard-rules-engine-actions.md)을 참조 하세요.

6. **저장** 을 선택 하 여 새 규칙을 저장 합니다.  이제 새 규칙을 배포할 수 있습니다.
   
   > [!IMPORTANT]
   > 규칙 변경이 Azure CDN를 통해 전파 되는 데 최대 15 분이 걸릴 수 있습니다.
   >
   

## <a name="see-also"></a>참고 항목

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진 작업](cdn-standard-rules-engine-actions.md)
