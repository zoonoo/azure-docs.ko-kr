---
title: 휴지 상태의 데이터 암호화 QnA Maker
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 암호화를 QnA Maker 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372391"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>휴지 상태의 데이터 암호화 QnA Maker

QnA Maker은 클라우드에 유지 될 때 데이터를 자동으로 암호화 하 여 조직의 보안 및 규정 준수 목표를 충족 하는 데 도움을 줍니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리 하는 암호화 키를 사용 합니다. 사용자 고유의 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. CMK (고객이 관리 하는 키)는 액세스 제어를 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다.

QnA Maker는 Azure search에서 CMK 지원을 사용 합니다. [Azure Search에서 Azure Key Vault를 사용 하 여 Cmk](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)를 만들어야 합니다. CMK를 사용 하도록 설정 하려면이 Azure 인스턴스를 QnA Maker 서비스와 연결 해야 합니다.

> [!IMPORTANT]
> Azure Search 서비스 리소스는 2019 년 1 월 일 후에 생성 되어야 하며 무료 (공유) 계층에 있을 수 없습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 것은 지원 되지 않습니다.

## <a name="enable-customer-managed-keys"></a>고객이 관리 하는 키 사용

QnA Maker 서비스는 Azure Search 서비스의 CMK를 사용 합니다. CMKs를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 새 Azure Search 인스턴스를 만들고 [Azure Cognitive Search에 대 한 고객이 관리 하는 주요 필수 조건](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)에 설명 된 필수 구성 요소를 사용 하도록 설정 합니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker 리소스를 만들면 자동으로 Azure Search 인스턴스와 연결 됩니다. CMK와 함께 사용할 수 없습니다. CMK를 사용 하려면 1 단계에서 만든 Azure Search의 새로 만든 인스턴스를 연결 해야 합니다. 특히 QnA Maker 리소스에서 `AzureSearchAdminKey` 및 `AzureSearchName` 를 업데이트 해야 합니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 다음으로 새 응용 프로그램 설정을 만듭니다.
   * **이름**: 다음으로 설정 합니다.`CustomerManagedEncryptionKeyUrl`
   * **Value**: Azure Search 인스턴스를 만들 때 1 단계에서 얻은 값입니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 완료 되 면 런타임을 다시 시작 합니다. 이제 QnA Maker 서비스는 CMK를 사용 합니다.

## <a name="regional-availability"></a>국가별 가용성

모든 Azure Search 지역에서 고객 관리 키를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault에서 CMKs를 사용 하 Azure Search의 암호화](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [휴지 상태의 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Azure Key Vault에 대 한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
