---
title: 자습서 - API Management에서 수정 버전을 사용하여 작업을 중단하지 않는 API 변경을 안전하게 수행
titleSuffix: Azure API Management
description: 이 자습서의 단계에 따라 API Management에서 수정 버전을 사용하여 작업을 중단하지 않는 변경을 수행하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377508"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>자습서: 수정 버전을 사용하여 작업을 중단하지 않는 API 변경을 안전하게 수행
개발자들이 API를 사용할 수 있도록 모든 준비가 완료되면, API 호출자의 작업을 중단하지 않으면서 해당 API를 변경해야 합니다. 이렇게 하면 개발자에게 변경 내용을 알릴 때도 유용합니다. 

Azure API Management에서 *수정 버전* 을 사용하여 작업을 중단하지 않는 API 변경을 수행하여 변경 내용을 안전하게 모델링하고 테스트할 수 있습니다. 준비가 되면 현재 수정 버전을 만들고 현재 API를 바꿀 수 있습니다. 

배경 정보는 [버전 및 수정 버전](https://azure.microsoft.com/blog/versions-revisions/) 및 [Azure API Management를 사용한 API 버전 관리](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 새 수정 버전 추가
> * 작업을 중단하지 않는 방식으로 수정 버전 변경
> * 수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
> * 개발자 포털로 이동하여 변경 내용과 변경 로그 확인

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Azure Portal의 API 수정 버전":::

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 또한, 다음 자습서 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 완료합니다.

## <a name="add-a-new-revision"></a>새 수정 버전 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하고 API Management 인스턴스로 이동합니다.
1. **API** 를 선택합니다.
2. API 목록에서 **데모 회의 API**(또는 수정 버전을 추가하려는 다른 API)를 선택합니다.
3. **수정 버전** 탭을 선택합니다.
4. **+ 수정 버전 추가** 를 선택합니다.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API 수정 버전 추가":::

    > [!TIP]
    > API의 바로 가기 메뉴( **...** )에서 **수정 버전 추가** 를 선택할 수도 있습니다.

5. 새 수정 버전의 용도를 쉽게 기억할 수 있도록 해당 버전의 설명을 입력합니다.
6. **만들기** 를 선택합니다.
7. 이제 새 수정 버전이 작성되었습니다.

    > [!NOTE]
    > 원본 API는 **수정 버전 1** 로 유지됩니다. 다른 수정 버전을 현재 항목으로 설정할 때까지는 사용자가 이 수정 버전을 계속 호출합니다.

## <a name="make-non-breaking-changes-to-your-revision"></a>작업을 중단하지 않는 방식으로 수정 버전 변경

1. API 목록에서 **데모 회의 API** 를 선택합니다.
1. 화면 상단 근처에서 **디자인** 탭을 선택합니다.
1. 디자인 탭 바로 위에 있는 **수정 버전 선택기** 를 보시면 현재 **수정 버전 2** 를 선택한 것으로 표시됩니다.

    > [!TIP]
    > 수정 버전 선택기를 사용하여 작업하려는 수정 버전 간을 전환합니다.
1. **+ 작업 추가** 를 선택합니다.
1. 새 작업을 **POST** 로 설정하고 작업의 이름, 표시 이름 및 URL을 **test** 로 설정합니다.
1. 새 작업을 **저장** 합니다.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="수정 버전 수정":::
1. 이제 **수정 버전 2** 를 변경했습니다. 페이지 상단 근처에 있는 **수정 버전 선택기** 를 사용하여 **수정 버전 1** 로 다시 전환합니다.
1. **수정 버전 1** 에는 새 작업이 표시되지 않습니다. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가

1. 페이지 상단 근처의 메뉴에서 **수정 버전** 탭을 선택합니다.
1. **수정 버전 2** 의 상황에 맞는 메뉴(**...**)를 엽니다.
1. **현재로 설정** 을 선택합니다.
1. 이 변경 내용에 대한 정보를 게시하려는 경우 **이 API에 대한 공용 변경 로그에 게시** 확인란을 선택합니다. 개발자가 보는 변경 내용에 대한 설명을 제공합니다. 예를 들면 다음과 같습니다. **수정 버전 테스트. 세 “테스트” 작업을 추가했습니다.** 와 같이 개발자에게 표시할 변경 내용에 대한 설명을 입력합니다.
1. 이제 **수정 버전 2** 가 현재 항목으로 설정되었습니다.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="수정 버전 창의 수정 버전 메뉴":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>개발자 포털로 이동하여 변경 내용과 변경 로그 확인

[개발자 포털](api-management-howto-developer-portal-customize.md)을 사용해 본 경우 API 변경 내용을 검토하고 여기에서 로그를 변경할 수 있습니다.

1. Azure Portal에서 **API** 를 선택합니다.
1. 상단 메뉴에서 **개발자 포털** 을 선택합니다.
1. 개발자 포털에서 **API** 를 선택한 다음, **데모 회의 API** 를 선택합니다.
1. 이제 새 **테스트** 작업을 사용할 수 있습니다.
1. API 이름 근처에 있는 **변경 로그** 를 선택합니다.
1. 이 목록에 변경 로그 항목이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 새 수정 버전 추가
> * 작업을 중단하지 않는 방식으로 수정 버전 변경
> * 수정 버전을 현재 항목으로 설정하고 변경 로그 항목 추가
> * 개발자 포털로 이동하여 변경 내용과 변경 로그 확인

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [여러 버전의 API 게시](api-management-get-started-publish-versions.md)
