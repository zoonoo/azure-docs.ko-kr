---
title: 샘플 API management 정책-Application Gateway를 사용 하는 경우 IP 주소를 필터링 합니다.
titleSuffix: Azure API Management
description: Azure API management 정책 샘플-Application Gateway를 사용 하는 경우 요청 IP 주소를 필터링 하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75942478"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Application Gateway를 사용 하는 경우 요청 IP 주소에 대 한 필터링

이 문서에서는 Application Gateway 또는 다른 중개자를 통해 API Management 인스턴스에 액세스할 때 요청 IP 주소에 대해 필터링 하는 방법을 보여 주는 Azure API management 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [액세스 제한 정책](../api-management-access-restriction-policies.md)
+ [정책 샘플](../policy-samples.md)
