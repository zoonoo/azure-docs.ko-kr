---
title: 미사용 데이터의 폼 인식기 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 폼 인식기 암호화입니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: b897de94ad8cdb628520f9386b076f762a5cc230
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537955"
---
# <a name="form-recognizer-encryption-of-data-at-rest"></a>미사용 데이터의 폼 인식기 암호화

Azure 양식 인식기는 클라우드로 데이터를 보관할 때 데이터를 자동으로 암호화 합니다. 양식 인식기 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객이 관리 하는 키는 11 월 2020 일 이후 생성 된 리소스만 사용할 수 있습니다. 양식 인식기에서 CMK를 사용 하려면 새 폼 인식기 리소스를 만들어야 합니다. 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 id를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* [양식 인식기 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대 한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


