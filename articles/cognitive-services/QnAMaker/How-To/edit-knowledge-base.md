---
title: 기술 자료 편집 - QnA Maker
description: QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756728"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>기술 자료에서 QnA 쌍 편집

QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.

QnA 쌍은 파일 또는 URL과 같은 데이터 원본에서 추가 되거나 편집 소스로 추가 됩니다. 편집 원본은 QnA 쌍이 QnA 포털에 수동으로 추가 되었음을 나타냅니다. 모든 QnA 쌍을 편집할 수 있습니다.

## <a name="add-an-editorial-qna-pair"></a>편집 QnA 쌍 추가
1. [QnA 포털](https://www.qnamaker.ai/)에 로그인 한 후 기술 자료를 선택 하 여 QnA 쌍을 추가 합니다.
1. 기술 자료의 **편집** 페이지에서 **QnA pair 추가** 를 선택 하 여 새 QnA 쌍을 추가 합니다.

1. New QnA pair 행에서 필요한 **질문** 및 **대답** 필드를 추가 합니다. 다른 필드는 선택 사항입니다. 모든 필드는 언제 든 지 변경할 수 있습니다.

1. 선택적으로 **대체**구문을 추가 합니다. 대체 구문 이란 원래 질문과 크게 다르지만 동일한 답을 제공 해야 하는 모든 형태의 질문입니다.

    기술 자료가 게시 되 고 [활성 학습](use-active-learning.md) 기능이 설정 되어 있는 경우에는 사용자가 동의할 수 있는 대체 구문 옵션을 수집 QnA Maker 수집 합니다. 이러한 선택 항목은 예측 정확도를 높이기 위해 선택 됩니다.

1. 필요에 따라 **메타 데이터**를 추가 합니다. 메타 데이터를 보려면 상황에 맞는 메뉴에서 **옵션 보기** 를 선택 합니다. 메타 데이터는 채팅 봇과 같은 클라이언트 응용 프로그램에서 제공 하는 응답에 대 한 필터를 제공 합니다.

1. 필요에 **따라 추가 작업 프롬프트**를 추가 합니다. 추가 작업 프롬프트는 클라이언트 응용 프로그램에 사용자에 게 제공할 추가 대화 경로를 제공 합니다.

1. **저장 및 학습** 을 선택 하 여 새 QnA 쌍을 포함 하는 예측을 확인 합니다.

## <a name="edit-a-qna-pair"></a>QnA 쌍 편집

QnA 쌍의 모든 필드는 원래 데이터 원본에 관계 없이 편집할 수 있습니다. 일부 필드는 상황에 맞는 도구 모음에 있는 현재 **뷰 옵션** 설정 때문에 표시 되지 않을 수 있습니다.

## <a name="delete-a-qna-pair"></a>QnA 쌍 삭제

QnA를 삭제하려면 QnA 행의 맨 오른쪽에 있는 **삭제** 아이콘을 클릭합니다. 영구 작업입니다. 실행을 취소할 수 없습니다. 집합을 삭제 하기 전에 **게시** 페이지에서 KB를 내보내는 것이 좋습니다.

![QnA 쌍 삭제](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>QnA pair ID 찾기

QnA pair ID를 찾아야 하는 경우 다음 두 위치에서 찾을 수 있습니다.

* 관심이 있는 QnA pair 행의 삭제 아이콘을 마우스로 가리킵니다. 가리키기 텍스트는 QnA pair ID를 포함 합니다.
* 기술 자료를 내보냅니다. 기술 자료의 각 QnA 쌍은 QnA 쌍 ID를 포함 합니다.

## <a name="add-alternate-questions"></a>대체 질문 추가

기존 QnA 쌍에 대체 질문을 추가하여 사용자 쿼리와 일치할 가능성을 높입니다.

![대체 질문 추가](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA 쌍 연결

QnA 쌍 연결은 [추가 작업 프롬프트](multiturn-conversation.md)와 함께 제공 됩니다. 이는 기술 자료 수준에서 관리 되는 QnA 쌍 간의 논리적 연결입니다. QnA Maker 포털에서 추가 작업 프롬프트를 편집할 수 있습니다.

응답의 메타 데이터에 QnA 쌍을 연결할 수 없습니다.

## <a name="add-metadata"></a>메타데이터 추가

먼저 **보기 옵션**을 선택 하 고 **메타 데이터 표시**를 선택 하 여 메타 데이터 집합을 추가 합니다. 메타 데이터 열이 표시 됩니다. 그런 다음, **+** 서명을 선택 하 여 메타 데이터 집합을 추가 합니다. 이 집합은 하나의 키와 하나의 값으로 구성 됩니다.

## <a name="save-changes-to-the-qna-pairs"></a>QnA 쌍의 변경 내용 저장

변경 내용이 손실 되지 않도록 편집 후 **저장 및 학습** 을 주기적으로 선택 합니다.

![메타데이터 추가](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](./collaborate-knowledge-base.md)

* [QnA Maker에서 사용 하는 Azure 리소스 관리](set-up-qnamaker-service-azure.md)