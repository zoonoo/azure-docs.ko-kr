---
title: 미사용 데이터의 콘텐츠 중재자 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 콘텐츠 중재자 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372403"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>미사용 데이터의 콘텐츠 중재자 암호화

콘텐츠 운영자는 클라우드에 유지될 때 데이터를 자동으로 암호화하여 조직의 보안 및 규정 준수 목표를 달성하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층에서만 사용할 수 있습니다. 고객 관리 키를 사용하는 기능을 요청하려면 콘텐츠 [중재자 고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성하고 제출합니다. 요청 상태를 다시 듣는 데 는 영업일 기준으로 약 3~5일이 소요됩니다. 필요에 따라 대기열에 배치되고 공간이 사용 가능해지면 승인될 수 있습니다. 콘텐츠 중재자 서비스와 함께 CMK 를 사용할 수 있도록 승인되면 새 콘텐츠 중재자 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. E0 가격 책정 계층이 있는 콘텐츠 중재자 리소스가 만들어지면 Azure Key Vault를 사용하여 관리되는 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>콘텐츠 운영자 팀의 데이터 암호화 사용

콘텐츠 운영자 검토 팀의 데이터 암호화를 사용하려면 [웹에서 빠른 시작: 콘텐츠 중재자 시도를](quick-start.md#create-a-review-team)참조하십시오.  

> [!NOTE]
> 콘텐츠 운영자 E0 가격 책정 계층과 함께 _리소스 ID를_ 제공해야 합니다.


## <a name="next-steps"></a>다음 단계

* [콘텐츠 운영자 고객 관리 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
