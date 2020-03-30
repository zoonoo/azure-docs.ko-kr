---
title: Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정
titleSuffix: Azure Media Services
description: 비디오 인덱서 웹 사이트를 사용하여 브랜드 모델을 사용자 지정하는 방법을 알아봅니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128053"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠에 언급되어 있거나 비디오의 시각적 텍스트에 표시되는 경우 비디오 인덱서가 콘텐츠의 브랜드로 검색합니다.

사용자 지정 브랜드 모델을 사용하면 다음을 수행할 수 있습니다.

- Bing 브랜드 데이터베이스에서 브랜드를 검색하도록 비디오 인덱서를 선택합니다.
- 비디오 인덱서가 특정 브랜드가 감지되지 않도록 제외할 지 선택합니다(기본적으로 브랜드 거부 목록 만들기).
- 비디오 인덱서가 Bing의 브랜드 데이터베이스에 없을 수도 있는 모델의 일부가 되어야 하는 브랜드를 포함하도록 하려면 선택합니다(기본적으로 브랜드 수락 목록 만들기).

자세한 개요는 이 [개요를](customize-brands-model-overview.md)참조하십시오.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="edit-brands-model-settings"></a>브랜드 모델 설정 편집

Bing 브랜드 데이터베이스의 브랜드를 검색할지를 여부를 설정하는 옵션이 제공됩니다. 이 옵션을 설정하려면 브랜드 모델의 설정을 편집해야 합니다. 다음 단계를 수행하세요.

1. [비디오 인덱서](https://www.videoindexer.ai/) 웹 사이트로 이동하여 로그인합니다.
2. 계정에서 모델을 사용자 지정하려면 페이지의 오른쪽 상단 모서리에 있는 **콘텐츠 모델 사용자 지정** 단추를 선택합니다.

   ![비디오 인덱서에서 콘텐츠 모델 사용자 지정](./media/content-model-customization/content-model-customization.png)

3. 브랜드를 편집하려면 **브랜드** 탭을 선택합니다.

    ![비디오 인덱서에서 브랜드 모델 사용자 지정](./media/customize-brand-model/customize-brand-model.png)

4. **Bing에서 제안한 브랜드 표시** 옵션을 확인하여 Bing에서 제안한 브랜드를 검색하려면 Bing에서 제안한 브랜드를 선택하지 않은 상태로 두십시오.

## <a name="include-brands-in-the-model"></a>모델에 브랜드 포함

**브랜드 포함** 섹션은 Bing에서 제안하지 않더라도 비디오 인덱서가 감지할 수 있는 맞춤 브랜드를 나타냅니다.  

### <a name="add-a-brand-to-include-list"></a>목록을 포함하도록 브랜드 추가

1. **+ 브랜드 추가를 선택합니다.**

    ![비디오 인덱서에서 브랜드 모델 사용자 지정](./media/customize-brand-model/add-brand.png)

    이름(필수), 범주(선택), 설명(선택) 및 참조 URL(선택)을 제공합니다.
    범주 필드는 브랜드에 태그를 지정하는 데 도움을 주기 위한 것입니다. 이 필드는 Video Indexer API를 사용할 때 브랜드의 *태그*로 표시됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

    참조 URL 필드는 브랜드에 대한 모든 참조 웹 사이트일 수 있습니다(예: 위키백과 페이지의 링크).

2. **브랜드 추가를** 선택하면 브랜드가 **브랜드 포함** 목록에 추가되었음을 확인할 수 있습니다.

### <a name="edit-a-brand-on-the-include-list"></a>포함 목록에서 브랜드 편집

1. 편집할 브랜드 옆에 있는 연필 아이콘을 선택합니다.

    브랜드의 범주, 설명 또는 참조 URL을 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.

2. **업데이트** 단추를 선택하여 새 정보로 브랜드를 업데이트합니다.

### <a name="delete-a-brand-on-the-include-list"></a>포함 목록에서 브랜드 삭제

1. 삭제하려는 브랜드 옆에 있는 휴지통 아이콘을 선택합니다.
2. **삭제를** 선택하면 *브랜드가* 더 이상 브랜드 포함 목록에 표시되지 않습니다.

## <a name="exclude-brands-from-the-model"></a>모델에서 브랜드 제외

**브랜드 제외** 섹션은 동영상 인덱서가 감지하지 않으려는 브랜드를 나타냅니다.

### <a name="add-a-brand-to-exclude-list"></a>브랜드 추가하여 제외 목록

1. **+ 브랜드 추가를 선택합니다.**

    이름(필수), 범주(선택)를 제공합니다.

2. **브랜드 추가를** 선택하면 브랜드 *제외* 목록에 브랜드가 추가된 것을 확인할 수 있습니다.

### <a name="edit-a-brand-on-the-exclude-list"></a>제외 목록에서 브랜드 편집

1. 편집할 브랜드 옆에 있는 연필 아이콘을 선택합니다.

    브랜드의 범주만 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.

2. **업데이트** 단추를 선택하여 새 정보로 브랜드를 업데이트합니다.

### <a name="delete-a-brand-on-the-exclude-list"></a>제외 목록에서 브랜드 삭제

1. 삭제하려는 브랜드 옆에 있는 휴지통 아이콘을 선택합니다.
2. **삭제를** 선택하면 브랜드 *제외* 목록에 브랜드가 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)
