---
title: 샘플 API 관리 정책 - 응용 프로그램 게이트웨이를 사용할 때 IP 주소에 대한 필터
titleSuffix: Azure API Management
description: Azure API 관리 정책 샘플 - 응용 프로그램 게이트웨이를 사용할 때 요청 IP 주소를 필터링하는 방법을 보여 줍니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942478"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>응용 프로그램 게이트웨이를 사용할 때 요청 IP 주소에 필터링

이 문서에서는 API 관리 인스턴스가 응용 프로그램 게이트웨이 또는 기타 중간을 통해 액세스할 때 요청 IP 주소에 대한 필터를 보여 주는 Azure API 관리 정책 샘플을 보여 줍니다. 정책 코드를 설정하거나 편집하려면 [정책 설정 또는 편집](../set-edit-policies.md)에 설명된 단계를 따릅니다. 다른 예제를 보려면 [정책 샘플](../policy-samples.md)을 참조하세요.

## <a name="policy"></a>정책

코드를 **인바운드** 블록에 붙여넣습니다.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>다음 단계

APIM 정책에 대해 자세히 알아보기:

+ [액세스 제한 정책](../api-management-access-restriction-policies.md)
+ [정책 샘플](../policy-samples.md)
