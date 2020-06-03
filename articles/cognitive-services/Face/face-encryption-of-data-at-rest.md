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
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309036"
---
# <a name="face-service-encryption-of-data-at-rest"></a>미사용 데이터의 얼굴 서비스 암호화

Face 서비스는 데이터를 클라우드에 보관할 때 데이터를 자동으로 암호화 합니다. 얼굴 서비스 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층 에서만 사용할 수 있습니다. 고객 관리 키를 사용 하는 기능을 요청 하려면 [고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성 하 여 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. Face 서비스에서 CMK를 사용 하도록 승인 되 면 새 얼굴 리소스를 만들고이를 가격 책정 계층으로 선택 해야 합니다. E0 가격 책정 계층을 사용 하 여 얼굴 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 되는 id를 설정할 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성

고객 관리 키는 현재 다음 지역에서 사용할 수 있습니다.

* 미국 중남부
* 미국 서부 2
* 미국 동부
* US Gov 버지니아

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* CMK를 지 원하는 서비스의 전체 목록은 [Cognitive Services의 고객 관리 키](../encryption/cognitive-services-encryption-keys-portal.md) 를 참조 하세요.
* [Azure Key Vault란](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
