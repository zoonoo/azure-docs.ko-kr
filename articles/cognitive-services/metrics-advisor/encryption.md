---
title: 메트릭 관리자 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 메트릭 관리자 서비스 암호화
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909746"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>미사용 데이터의 메트릭 관리자 서비스 암호화

메트릭 관리자 서비스는 데이터를 클라우드에 보관할 때 데이터를 자동으로 암호화 합니다. 메트릭 관리자 서비스 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리 키는 E0 가격 책정 계층 에서만 사용할 수 있습니다. 고객 관리 키를 사용 하는 기능을 요청 하려면 [메트릭 Advisor 서비스 Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성 하 고 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. 메트릭 관리자 서비스와 함께 CMK를 사용 하도록 승인 되 면 새 메트릭 관리자 리소스를 만들고 해당 가격 책정 계층으로 E0를 선택 해야 합니다. E0 가격 책정 계층을 사용 하 여 메트릭 관리자 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 id를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* [메트릭 관리자 서비스 Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대 한 자세한 정보](../../key-vault/general/overview.md)