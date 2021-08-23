---
title: 미사용 데이터의 Form Recognizer 서비스 암호화
titleSuffix: Azure Applied AI Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며, CMK(고객 관리형 키)라고 하는 고유한 키를 사용하여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Form Recognizer의 미사용 데이터 암호화와 CMK를 활성화하고 관리하는 방법을 설명합니다.
author: erindormier
manager: venkyv
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: fad7440c82ee22a370bfecc13c60f3d4d4828b07
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538627"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>미사용 데이터의 Form Recognizer 암호화

Azure Form Recognizer는 클라우드로 데이터를 유지할 때 자동으로 데이터를 암호화합니다. Form Recognizer 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리형 키는 2020년 5월 11일 이후에 생성된 리소스만 사용할 수 있습니다. Form Recognizer에서 CMK를 사용하려면 새 Form Recognizer 리소스를 만들어야 합니다. 리소스가 생성되면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../../cognitive-services/includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* [Form Recognizer 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)