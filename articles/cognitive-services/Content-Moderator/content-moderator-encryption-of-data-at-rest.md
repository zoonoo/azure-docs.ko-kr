---
title: 휴지 상태의 데이터 암호화 Content Moderator
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 암호화를 Content Moderator 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913707"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>휴지 상태의 데이터 암호화 Content Moderator

Content Moderator은 클라우드에 유지 될 때 데이터를 자동으로 암호화 하 여 조직의 보안 및 규정 준수 목표를 충족 하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층 에서만 사용할 수 있습니다. 고객 관리 키를 사용 하는 기능을 요청 하려면 [Content Moderator Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성 하 고 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. Content Moderator 서비스를 사용 하 여 CMK를 승인한 후에는 새 Content Moderator 리소스를 만들고, 해당 가격 책정 계층으로 E0를 선택 해야 합니다. E0 가격 책정 계층을 사용 하 여 Content Moderator 리소스가 만들어지면 Azure Key Vault를 사용 하 여 관리 되는 id를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Content Moderator 팀에 데이터 암호화 사용

Content Moderator 검토 팀에 데이터 암호화를 사용 하도록 설정 하려면 [빠른 시작: 웹에서 Content Moderator](quick-start.md#create-a-review-team)사용을 참조 하세요.  

> [!NOTE]
> Content Moderator E0 가격 책정 계층을 사용 하 여 _리소스 ID_ 를 제공 해야 합니다.

## <a name="next-steps"></a>다음 단계

* CMK를 지 원하는 서비스의 전체 목록은 [Cognitive Services의 고객 관리 키](../encryption/cognitive-services-encryption-keys-portal.md) 를 참조 하세요.
* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)