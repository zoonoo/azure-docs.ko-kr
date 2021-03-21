---
title: Cognitive Services에 대 한 Customer-Managed 키
titleSuffix: Cognitive Services
description: Azure Portal를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키를 구성 하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: 88a723abc606a527232b7c1949f35c1fedfdba50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706846"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Cognitive Services에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키 구성

Cognitive Services에 대 한 Azure Key Vault에서 Customer-Managed 키를 사용 하도록 설정 하는 프로세스는 제품 마다 다릅니다. 다음 링크를 사용 하 여 서비스별 지침을 따르세요.

## <a name="vision"></a>Vision

* [휴지 상태의 데이터 암호화 Custom Vision](../custom-vision-service/encrypt-data-at-rest.md)
* [미사용 데이터의 Face 서비스 암호화](../face/encrypt-data-at-rest.md)
* [미사용 데이터의 폼 인식기 암호화](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>언어

* [휴지 상태의 데이터에 대 한 서비스 암호화 Language Understanding](../LUIS/encrypt-data-at-rest.md)
* [휴지 상태의 데이터 암호화 QnA Maker](../QnAMaker/encrypt-data-at-rest.md)
* [미사용 데이터의 변환기 암호화](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>의사 결정

* [휴지 상태의 데이터 암호화 Content Moderator](../Content-Moderator/encrypt-data-at-rest.md)
* [휴지 상태의 데이터 암호화 Personalizer](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)