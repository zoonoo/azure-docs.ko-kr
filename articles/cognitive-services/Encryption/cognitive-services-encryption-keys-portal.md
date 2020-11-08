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
ms.openlocfilehash: 1d161c82c087fd86a3774f0d121330260b1574e4
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366097"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Cognitive Services에 대 한 Azure Key Vault를 사용 하 여 고객 관리 키 구성

Cognitive Services에 대 한 Azure Key Vault에서 Customer-Managed 키를 사용 하도록 설정 하는 프로세스는 제품 마다 다릅니다. 다음 링크를 사용 하 여 서비스별 지침을 따르세요.

## <a name="vision"></a>시각

* [휴지 상태의 데이터 암호화 Custom Vision](../Custom-Vision-Service/custom-vision-encryption-of-data-at-rest.md)
* [미사용 데이터의 Face 서비스 암호화](../Face/face-encryption-of-data-at-rest.md)
* [미사용 데이터의 폼 인식기 암호화](../form-recognizer/form-recognizer-encryption-of-data-at-rest.md)

## <a name="language"></a>언어

* [휴지 상태의 데이터에 대 한 서비스 암호화 Language Understanding](../LUIS/luis-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터 암호화 QnA Maker](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [미사용 데이터의 변환기 암호화](../translator/translator-encryption-of-data-at-rest.md)

## <a name="decision"></a>의사 결정

* [휴지 상태의 데이터 암호화 Content Moderator](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [휴지 상태의 데이터 암호화 Personalizer](../personalizer/personalizer-encryption-of-data-at-rest.md)

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed 키 요청 양식](https://aka.ms/cogsvc-cmk)