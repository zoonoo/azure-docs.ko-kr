---
title: Azure API Managment 정책 샘플 - 응답 콘텐츠 필터링 | Microsoft Docs
description: Azure API Managment 정책 샘플 - 요청과 연결된 제품에 따라 응답 페이로드에서 데이터 요소를 필터링하는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: af362ac51fb8b7d1689451d49f2ed831c5f9ee2e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284430"
---
# <a name="filter-response-content"></a>응답 콘텐츠 필터링

이 문서에서는 요청과 연결된 제품에 따라 응답 페이로드에서 데이터 요소를 필터링하는 방법을 설명하는 Azure API Managment 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **아웃바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-samples.md)

