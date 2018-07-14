---
title: LUIS에서 키 관리 | Microsoft Docs
description: Language Understanding(LUIS)을 사용하여 프로그래밍 방식 API, 끝점 및 외부 키를 관리합니다.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266025"
---
# <a name="manage-your-luis-keys"></a>LUIS 키 관리
키를 사용하면 LUIS 앱을 작성하고 게시하거나 끝점을 쿼리할 수 있습니다. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>주요 개념
LUIS 작성 및 끝점 키 개념을 이해하려면 [LUIS의 키](luis-concept-keys.md)를 참조하세요.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>끝점 키 할당
**앱 게시** 페이지의 **리소스 및 키** 테이블에 키가 이미 있습니다. 이것은 작성(시작) 키입니다. 

1. [Azure Portal](https://portal.azure.com)에서 LUIS 키를 만듭니다. 자세한 내용은 [Azure를 사용하여 구독 키 만들기](luis-how-to-azure-subscription.md)를 참조하세요.
 
2. 이전 단계에서 만든 LUIS 키를 추가하려면 **키 추가** 단추를 클릭하여 **앱에 키 할당** 대화 상자를 엽니다. 

    ![앱에 키 할당](./media/luis-manage-keys/assign-key.png)
3. 대화 상자에서 테넌트를 선택합니다. 
 
    > [!Note]
    > Azure에서 테넌트는 서비스와 연결된 클라이언트 또는 조직의 Azure Active Directory ID를 나타냅니다. 이전에 개인 Microsoft 계정으로 Azure 구독을 등록했으면 이미 테넌트를 가지고 있습니다. [Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)에 로그인할 때 기본 테넌트에 자동으로 로그인됩니다. 이 테넌트를 추가 비용 없이 사용할 수 있으나 조직 관리자 계정을 만들 수도 있습니다.

4. 추가할 Azure LUIS 키와 연결된 Azure 구독을 선택합니다.

5. Azure LUIS 계정을 선택합니다. 계정의 지역이 괄호 안에 표시됩니다. 

    ![키 선택](./media/luis-manage-keys/assign-key-filled-out.png)

6. 이 끝점 키를 할당한 후 모든 끝점 쿼리에서 이 키를 사용합니다. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>게시 지역
[유럽](luis-reference-regions.md#publishing-to-europe) 및 [오스트레일리아](luis-reference-regions.md#publishing-to-australia)의 게시를 포함하여 게시 [지역](luis-reference-regions.md)을 자세히 알아봅니다. 게시 지역은 작성 지역과 다릅니다. 원하는 게시 지역에 해당하는 작성 지역에서 앱을 만들어야 합니다.

## <a name="unassign-key"></a>키 할당 취소

* **리소스 및 키 목록**에서 할당 취소할 엔터티 옆에 있는 휴지통 아이콘을 클릭합니다. 그런 다음, 확인 메시지에서 **확인**을 클릭하여 삭제를 확인합니다.
 
    ![엔터티 할당 취소](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> LUIS 키의 할당을 취소해도 이 키가 Azure 구독에서 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계

키를 사용하여 **앱 게시** 페이지에서 앱을 게시합니다. 게시에 대한 자세한 내용은 [앱 게시](PublishApp.md)를 참조하세요.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website