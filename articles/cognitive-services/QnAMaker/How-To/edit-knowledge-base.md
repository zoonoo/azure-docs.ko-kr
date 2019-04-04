---
title: 기술 자료 편집 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 22d408204b69e0a564103efd29468c6f0d68d93a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577207"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>QnA Maker의 기술 자료 편집

QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>기술 자료 콘텐츠 편집

1.  맨 위 탐색 모음에서 **내 기술 자료**를 선택합니다. 

    직접 만들었거나 공유된 모든 서비스를 **마지막으로 수정한 날짜**의 내림차순으로 볼 수 있습니다.

    ![내 기술 자료](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. 특정 기술 자료를 선택하여 편집합니다.
 
1. **설정**을 선택합니다. 여기에서 필수 필드인 서비스 이름을 편집할 수 있습니다.
  
    |목표|조치|
    |--|--|
    |URL 추가|**기술 자료 관리 -> '+ URL 추가'** 링크를 클릭하여 기술 자료에 새 FAQ 콘텐츠를 추가할 새 URL을 추가할 수 있습니다.|
    |URL 삭제|삭제 아이콘, 휴지통을 선택하여 기존 URL을 삭제할 수 있습니다.|
    |URL 콘텐츠 새로 고침|기술 자료에서 기존 URL의 최신 콘텐츠를 탐색하도록 하려면 **새로 고침** 확인란을 선택합니다. 이렇게 하면 최신 URL 콘텐츠로 기술 자료가 업데이트됩니다.|
    |파일 추가|**기술 자료 관리**를 선택한 다음, **+ 파일 추가**를 선택하여 기술 자료의 일부가 되도록 지원되는 파일 문서를 추가할 수 있습니다.|
    |가져오기|**기술 자료 가져오기** 단추를 선택하여 기존 기술 자료를 가져올 수도 있습니다. |
    |주 지역에서|기술 자료의 업데이트는 기술 자료와 관련된 QnA Maker 서비스를 만드는 동안 사용되는 **관리 가격 책정 계층**에 따라 달라집니다. 필요한 경우 Azure Portal에서 관리 계층을 업데이트할 수도 있습니다.

1. 기술 자료 변경을 마쳤으면 변경 내용을 유지하기 위해 페이지의 오른쪽 맨 위 모서리에 있는 **저장 후 학습**을 선택합니다.    

    ![저장 후 학습](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >**저장 후 학습**을 선택하기 전에 페이지를 나가면 모든 변경 내용이 손실됩니다.

## <a name="add-a-qna-pair"></a>QnA 쌍 추가

에 **설정을** 페이지에서 **QnA 추가 쌍** 기술 자료 테이블에 새 행을 추가 하려면.

![QnA 쌍 추가](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>QnA 쌍 삭제

QnA를 삭제하려면 QnA 행의 맨 오른쪽에 있는 **삭제** 아이콘을 클릭합니다. 영구 작업입니다. 실행을 취소할 수 없습니다. 쌍을 삭제하기 전에 **게시** 페이지에서 KB를 내보내는 것이 좋습니다. 

![QnA 쌍 삭제](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>대체 질문 추가

기존 QnA 쌍에 대체 질문을 추가하여 사용자 쿼리와 일치할 가능성을 높입니다.

![대체 질문 추가](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>메타데이터 추가


메타 데이터 아이콘을 선택 하 여 메타 데이터 쌍을 추가 합니다. 메타 데이터 쌍을 하나의 키 한 개와 값으로 구성 됩니다.

![메타데이터 추가](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> 변경 내용이 손실되지 않도록 하려면 편집한 후 기술 자료를 주기적으로 저장하고 학습해야 합니다.

## <a name="manage-large-knowledge-bases"></a>큰 기술 자료 관리

* **데이터 원본 그룹**: QnA는 추출된 데이터 원본에 따라 그룹화됩니다. 데이터 원본을 확장하거나 축소할 수 있습니다.

    ![QnA Maker 데이터 원본 막대를 사용하여 데이터 원본 질문과 답변을 축소 및 확장합니다.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **기술 자료 검색**: 기술 자료 표의 맨 위에 있는 텍스트 상자에 입력하여 기술 자료를 검색할 수 있습니다. 질문, 답변 또는 메타데이터 콘텐츠를 검색하려면 Enter 키를 클릭합니다. 검색 필터를 제거하려면 X 아이콘을 클릭합니다.

    ![질문과 답변 위의 QnA Maker 검색 상자를 사용하여 필터 일치 항목으로만 뷰를 줄입니다.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **페이지 매김**: 대규모 기술 자료 관리를 위해 신속하게 데이터 원본 이동

    ![질문과 답변 위의 QnA Maker 페이지 매김 기능을 사용하여 질문과 답변 페이지를 이동합니다.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>기술 자료 삭제

KB(기술 자료)를 삭제하는 것은 영구 작업입니다. 실행을 취소할 수 없습니다. 기술 자료를 삭제하기 전에 QnA Maker 포털의 **설정** 페이지에서 기술 자료를 내보내야 합니다. 

[협력자](collaborate-knowledge-base.md)와 KB를 공유한 다음, 삭제하는 경우 모든 사용자가 KB에 액세스할 수 없습니다. 

## <a name="delete-azure-resources"></a>Azure 리소스 삭제 

QnA Maker 기술 자료에 사용되는 Azure 리소스를 삭제하는 경우 기술 자료는 더 이상 작동하지 않습니다. 모든 리소스를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](./collaborate-knowledge-base.md)
