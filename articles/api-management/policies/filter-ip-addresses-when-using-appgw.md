---
title: 샘플 API Management 정책 - Application Gateway를 사용하는 경우 IP 주소를 필터링합니다.
titleSuffix: Azure API Management
description: Azure API Management 정책 샘플 - Application Gateway를 사용하는 경우 요청 IP 주소를 필터링하는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076116"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Application Gateway를 사용하는 경우 요청 IP 주소 필터링

이 문서에서는 Application Gateway 또는 다른 중개자를 통해 API Management 인스턴스에 액세스할 때 요청 IP 주소를 필터링하는 방법을 안내하는 Azure API Management 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-reference.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [액세스 제한 정책](../api-management-access-restriction-policies.md)
+ [정책 샘플](../policy-reference.md)