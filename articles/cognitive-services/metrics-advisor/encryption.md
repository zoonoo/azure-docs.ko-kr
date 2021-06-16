---
title: Metrics Advisor 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 Metrics Advisor 서비스 암호화.
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 782314b39a3ab6deda7011b38d75778e5441b32f
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891197"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>미사용 데이터의 Metrics Advisor 서비스 암호화

Metrics Advisor 서비스는 데이터를 클라우드에 저장할 때 자동으로 암호화합니다. Metrics Advisor 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리형 키는 E0 가격 책정 계층에서만 사용할 수 있습니다. 고객 관리형 키를 사용하는 기능을 요청하려면 [Metrics Advisor 서비스 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다. 요청 상태에 대한 답변을 받는 데는 영업일 기준 약 3~5일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. Metrics Advisor 서비스에서 CMK 사용이 승인되면 새 Metrics Advisor 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. E0 가격 계층을 사용하여 Metrics Advisor 리소스가 만들어지면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* [Metrics Advisor 서비스 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)