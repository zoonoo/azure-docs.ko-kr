---
title: Azure API Management 정책 샘플 - X-CSRF 패턴 구현 | Microsoft Docs
description: Azure API Management 정책 샘플 - 많은 API에서 사용되는 X-CSRF 패턴을 구현하는 방법을 보여 줍니다. 이 예제는 SAP 게이트웨이에만 해당됩니다.
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
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946026"
---
# <a name="implement-x-csrf-pattern"></a>X-CSRF 패턴 구현

이 문서에서는 많은 API에서 사용되는 X-CSRF 패턴을 구현하는 방법을 보여 주는 Azure API Management 정책 샘플을 제공합니다. 이 예제는 SAP 게이트웨이에만 해당됩니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-samples.md)

