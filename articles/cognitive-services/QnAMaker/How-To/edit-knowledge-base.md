---
title: 기술 자료 편집 - QnA Maker
description: QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756728"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>기술 자료에서 QnA 쌍 편집

QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.

QnA 쌍은 파일 이나 URL 과 같은 데이터 원본에서 추가되거나 편집 소스로 추가됩니다. 편집 소스는 QnA 쌍이 QnA 포털에 수동으로 추가되었다는 것을 나타냅니다. 모든 QnA 쌍은 편집에 사용할 수 있습니다.

## <a name="add-an-editorial-qna-pair"></a>에디토리얼 QnA 페어 추가
1. [QnA 포털에](https://www.qnamaker.ai/)로그인한 다음 기술 자료에서 QnA 쌍을 추가합니다.
1. 기술 자료의 **편집** 페이지에서 **QnA 쌍 추가를** 선택하여 새 QnA 쌍을 추가합니다.

1. 새 QnA 쌍 행에 필요한 **질문** 및 **답변** 필드를 추가합니다. 다른 필드는 선택 사항입니다. 모든 필드는 언제든지 변경할 수 있습니다.

1. 선택적으로 **대체 관용구를 추가합니다.** 대체 관용구는 원래 질문과 크게 다르지만 동일한 답변을 제공해야 하는 모든 형태의 질문입니다.

    기술 자료가 게시되고 활성 [학습이](use-active-learning.md) 켜져 있으면 QnA Maker는 수락할 대체 관용구 선택 항목을 수집합니다. 이러한 선택 사항은 예측 정확도를 높이기 위해 선택됩니다.

1. 선택적으로 **메타데이터를**추가합니다. 메타데이터를 보려면 컨텍스트 메뉴에서 **보기 옵션을** 선택합니다. 메타데이터는 챗봇과 같은 클라이언트 응용 프로그램이 제공하는 답변에 대한 필터를 제공합니다.

1. 선택적으로 **후속 프롬프트를 추가합니다.** 후속 프롬프트는 사용자에게 제공할 클라이언트 응용 프로그램에 대한 추가 대화 경로를 제공합니다.

1. **저장 및 학습을** 선택하여 새 QnA 쌍을 포함한 예측을 확인합니다.

## <a name="edit-a-qna-pair"></a>QnA 쌍 편집

원래 데이터 원본에 관계없이 QnA 쌍의 모든 필드를 편집할 수 있습니다. 컨텍스트 도구 모음에 있는 현재 **옵션 보기** 설정으로 인해 일부 필드가 표시되지 않을 수 있습니다.

## <a name="delete-a-qna-pair"></a>QnA 쌍 삭제

QnA를 삭제하려면 QnA 행의 맨 오른쪽에 있는 **삭제** 아이콘을 클릭합니다. 영구 작업입니다. 실행을 취소할 수 없습니다. 세트를 삭제하기 전에 **게시** 페이지에서 KB를 내보내는 것이 좋습니다.

![QnA 쌍 삭제](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>QnA 쌍 ID 찾기

QnA 쌍 ID를 찾아야 하는 경우 다음 두 위치에서 찾을 수 있습니다.

* 관심 있는 QnA 쌍 행의 삭제 아이콘을 마우스로 가리킵니다. 가리키기 텍스트에는 QnA 쌍 ID가 포함됩니다.
* 기술 자료 내보내기 기술 자료의 각 QnA 쌍에는 QnA 쌍 ID가 포함됩니다.

## <a name="add-alternate-questions"></a>대체 질문 추가

기존 QnA 쌍에 대체 질문을 추가하여 사용자 쿼리와 일치할 가능성을 높입니다.

![대체 질문 추가](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA 쌍 연결

QnA 쌍을 연결하는 것은 [후속 프롬프트와](multiturn-conversation.md)함께 제공됩니다. 이는 기술 자료 수준에서 관리되는 QnA 쌍 간의 논리적 연결입니다. QnA Maker 포털에서 후속 프롬프트를 편집할 수 있습니다.

답변의 메타데이터에 QnA 쌍을 연결할 수 없습니다.

## <a name="add-metadata"></a>메타데이터 추가

먼저 **보기 옵션을**선택한 다음 메타데이터 표시를 선택하여 **메타데이터 집합을**추가합니다. 그러면 메타데이터 열이 표시됩니다. 다음으로 기호를 **+** 선택하여 메타데이터 집합을 추가합니다. 이 집합은 하나의 키와 하나의 값으로 구성됩니다.

## <a name="save-changes-to-the-qna-pairs"></a>QnA 쌍에 변경 사항 저장

변경 내용을 잃지 않도록 편집한 후 저장 **및 학습을** 주기적으로 선택합니다.

![메타데이터 추가](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](./collaborate-knowledge-base.md)

* [QnA 메이커에서 사용하는 Azure 리소스 관리](set-up-qnamaker-service-azure.md)