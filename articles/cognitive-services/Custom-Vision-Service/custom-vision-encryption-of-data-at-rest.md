---
title: 휴지 상태의 데이터 암호화 Custom Vision
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 암호화를 Custom Vision 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310294"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>휴지 상태의 데이터 암호화 Custom Vision

Azure Custom Vision는 데이터를 클라우드에 보관할 때 자동으로 암호화 합니다. Custom Vision 암호화를 통해 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족할 수 있습니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객이 관리 하는 키는 11 월 2020 일 이후 생성 된 리소스만 사용할 수 있습니다. Custom Vision에서 CMK를 사용 하려면 새 Custom Vision 리소스를 만들어야 합니다. 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 id를 설정할 수 있습니다.

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
