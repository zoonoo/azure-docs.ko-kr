---
title: Azure API Management에서 구독을 만드는 방법 | Microsoft Docs
description: Azure API Management에서 구독을 만드는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621737"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Azure API Management에서 구독을 만드는 방법

APIM(Azure API Management)을 통해 API를 게시할 때 이러한 API에 대한 액세스를 보호하는 가장 쉽고 일반적인 방법은 구독 키를 사용하는 것입니다. 즉, 게시된 API를 사용해야 하는 클라이언트 애플리케이션은 이러한 API에 호출할 때 HTTP 요청에 유효한 구독 키를 포함해야 합니다. API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독에 대한 자세한 내용은 [Azure API Management의 구독](api-management-subscriptions.md)을 참조하세요.

이 문서에서는 Azure Portal에서 구독을 만들기 위한 단계를 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음을 수행해야 합니다.

+ [APIM 인스턴스 만들기](get-started-create-service-instance.md)
+ [APIM의 구독](api-management-subscriptions.md) 이해

## <a name="create-a-new-subscription"></a>새 구독 만들기

1. 왼쪽 메뉴에서 **구독**을 클릭합니다.
2. **구독 추가**를 클릭합니다.
3. 구독의 이름을 입력하고 범위를 선택합니다.
4. 페이지 맨 아래에 있는 **저장**

![유연성 있는 구독](./media/api-management-subscriptions/flexible-subscription.png)

구독이 만들어지면 한 쌍의 API 키(기본 및 보조)는 API에 액세스하기 위해 프로비전됩니다.

## <a name="next-steps"></a>다음 단계
API Management에 대한 자세한 내용:

+ API Management의 기타 [개념](api-management-terminology.md)에 대해 알아보기
+ [자습서](import-and-publish.md)에 따라 API Management에 대해 자세히 알아보기
+ 일반적인 질문은 [FAQ 페이지](api-management-faq.md) 확인