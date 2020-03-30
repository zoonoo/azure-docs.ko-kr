---
title: 비디오 인덱서 웹 사이트로 언어 모델 사용자 지정
titleSuffix: Azure Media Services
description: 비디오 인덱서 웹 사이트에서 언어 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128083"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다.

사용자 지정 언어 모델에 대한 자세한 개요 및 모범 사례는 [비디오 인덱서를 사용 하](customize-language-model-overview.md)여 언어 모델 사용자 지정을 참조 하십시오.

이 항목에 설명된 대로 Video Indexer 웹 사이트를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

1. [비디오 인덱서](https://www.videoindexer.ai/) 웹 사이트로 이동하여 로그인합니다.
2. 계정에서 모델을 사용자 지정하려면 페이지의 오른쪽 상단 모서리에 있는 **콘텐츠 모델 사용자 지정** 단추를 선택합니다.

   ![비디오 인덱서에서 콘텐츠 모델 사용자 지정](./media/content-model-customization/content-model-customization.png)

3. **언어** 탭을 선택합니다.

    지원되는 언어 목록이 표시됩니다.

    ![비디오 인덱서의 언어 모델 목록](./media/customize-language-model/customize-language-model.png)

4. 원하는 언어에서 **모델 추가를**선택합니다.
5. 언어 모델의 이름을 입력하고 Enter 키를 누릅니다.

    이 단계에서는 모델을 만들고 텍스트 파일을 모델에 업로드할 수 있는 옵션을 제공합니다.

6. 텍스트 파일을 추가하려면 **파일 추가를**선택합니다. 파일 탐색기가 열립니다.

7. 텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

    언어 모델의 오른쪽에 있는 **...단추를** 선택하고 **파일 추가를**선택하여 텍스트 파일을 추가할 수도 있습니다.

8. 텍스트 파일 업로드가 완료되면 녹색 **기차** 옵션을 선택합니다.

    ![비디오 인덱서의 언어 모델 교육](./media/customize-language-model/train-model.png)

학습 프로세스는 몇 분 정도 걸릴 수 있습니다. 학습이 완료되면 모델 옆에 **학습됨**이 표시됩니다. 모델에서 파일을 미리 보고 다운로드하고 삭제할 수 있습니다.

![비디오 인덱서의 학습된 언어 모델](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>새로운 비디오에서 언어 모델 사용

새 동영상에서 언어 모델을 사용하려면 다음 작업 중 하나를 수행합니다.

* 페이지 상단의 **업로드** 버튼을 선택합니다.

    ![업로드 버튼 비디오 인덱서](./media/customize-language-model/upload.png)

* 오디오 또는 비디오 파일을 원에 놓거나 파일을 찾아봅슬합니다.

    ![미디어 파일 비디오 인덱서 업로드](./media/customize-language-model/upload2.png)

**비디오 소스 언어를**선택할 수 있는 옵션이 제공됩니다. 드롭다운을 선택하고 목록에서 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

페이지 하단에서 **업로드** 옵션을 선택하면 언어 모델을 사용하여 새 동영상이 색인이 생성됩니다.

### <a name="using-a-language-model-to-reindex"></a>언어 모델을 사용하여 다시 인덱싱

언어 모델을 사용하여 컬렉션의 비디오를 다시 인덱싱하려면 [Video Indexer](https://www.videoindexer.ai/) 홈페이지의 **계정 비디오**로 이동하여 다시 인덱싱할 비디오의 이름을 커서로 가리킵니다.

동영상을 편집하고, 동영상을 삭제하고, 동영상을 다시 인덱싱하는 옵션이 표시됩니다. 비디오 인덱싱 옵션을 선택합니다.

![비디오 인덱서로 다시 인덱싱](./media/customize-language-model/reindex1.png)

**동영상 소스 언어를** 선택하여 동영상을 다시 인덱싱할 수 있는 옵션이 제공됩니다. 드롭다운을 선택하고 목록에서 만든 언어 모델을 선택합니다. 언어 모델의 언어와 지정한 이름이 괄호 안에 표시되어야 합니다.

![비디오 소스 언어 선택- 비디오 인덱서를 사용 하 고 비디오 색인 을 다시 색인](./media/customize-language-model/reindex.png)

다시 **색인** 단추를 선택하면 언어 모델을 사용하여 비디오가 다시 인덱싱됩니다.

## <a name="edit-a-language-model"></a>언어 모델 편집

이름을 변경하고 파일을 추가 및 삭제하여 언어 모델을 편집할 수 있습니다.

언어 모델에서 파일을 추가하거나 삭제하는 경우 녹색 **Train** 옵션을 선택하여 모델을 다시 학습해야 합니다.

### <a name="rename-the-language-model"></a>언어 모델 이름 바꾸기

언어 모델의 오른쪽에 있는 타원 **(...**) 단추를 선택하고 **이름 바꾸기를**선택하여 언어 모델의 이름을 변경할 수 있습니다.

새 이름을 입력하고 Enter 키를 누릅니다.

### <a name="add-files"></a>파일 추가

텍스트 파일을 추가하려면 **파일 추가를**선택합니다. 파일 탐색기가 열립니다.

텍스트 파일을 탐색하여 선택합니다. 여러 텍스트 파일을 언어 모델에 추가할 수 있습니다.

언어 모델의 오른쪽에 있는 타원 **(...**) 단추를 선택하고 **파일 추가를**선택하여 텍스트 파일을 추가할 수도 있습니다.

### <a name="delete-files"></a>파일 삭제

언어 모델에서 파일을 삭제하려면 텍스트 파일의 오른쪽에 있는 타원 **(...**) 버튼을 선택하고 **삭제를**선택합니다. 삭제를 취소할 수 없다는 새 창이 나타납니다. 새 창에서 **삭제** 옵션을 선택합니다.

이 작업은 언어 모델에서 파일을 완전히 제거합니다.

## <a name="delete-a-language-model"></a>언어 모델 삭제

계정에서 언어 모델을 삭제하려면 언어 모델의 오른쪽에 있는 타원 **(...**) 버튼을 선택하고 **삭제를**선택합니다.

삭제를 취소할 수 없다는 새 창이 나타납니다. 새 창에서 **삭제** 옵션을 선택합니다.

이 작업은 사용자 계정에서 언어 모델을 완전히 제거합니다. 삭제된 언어 모델을 사용하던 모든 동영상은 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 비디오 인덱서가 기본 모델을 사용하여 비디오를 다시 인덱싱합니다.

## <a name="customize-language-models-by-correcting-transcripts"></a>성적 증명서를 수정하여 언어 모델 사용자 지정

Video Indexer는 사용자가 동영상의 기록을 수정한 실제 수정을 기반으로 언어 모델의 자동 사용자 지정을 지원합니다.

1. 성적 증명서를 수정하려면 계정 비디오에서 수정할 동영상을 엽니다. **타임라인** 탭을 선택합니다.

    ![언어 모델 타임라인 탭 사용자 지정—비디오 인덱서](./media/customize-language-model/timeline.png)

1. 연필 아이콘을 선택하여 전사의 성적증명서를 편집합니다.

    ![언어 모델 편집 전사 사용자 지정-비디오 인덱서](./media/customize-language-model/edits.png)

    비디오 인덱서 (Video Indexer)는 비디오의 전사에서 귀하가 수정 한 모든 줄을 캡처하고 "성적 증명서 편집에서"라는 텍스트 파일에 자동으로 추가합니다. 이러한 편집 내용은 이 비디오를 인덱싱하는 데 사용된 특정 언어 모델을 다시 학습하는 데 사용됩니다.
    
    이 비디오를 인덱싱할 때 언어 모델을 지정하지 않은 경우 이 비디오의 모든 편집 내용은 비디오의 검색된 언어 내에서 "계정 적응"이라는 기본 언어 모델에 저장됩니다.
    
    동일한 줄에 대해 여러 번 편집한 경우 언어 모델을 업데이트하는 데 수정된 줄의 마지막 버전만 사용됩니다.  
    
    > [!NOTE]
    > 사용자 지정에는 텍스트 보정만 사용됩니다. 실제 단어를 포함하지 않는 수정 사항(예: 문장 부호 또는 공백)은 포함되지 않습니다.
    
1. 콘텐츠 모델 사용자 지정 페이지의 언어 탭에 성적 증명서 수정이 표시됩니다.

    ![언어 모델 사용자 지정-비디오 인덱서](./media/customize-language-model/customize.png)

   각 언어 모델에 대한 "성적 증명서 편집에서" 파일을 보려면 파일을 선택하여 엽니다.

    ![성적 증명서 편집에서-비디오 인덱서](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>다음 단계

[API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)
