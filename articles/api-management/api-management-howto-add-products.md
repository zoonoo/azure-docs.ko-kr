---
title: 자습서 - Azure API Management에서 제품 만들기 및 게시
description: 이 자습서에서는 Azure API Management에서 제품을 만들고 게시합니다. 제품이 게시되면 개발자는 제품의 API를 사용할 수 있습니다.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993553"
---
# <a name="tutorial-create-and-publish-a-product"></a>자습서: 제품 만들기 및 게시  

Azure API Management에서 [*제품*](api-management-terminology.md#term-definitions)은 하나 이상의 API와 사용 할당량 및 사용 약관을 포함하고 있습니다. 제품이 게시되면 개발자는 제품을 구독하고 제품의 API를 사용할 수 있습니다.  

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 제품 만들기 및 게시
> * 제품에 API 추가

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="포털의 API Management 제품":::


## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 또한 [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서를 완료합니다.

## <a name="create-and-publish-a-product"></a>제품 만들기 및 게시

1. Azure Portal에 로그인하고 API Management 인스턴스로 이동합니다.
1. 왼쪽 탐색 영역에서 **제품** >  **+ 추가** 를 선택합니다.
1.  **제품 추가** 창에서, 다음 표에 설명된 값을 입력하여 제품을 만듭니다.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="포털에서 제품 추가":::

    | 속성                     | 설명                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 표시 이름             | [개발자 포털](api-management-howto-developer-portal.md)에 표시하려는 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Description              | 제품의 용도, 제품에서 액세스할 수 있는 API, 기타 정보 등 제품 정보를 입력합니다.                                                                                                                                               |
    | 시스템 상태                    | 제품을 게시하려면 **게시됨** 을 누릅니다. 제품의 API를 호출하려면 먼저 제품을 게시해야 합니다. 기본적으로 새 제품은 게시되지 않으며 **관리자** 그룹에만 표시됩니다.                                                                                      |
    | 구독 필요    | 사용자가 구독해야만 제품을 사용할 수 있게 할 경우 선택합니다.                                                                                                                                                                                                                                   |
    | 승인 필요        | 관리자가 이 제품에 대한 구독 시도를 검토하고 허용하거나 거부하도록 하려면 선택합니다. 선택하지 않으면 구독 시도가 자동으로 승인됩니다.                                                                                                                         |
    | 구독 수 제한 | 필요하다면 여러 동시 구독의 수를 제한합니다.                                                                                                                                                                                                                                |
    | 약관              | 구독자가 제품을 사용하기 위해 허용해야 하는 제품의 사용 약관을 포함할 수 있습니다.                                                                                                                                                                                                             |
    | API                     | 하나 이상의 API를 선택합니다. 제품을 만든 후 API를 추가할 수도 있습니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [제품에 API 추가](#add-apis-to-a-product)를 참조하세요. |

3. **만들기** 를 선택하여 새 제품을 만듭니다.

### <a name="add-more-configurations"></a>구성 더 추가

저장 후 제품을 계속 구성합니다. API Management 인스턴스의 **제품** 창에서 제품을 선택합니다. 추가 또는 업데이트:


|항목   |Description  |
|---------|---------|
|설정     |    제품 메타데이터 및 상태 데이터     |
|API     |  제품과 연결된 API       |
|[정책](api-management-howto-policies.md)     |  제품 API에 적용되는 정책      |
|Access Control     |  개발자 또는 게스트에게 제품 표시 여부       |
|[구독](api-management-subscriptions.md)    |    제품 구독자     |

## <a name="add-apis-to-a-product"></a>제품에 여러 API 추가

제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 제품 만들기 중에 기존 API를 하나 이상 추가할 수 있습니다. 나중에 제품 **설정** 페이지에서 또는 API를 만드는 동안 제품에 API를 추가할 수 있습니다.

개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.

### <a name="add-an-api-to-an-existing-product"></a>기존 제품에 API 추가


1. API Management 인스턴스의 왼쪽 탐색 영역에서 **제품** 을 선택합니다.
1. 제품을 선택한 다음, **API** 를 선택합니다.
1. **+추가** 를 선택합니다.
1. 하나 이상의 API를 선택한 다음, **선택** 을 선택합니다.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="기존 제품에 API 추가":::

> [!TIP]
> [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) 또는 PowerShell 명령을 통해 사용자 구독을 만들거나 사용자 지정 구독 키가 포함된 제품으로 업데이트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 제품 만들기 및 게시
> * 제품에 API 추가

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [빈 API 및 모의 API 응답 만들기](mock-api-responses.md)
