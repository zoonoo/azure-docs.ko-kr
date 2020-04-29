---
title: Video Indexer 웹 사이트로 Person 모델 사용자 지정
titleSuffix: Azure Media Services
description: Video Indexer 웹 사이트를 사용 하 여 사용자 모델을 사용자 지정 하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499960"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정

Video Indexer는 비디오 콘텐츠에 대 한 유명인 인식을 지원 합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 자세한 개요는 [Video Indexer에서 사용자 모델 사용자 지정](customize-person-model-overview.md)을 참조 하세요.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. Api를 [사용 하 여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명 된 대로 api를 사용할 수도 있습니다.

## <a name="central-management-of-person-models-in-your-account"></a>계정의 개인 모델 중앙 관리

1. 계정에서 사용자 모델을 보고, 편집 하 고, 삭제 하려면 Video Indexer 웹 사이트로 이동 하 여 로그인 합니다.

2. 페이지의 오른쪽 위 모서리에서 콘텐츠 모델 사용자 지정 단추를 선택 합니다.

    ![콘텐츠 모델 사용자 지정](./media/customize-face-model/content-model-customization.png)

3. 사용자 탭을 선택 합니다.

    계정에 기본 사용자 모델이 표시 됩니다. 기본 사용자 모델은 인덱싱을 수행 하는 동안 사용자 지정 사용자 모델을 지정 하지 않은 비디오 정보에서 편집 또는 변경 되었을 수 있는 모든 얼굴을 보유 합니다.

    다른 사람 모델을 만든 경우이 페이지에도 나열 됩니다.

    ![콘텐츠 모델 사용자 지정](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

1. **+ 모델 추가** 단추를 선택 합니다.

    ![새 사용자 모델 추가](./media/customize-face-model/add-new-person.png)

2. 모델 이름을 입력 하 고 이름 옆에 있는 확인 단추를 선택 합니다.

    ![새 사용자 모델 추가](./media/customize-face-model/add-new-person2.png)

    새 사람 모델을 만들었습니다. 이제 새 사람 모델에 얼굴을 추가할 수 있습니다.

3. 목록 메뉴 단추를 선택 하 고 **+ 사용자 추가**를 선택 합니다.

    ![새 사용자 모델 추가](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>Person 모델에 새 사용자 추가

> [!NOTE]
> Video Indexer를 사용 하 여 사용자 모델에서 이름이 같은 여러 사용자를 추가할 수 있습니다. 그러나 유용성 및 명확성을 위해 모델의 각 사용자에 게 고유한 이름을 지정 하는 것이 좋습니다.

1. Person 모델에 새 얼굴을 추가 하려면 얼굴을 추가 하려는 Person 모델 옆의 목록 메뉴 단추를 선택 합니다.

1. 메뉴에서 **+ 사용자 추가** 를 선택 합니다.

    ![사용자에 게 새 얼굴 추가](./media/customize-face-model/add-new-face.png)

    팝업에 사용자의 세부 정보를 입력 하 라는 메시지가 표시 됩니다. 사용자의 이름을 입력 하 고 확인 단추를 선택 합니다.

    ![사용자에 게 새 얼굴 추가](./media/customize-face-model/add-new-face2.png)

    그런 다음 파일 탐색기에서 선택 하거나 얼굴 이미지를 끌어서 놓을 수 있습니다. Video Indexer는 모든 표준 이미지 파일 형식 (예: JPG, PNG 등)을 가져옵니다.

    이 새 얼굴을 추가한 사람 모델을 사용 하 여 인덱싱하는 이후 비디오와 이미 인덱싱한 현재 비디오에서이 사용자의 발생을 검색할 수 Video Indexer. 일괄 처리 프로세스 이므로 현재 비디오의 사용자를 인식 하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="rename-a-person-model"></a>개인 모델 이름 바꾸기

기본 사람 모델을 포함 하 여 계정에서 모든 사용자 모델의 이름을 바꿀 수 있습니다. 기본 사용자 모델의 이름을 바꾸는 경우에도 계정의 기본 사용자 모델로 계속 사용 됩니다.

1. 이름을 바꿀 Person 모델 옆의 목록 메뉴 단추를 선택 합니다.
2. 메뉴에서 **이름 바꾸기**를 선택합니다.

    ![개인 모델 이름 바꾸기](./media/customize-face-model/rename-person.png)

3. 모델의 현재 이름을 선택 하 고 새 이름을 입력 합니다.

    ![개인 모델 이름 바꾸기](./media/customize-face-model/rename-person2.png)

4. 이름을 바꿀 모델의 확인 단추를 선택 합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

계정에서 만든 모든 사용자 모델을 삭제할 수 있습니다. 그러나 기본 사용자 모델을 삭제할 수는 없습니다.

1. 메뉴에서 **삭제**를 선택합니다.

    ![사용자 모델 삭제](./media/customize-face-model/delete-person.png)

    팝업이 표시 되 고이 작업에서 개인 모델과 여기에 포함 된 모든 사용자 및 파일을 삭제 한다는 알림이 표시 됩니다. 이 작업은 실행 취소할 수 없습니다.

    ![사용자 모델 삭제](./media/customize-face-model/delete-person2.png)

1. 잘 모르겠으면 삭제를 다시 선택 합니다.

> [!NOTE]
> 이 사용자 모델을 사용 하 여 인덱싱한 기존 비디오는 비디오에 표시 되는 얼굴의 이름을 업데이트 하는 기능을 지원 하지 않습니다. 다른 사람 모델을 사용 하 여 인덱스를 다시 만든 후에만 이러한 비디오에서 얼굴 이름을 편집할 수 있습니다. Person 모델을 지정 하지 않고 인덱스를 변경한 경우 기본 모델이 사용 됩니다.

## <a name="manage-existing-people-in-a-person-model"></a>사용자 모델에서 기존 사용자 관리

사용자 모델의 콘텐츠를 보려면 Person 모델 이름 옆의 화살표를 선택 합니다. 드롭다운은 해당 특정 사용자 모델의 모든 사용자를 표시 합니다. 각 사람들 옆의 목록 메뉴 단추를 선택 하면 관리, 이름 바꾸기 및 삭제 옵션이 표시 됩니다.  

![사용자에 게 새 얼굴 추가](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>개인 이름 바꾸기

1. 사용자 모델에서 개인의 이름을 바꾸려면 목록 메뉴 단추를 선택 하 고 목록 메뉴에서 **이름 바꾸기** 를 선택 합니다.
1. 사용자의 현재 이름을 선택 하 고 새 이름을 입력 합니다.
1. 확인 단추를 선택 하면 해당 사용자의 이름이 변경 됩니다.

### <a name="delete-a-person"></a>사람 삭제

1. 사용자 모델에서 사용자를 삭제 하려면 목록 메뉴 단추를 선택 하 고 목록 메뉴에서 **삭제** 를 선택 합니다.
1. 팝업은이 작업을 통해 사용자가 삭제 되 고이 작업은 실행 취소할 수 없음을 나타냅니다.
1. **삭제** 를 다시 선택 하면 person 모델에서 해당 사용자가 제거 됩니다.

### <a name="manage-a-person"></a>사용자 관리

**관리**를 선택 하는 경우이 사용자 모델을 학습 하는 모든 얼굴을 볼 수 있습니다. 이러한 얼굴은이 사람 모델을 사용 하는 비디오 또는 수동으로 업로드 한 이미지에서 해당 사용자가 발생 하는 경우에 발생 합니다.

**이미지 추가**를 선택 하 여 사용자에 게 얼굴을 더 추가할 수 있습니다.

관리 창을 사용 하 여 개인의 이름을 바꾸고 person 모델에서 사용자를 삭제할 수 있습니다.

## <a name="use-a-person-model-to-index-a-video"></a>사용자 모델을 사용 하 여 비디오 인덱싱

사용자 모델을 사용 하 여 비디오를 업로드 하는 동안 개인 모델을 할당 하 여 새 비디오를 인덱싱할 수 있습니다.

새 비디오에서 사용자 모델을 사용 하려면 다음 단계를 수행 합니다.

1. 페이지 맨 위에서 **업로드** 단추를 선택 합니다.

    ![사용자 모델 업로드](./media/customize-face-model/upload.png)

1. 원형에서 비디오 파일을 삭제 하거나 파일을 찾습니다.
1. **고급 옵션** 화살표를 선택 합니다.

    ![사용자 모델 업로드](./media/customize-face-model/upload2.png)

1. 드롭다운을 선택 하 고 만든 Person 모델을 선택 합니다.

    ![사용자 모델 업로드](./media/customize-face-model/upload3.png)

1. 페이지 맨 아래에서 **업로드** 옵션을 선택 하면 사용자 모델을 사용 하 여 새 비디오가 인덱싱됩니다.

업로드 중에 Person 모델을 지정 하지 않으면 Video Indexer는 계정의 기본 사용자 모델을 사용 하 여 비디오를 인덱싱합니다.

## <a name="use-a-person-model-to-reindex-a-video"></a>사용자 모델을 사용 하 여 비디오의 인덱스를 만듭니다.

사용자 모델을 사용 하 여 컬렉션의 비디오를 다시 인덱싱 하려면 Video Indexer 홈페이지의 계정 비디오로 이동 하 고 다시 인덱싱 하려는 비디오의 이름을 마우스로 가리킵니다.

비디오를 편집, 삭제 및 재 인덱싱 하는 옵션이 표시 됩니다.

1. 비디오를 재 인덱싱 하는 옵션을 선택 합니다.

    ![Person 모델을 사용 하 여 비디오 인덱스를 만듭니다.](./media/customize-face-model/reindex.png)

    이제를 사용 하 여 비디오를 인덱스를 만들 사람 모델을 선택할 수 있습니다.
1. 드롭다운을 선택 하 고 사용 하려는 개인 모델을 선택 합니다.

    ![Person 모델을 사용 하 여 비디오 인덱스를 만듭니다.](./media/customize-face-model/reindex2.png)

1. **재 인덱싱** 단추를 선택 하면 사용자 모델을 사용 하 여 비디오가 인덱싱해야 됩니다.

방금 인덱싱해야 비디오에서 검색 되 고 인식 되는 얼굴에 대 한 모든 새 편집은 비디오를 인덱스를 만드는 데 사용한 Person 모델에 저장 됩니다.

## <a name="managing-people-in-your-videos"></a>비디오의 사용자 관리

얼굴을 편집 하 고 삭제 하 여 인덱싱하는 비디오에서 인식 되는 얼굴 및 검색 된 사용자를 관리할 수 있습니다.

얼굴을 삭제 하면 비디오의 정보에서 특정 얼굴이 제거 됩니다.

얼굴을 편집 하면 비디오에서 검색 되 고 인식 되는 얼굴의 이름이 바뀝니다. 비디오에서 얼굴을 편집 하는 경우 해당 이름은 업로드 및 인덱싱을 수행 하는 동안 비디오에 할당 된 person 모델에 사람 항목으로 저장 됩니다.

업로드 중에 사용자 모델을 비디오에 할당 하지 않으면 사용자 계정의 기본 사용자 모델에 편집 내용이 저장 됩니다.

### <a name="edit-a-face"></a>얼굴 편집

> [!NOTE]
> 사용자 모델에 이름이 같은 사용자가 두 명 이상 있는 경우 해당 사용자 모델을 사용 하는 비디오 내에서 해당 이름의 태그를 지정할 수 없습니다. Video Indexer 콘텐츠 모델 사용자 지정 페이지의 사용자 탭에서 해당 이름을 공유 하는 사용자만 변경할 수 있습니다. 따라서 사용자 모델에서 각 사용자에 게 고유한 이름을 지정 하는 것이 좋습니다.

1. Video Indexer 웹 사이트로 이동하고 로그인합니다.
1. 계정에서 보고 편집하려는 비디오를 검색합니다.
1. 비디오에서 얼굴을 편집 하려면 Insights 탭으로 이동 하 고 창의 오른쪽 위 모퉁이에 있는 연필 아이콘을 선택 합니다.

    ![비디오에서 얼굴 편집](./media/customize-face-model/edit-face.png)

1. 검색 된 얼굴 중 하나를 선택 하 고 "알 수 없는 #X" (또는 이전에 면에 할당 된 이름)에서 이름을 변경 합니다.
1. 새 이름을 입력 한 후 새 이름 옆의 확인 아이콘을 선택 합니다. 이 작업은 새 이름을 저장 하 고 다른 현재 비디오 및 업로드 하는 이후 비디오에서이 얼굴의 모든 항목을 인식 하 고 이름을 표시 합니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다.

비디오에서 사용 하 고 있는 개인 모델에서 기존 사람의 이름으로 얼굴 이름을로 하는 경우 해당 사용자의 비디오에서 검색 된 얼굴 이미지가 모델에 이미 있는 항목과 병합 됩니다. 새 이름으로 얼굴 이름을로 하는 경우 비디오에서 사용 하는 개인 모델에 새 사용자 항목이 생성 됩니다.

![비디오에서 얼굴 편집](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>얼굴 삭제

비디오에서 검색 된 얼굴을 삭제 하려면 Insights 창으로 이동 하 고 창의 오른쪽 위 모퉁이에 있는 연필 아이콘을 선택 합니다. 얼굴 이름 아래에서 **삭제** 옵션을 선택 합니다. 이 작업은 비디오에서 검색 된 얼굴을 제거 합니다. 사용자의 얼굴은 표시 되는 다른 비디오에서 여전히 검색 되지만, 해당 비디오를 인덱싱한 후에도 해당 비디오에서 얼굴을 삭제할 수 있습니다.

이름이 지정 된 사용자는 사용자 모델에서 사용자를 특별히 삭제 하지 않는 한 얼굴을 삭제 한 비디오를 인덱싱하는 데 사용 된 사람 모델에도 계속 존재 합니다.

![비디오에서 얼굴 삭제](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>다음 단계

[API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)
