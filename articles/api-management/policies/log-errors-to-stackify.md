---
title: Azure API 관리 정책 샘플 - 로깅을 위해 Stackify에 오류 보내기 | Microsoft Docs
description: Azure API 관리 정책 샘플 - 오류 로깅 정책을 추가하여 로깅을 위해 Stackify에 오류를 보내는 방법을 보여 줍니다.
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
ms.openlocfilehash: 5daf21cb55289c874d56910b1240e1433f3d55d0
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945958"
---
# <a name="send-errors-to-stackify-for-logging"></a>로깅을 위해 Stackify에 오류 보내기

이 문서에서는 오류 로깅 정책을 추가하여 로깅을 위해 Stackify에 오류를 보내는 방법을 설명하는 Azure API 관리 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **on-error** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [변환 정책](../api-management-transformation-policies.md)
+ [정책 샘플](../policy-samples.md)

