---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 웹 사이트에서 언어 모델 사용자 지정
titleSuffix: Azure Media Services
description: Azure Video Analyzer for Media(이전의 Video Indexer) 웹 사이트에서 언어 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: 4f1d5e708755828fb2193ffca6275684418110d5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386405"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-website"></a>Video Analyzer for Media 웹 사이트에서 언어 모델 사용자 지정

Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Analyzer for Media를 사용하여 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

Video Analyzer for Media 웹 사이트에서 이 항목에 설명된 대로 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

1. [Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트로 이동하여 로그인합니다.
1. 계정에서 모델을 사용자 지정하려면 페이지 왼쪽에서 **콘텐츠 모델 사용자 지정** 단추를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Video Analyzer for Media에서 콘텐츠 모델 사용자 지정":::
1. **언어** 탭을 선택합니다.

    지원되는 언어 목록이 표시됩니다.
1. 원하는 언어에서 **모델 추가** 를 선택합니다.
1. 언어 모델의 이름을 입력하고 Enter 키를 누릅니다.

    이 단계에서는 모델이 생성되고 모델에 텍스트 파일을 업로드하는 옵션이 제공됩니다.
1. 텍스트 파일을 추가하려면 **파일 추가** 를 선택합니다. 파일 탐색기가 열립니다.
1. 텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

    언어 모델의 오른쪽에 있는 **...** 단추를 선택하고 **파일 추가** 를 선택하여 텍스트 파일을 추가할 수도 있습니다.
1. 텍스트 파일 업로드를 완료했으면 녹색 **학습** 옵션을 선택합니다.

학습 프로세스는 몇 분 정도 걸릴 수 있습니다. 학습이 완료되면 모델 옆에 **학습됨** 이 표시됩니다. 모델에서 파일을 미리 보고 다운로드하고 삭제할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="모델 학습":::

### <a name="using-a-language-model-on-a-new-video"></a>새로운 비디오에서 언어 모델 사용

새로운 비디오에서 언어 모델을 사용하려면 다음 작업 중 하나를 수행합니다.

* 페이지 맨 위에 있는 **업로드** 단추를 선택합니다.

    ![업로드 단추 Video Analyzer for Media](./media/customize-language-model/upload.png)
* 오디오 또는 비디오 파일을 놓거나 파일을 찾습니다.

**비디오 소스 언어** 를 선택할 수 있는 옵션이 제공됩니다. 드롭다운을 선택하고 목록에서 직접 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다. 예를 들면 다음과 같습니다.

![비디오 소스 언어 선택 - Video Analyzer for Media로 비디오 인덱스 다시 인덱싱](./media/customize-language-model/reindex.png)

페이지 맨 아래에 있는 **업로드** 옵션을 선택하면 새 비디오가 언어 모델을 사용하여 인덱싱됩니다.

### <a name="using-a-language-model-to-reindex"></a>언어 모델을 사용하여 다시 인덱싱

언어 모델을 사용하여 컬렉션의 비디오를 다시 인덱싱하려면 다음 단계를 수행합니다.

1. [Video Analyzer for Media](https://www.videoindexer.ai/) 홈페이지에 로그인합니다.
1. 비디오에서 **...** 단추를 클릭하고 **다시 인덱싱** 을 선택합니다.
1. 그러면 비디오를 다시 인덱싱하는 데 사용할 **비디오 소스 언어** 를 선택할 수 있는 옵션이 제공됩니다. 드롭다운을 선택하고 목록에서 직접 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.
1. **다시 인덱싱** 단추를 선택하여 비디오가 언어 모델을 사용하여 다시 인덱싱됩니다.

## <a name="edit-a-language-model"></a>언어 모델 편집

이름을 변경하고 파일을 추가 및 삭제하여 언어 모델을 편집할 수 있습니다.

언어 모델에서 파일을 추가하거나 삭제하는 경우 녹색 **학습** 옵션을 선택하여 모델을 다시 학습해야 합니다.

### <a name="rename-the-language-model"></a>언어 모델 이름 바꾸기

언어 모델의 오른쪽에 있는 줄임표( **...** ) 단추를 선택하고 **이름 바꾸기** 를 선택하여 언어 모델의 이름을 변경할 수 있습니다.

새 이름을 입력하고 Enter 키를 누릅니다.

### <a name="add-files"></a>파일 추가

텍스트 파일을 추가하려면 **파일 추가** 를 선택합니다. 파일 탐색기가 열립니다.

텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

언어 모델의 오른쪽에 있는 줄임표( **...** ) 단추를 선택하고 **파일 추가** 를 선택하여 텍스트 파일을 추가할 수도 있습니다.

### <a name="delete-files"></a>파일 삭제

언어 모델에서 파일을 삭제하려면 텍스트 파일의 오른쪽에 있는 줄임표( **...** ) 단추를 선택하고 **삭제** 를 선택합니다. 삭제를 실행 취소할 수 없음을 알리는 새 창이 팝업됩니다. 새 창에서 **삭제** 옵션을 선택합니다.

이 작업은 언어 모델에서 파일을 완전히 제거합니다.

## <a name="delete-a-language-model"></a>언어 모델 삭제

계정에서 언어 모델을 삭제하려면 언어 모델의 오른쪽에 있는 줄임표( **...** ) 단추를 선택하고 **삭제** 를 선택합니다.

삭제를 실행 취소할 수 없음을 알리는 새 창이 팝업됩니다. 새 창에서 **삭제** 옵션을 선택합니다.

이 작업은 사용자 계정에서 언어 모델을 완전히 제거합니다. 삭제된 언어 모델을 사용하던 모든 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Analyzer for Media는 기본 모델을 사용하여 비디오를 다시 인덱싱합니다.

## <a name="customize-language-models-by-correcting-transcripts"></a>대본을 수정하여 언어 모델 사용자 지정

Video Analyzer for Media는 사용자가 비디오의 대본에 대해 수정한 실제 내용에 따라 언어 모델의 자동 사용자 지정을 지원합니다.

1. 대본을 수정하려면 계정 비디오에서 편집하려는 비디오를 엽니다. **타임라인** 탭을 선택합니다.

    ![언어 모델 타임라인 탭 사용자 지정—Video Analyzer for Media](./media/customize-language-model/timeline.png)

1. 연필 아이콘을 선택하여 대화 내용의 대본을 편집합니다.

    ![언어 모델 대본 편집 사용자 지정—Video Analyzer for Media](./media/customize-language-model/edits.png)

    Video Analyzer for Media는 비디오의 대본에서 사용자가 수정한 모든 줄을 캡처한 후, "원본 대본 편집"이라는 텍스트 파일에 자동으로 추가합니다. 이러한 편집 내용은 이 비디오를 인덱싱하는 데 사용된 특정 언어 모델을 다시 학습하는 데 사용됩니다. 
    
    [위젯의](video-indexer-embed-widgets.md) 타임라인에서 수행된 편집도 포함됩니다.
    
    이 비디오를 인덱싱할 때 언어 모델을 지정하지 않은 경우 이 비디오에 대한 모든 편집 내용이 검색된 비디오 언어 내에서 "계정 적응"이라는 기본 언어 모델에 저장됩니다.
    
    동일한 줄을 여러 차례 편집한 경우 수정된 줄의 마지막 버전만 언어 모델을 업데이트하는 데 사용됩니다.  
    
    > [!NOTE]
    > 사용자 지정에는 텍스트 수정만 사용됩니다. 실제 단어(예: 문장 부호 또는 공백)가 포함되지 않은 수정은 포함되지 않습니다.
    
1. 콘텐츠 모델 사용자 지정 페이지의 언어 탭에 대본 수정 내용이 표시됩니다.

   각 언어 모델에 대한 "원본 대본 편집" 파일을 보려면 선택하여 엽니다.

    ![대본 편집에서—Video Analyzer for Media](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>다음 단계

[API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)
