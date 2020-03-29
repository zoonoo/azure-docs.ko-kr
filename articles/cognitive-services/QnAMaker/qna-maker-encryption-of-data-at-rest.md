---
title: 미사용 데이터의 QnA 메이커 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 QnA 메이커 암호화 .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372391"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>미사용 데이터의 QnA 메이커 암호화

QnA Maker는 데이터가 클라우드에 유지될 때 자동으로 암호화하여 조직의 보안 및 규정 준수 목표를 달성하는 데 도움을 줍니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 자신의 키로 구독을 관리하는 옵션도 있습니다. CMK(고객 관리 키)는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 더 큰 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

QnA Maker는 Azure 검색에서 CMK 지원을 사용합니다. Azure 키 [자격 증명 모음을 사용하여 Azure 검색에서 CMK를](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)만들어야 합니다. 이 Azure 인스턴스는 CMK를 사용하도록 설정하려면 QnA Maker 서비스와 연결되어야 합니다.

> [!IMPORTANT]
> Azure 검색 서비스 리소스는 2019년 1월 이후에 만들어졌어야 하며 무료(공유) 계층에 있을 수 없습니다. Azure 포털에서 고객 관리 키를 구성할 수 있는 지원은 없습니다.

## <a name="enable-customer-managed-keys"></a>고객 관리 키 사용

QnA Maker 서비스는 Azure 검색 서비스에서 CMK를 사용합니다. CMK를 활성화하려면 다음 단계를 따르십시오.

1. 새 Azure Search 인스턴스를 만들고 [Azure Cognitive Search에 대한 고객 관리 키 필수 구성 사이트에](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)언급된 필수 구성 조건을 사용하도록 설정합니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker 리소스를 만들면 Azure 검색 인스턴스와 자동으로 연결됩니다. CMK와 함께 사용할 수 없습니다. CMK를 사용하려면 1단계에서 새로 만든 Azure Search 인스턴스를 연결해야 합니다. 특히 QnA Maker `AzureSearchAdminKey` 리소스를 `AzureSearchName` 업데이트해야 합니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 다음으로 새 응용 프로그램 설정을 만듭니다.
   * **이름**: 이 것을`CustomerManagedEncryptionKeyUrl`
   * **값**: Azure Search 인스턴스를 만들 때 1단계에서 얻은 값입니다.

   ![암호화 설정 보기](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 완료되면 런타임을 다시 시작합니다. 이제 QnA 메이커 서비스가 CMK 지원됩니다.

## <a name="regional-availability"></a>국가별 가용성

고객 관리 키는 모든 Azure 검색 지역에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 키 자격 증명 모음에서 CMK를 사용하여 Azure 검색에서 암호화](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [미사용 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
