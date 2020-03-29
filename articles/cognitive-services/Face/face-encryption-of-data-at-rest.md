---
title: 미사용 데이터의 얼굴 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 얼굴 서비스 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372413"
---
# <a name="face-service-encryption-of-data-at-rest"></a>미사용 데이터의 얼굴 서비스 암호화

Face 서비스는 데이터를 클라우드에 유지하면 자동으로 암호화합니다. Face 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약속을 충족하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층에서만 사용할 수 있습니다. 고객 관리 키를 사용할 수 있는 기능을 요청하려면 [얼굴 서비스 고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성하고 제출합니다. 요청 상태를 다시 듣는 데 는 영업일 기준으로 약 3~5일이 소요됩니다. 필요에 따라 대기열에 배치되고 공간이 사용 가능해지면 승인될 수 있습니다. Face 서비스에서 CMK 사용이 승인되면 새 Face 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. E0 가격 책정 계층이 있는 Face 리소스가 만들어지면 Azure Key Vault를 사용하여 관리되는 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>다음 단계

* [얼굴 서비스 고객 관리 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


