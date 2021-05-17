---
title: 미사용 데이터의 Custom Vision 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며, CMK(고객 관리형 키)라고 하는 고유한 키를 사용하여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Custom Vision의 미사용 데이터 암호화와 CMK를 활성화하고 관리하는 방법을 설명합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652261"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>미사용 데이터의 Custom Vision 암호화

Azure Custom Vision은 클라우드로 데이터를 유지할 때 자동으로 암호화합니다. Custom Vision 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리형 키는 2020년 5월 11일 이후에 생성된 리소스만 사용할 수 있습니다. Custom Vision에서 CMK를 사용하려면 새 Custom Vision 리소스를 만들어야 합니다. 리소스가 생성되면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* CMK를 지원하는 서비스의 전체 목록은 [Cognitive Services용 고객 관리형 키](../encryption/cognitive-services-encryption-keys-portal.md)를 참조하세요.
* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)