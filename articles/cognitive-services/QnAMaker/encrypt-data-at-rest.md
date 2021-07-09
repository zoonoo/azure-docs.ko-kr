---
title: QnA Maker 미사용 데이터 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며, 이를 통해 CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 Cognitive Services 구독을 관리할 수 있습니다. 이 문서에서는 QnA Maker에 대한 미사용 데이터 암호화 및 CMK를 사용하도록 설정하고 관리하는 방법을 설명합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 863611b762de5b7ec535b20a71880b523d0fc166
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111892223"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker 미사용 데이터 암호화

QnA Maker는 데이터가 클라우드에 유지될 때 자동으로 암호화하여 조직의 보안 및 규정 준수 목표를 충족하는 데 도움을 줍니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 구독을 관리하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전시키고, 사용하지 않도록 설정하고, 철회할 수 있는 훨씬 더 큰 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성된 경우 이중 암호화가 제공되어 두 번째 보호 계층을 제공하는 한편 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

QnA Maker는 Azure Search의 CMK 지원을 사용합니다. [Azure Key Vault를 사용하여 Azure Search에서 CMK를 구성](../../search/search-security-manage-encryption-keys.md)합니다. CMK를 사용하도록 설정하려면 이 Azure 인스턴스를 QnA Maker 서비스와 연결해야 합니다.

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

사용자 지정 질문 답변은 [Azure 검색의 CMK 지원](../../search/search-security-manage-encryption-keys.md)을 사용하고, 제공된 CMK를 자동으로 연결하여 Azure 검색 인덱스에 저장된 데이터를 암호화합니다.

---

> [!IMPORTANT]
> Azure Search Service 리소스는 2019년 1월 이후에 만들어졌어야 하며 체험(공유) 계층에 있을 수 없습니다. 고객 관리형 키는 Azure Portal에서 구성할 수 없습니다.

## <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

QnA Maker 서비스는 Azure Search Service의 CMK를 사용합니다. 다음 단계에 따라 CMK를 사용하도록 설정합니다.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

1. 새 Azure Search 인스턴스를 만들고, [Azure Cognitive Search에 대한 고객 관리형 키](../../search/search-security-manage-encryption-keys.md#prerequisites)에 언급된 필수 구성 요소를 사용하도록 설정합니다.

   ![암호화 설정 보기 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. QnA Maker 리소스를 만들면 Azure Search 인스턴스와 자동으로 연결됩니다. 이 인스턴스는 CMK와 함께 사용할 수 없습니다. CMK를 사용하려면 1단계에서 새로 만든 Azure Search 인스턴스를 연결해야 합니다. 특히 QnA Maker 리소스에서 `AzureSearchAdminKey` 및 `AzureSearchName`을 업데이트해야 합니다.

   ![암호화 설정 보기 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. 다음으로 새 애플리케이션 설정을 만듭니다.
   * **이름**: `CustomerManagedEncryptionKeyUrl`로 설정합니다.
   * **값**: Azure Search 인스턴스를 만들 때 1단계에서 얻은 값을 사용합니다.

   ![암호화 설정 보기 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. 완료되면 런타임을 다시 시작합니다. 이제 QnA Maker 서비스에서 CMK를 사용할 수 있습니다.

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

1.  사용자 지정 질문 답변(미리 보기) 기능이 사용하도록 설정된 Text Analytics 서비스의 **암호화** 탭으로 이동합니다.
2.  **고객 관리형 키** 옵션을 선택합니다. [고객 관리형 키](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal)에 대한 세부 정보를 제공하고, **저장** 을 클릭합니다.

> [!div class="mx-imgBorder"]
> ![질문 답변 CMK](media/question-answering-cmk.png)
   
3.  성공적으로 저장되면 CMK가 Azure Search 인덱스에 저장된 데이터를 암호화하는 데 사용됩니다.

> [!IMPORTANT]
> 기술 자료를 만들기 전에 새 Azure Cognitive Search 서비스에서 CMK를 설정하는 것이 좋습니다. Text Analytics 서비스에서 기존 기술 자료를 사용하여 CMK를 설정하면 해당 서비스에 대한 액세스 권한이 손실될 수 있습니다. Azure Cognitive Search의 [암호화된 콘텐츠 작업](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content)에 대해 자세히 알아보세요.

> [!NOTE]
> 고객 관리형 키를 사용하는 기능을 요청하려면 [Cognitive Services 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다.

---

## <a name="regional-availability"></a>국가별 가용성

고객 관리형 키는 모든 Azure Search 지역에서 사용할 수 있습니다.

## <a name="encryption-of-data-in-transit"></a>전송 중 데이터 암호화

QnA Maker 포털은 사용자의 브라우저에서 실행됩니다. 모든 작업은 각 Cognitive Service API에 대한 직접 호출을 트리거합니다. 따라서 QnA Maker는 전송 중 데이터에 대한 규정을 준수합니다.
그러나 QnA Maker 포털 서비스는 미국 서부에서 호스팅되므로 미국 이외의 고객에게는 여전히 적합하지 않습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault의 CMK를 사용하여 Azure Search에서 암호화](../../search/search-security-manage-encryption-keys.md)
* [저장 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)
