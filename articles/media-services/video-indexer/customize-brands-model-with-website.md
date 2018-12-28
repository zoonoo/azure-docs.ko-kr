---
title: Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 웹 사이트를 사용하여 브랜드 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283619"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 음성 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 감지 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어, Microsoft가 비디오 또는 오디오 콘텐츠에 멘션되거나, 비디오의 시각적 텍스트에 표시되는 경우 Video Indexer는 콘텐츠에서 해당 항목을 브랜드로 감지합니다. 사용자 지정 브랜드 모델을 사용하여 Video Indexer가 Bing 브랜드 데이터베이스에서 브랜드를 검색할지, 특정 브랜드를 검색되지 않도록 제외할지(브랜드의 차단 목록 생성), Bing의 브랜드 데이터베이스에 없을 수 있는 모델 부분에 해당하는 브랜드를 포함할지(브랜드의 허용 목록 생성)를 선택할 수 있습니다.

자세한 개요를 보려면 [개요](customize-brands-model-overview.md)를 참조하세요.

Video Indexer 웹 사이트를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.

## <a name="edit-the-settings-of-the-brands-model"></a>브랜드 모델의 설정 편집  

Bing 브랜드 데이터베이스의 브랜드를 검색할지를 여부를 설정하는 옵션이 제공됩니다. 이 경우 브랜드 모델의 설정을 편집해야 합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 계정의 모델을 사용자 지정하려면 페이지 오른쪽 위 모서리에 있는 **콘텐츠 모델 사용자 지정** 단추를 클릭합니다.
 
   ![콘텐츠 모델 사용자 지정](./media/content-model-customization/content-model-customization.png) 
3. 브랜드를 편집하려면 **브랜드** 탭을 선택합니다.

    ![브랜드 모델 사용자 지정](./media/customize-brand-model/customize-brand-model.png)
4. Video Indexer가 Bing에서 제안한 브랜드를 포함하도록 하려면 **Show brands suggested by Bing**(Bing에서 제안한 브랜드 표시) 옵션을 선택합니다. Video Indexer가 Bing이 제안한 브랜드를 콘텐츠에서 검색하지 않도록 하려면 이 옵션을 선택 취소된 상태로 둡니다. 

## <a name="include-brands-in-the-model"></a>모델에 브랜드 포함

**브랜드 포함** 섹션은 Bing에서 제안하지 않더라도 Video Indexer서 검색하도록 하려는 사용자 지정 브랜드를 나타냅니다.  

### <a name="add-a-brand"></a>브랜드 추가

1. "+ 브랜드 추가"를 클릭합니다.

    ![브랜드 모델 사용자 지정](./media/customize-brand-model/add-brand.png)

    이름(필수), 범주(선택), 설명(선택) 및 참조 URL(선택)을 제공합니다.
    범주 필드는 브랜드에 태그를 지정하는 데 도움을 주기 위한 것입니다. 이 필드는 Video Indexer API를 사용할 때 브랜드의 *태그*로 표시됩니다. 예를 들어, "Azure" 브랜드는 "클라우드"로 태그를 지정하거나 분류할 수 있습니다.

    참조 URL 필드는 해당 Wikipedia 페이지의 링크처럼, 브랜드의 참조 웹 사이트일 수 있습니다.
2. "브랜드 추가"를 클릭하면 **브랜드 포함** 목록에 해당 브랜드가 추가된 것을 볼 수 있습니다.

### <a name="edit-a-brand"></a>브랜드 편집

1. 편집하려는 브랜드 옆에 있는 연필 아이콘을 클릭합니다.

    브랜드의 범주, 설명 또는 참조 URL을 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.
2. **업데이트** 단추를 클릭하여 브랜드를 새 정보로 업데이트합니다.

### <a name="delete-a-brand"></a>브랜드 삭제

1. 삭제하려는 브랜드 옆에 있는 휴지통 아이콘을 클릭합니다.
2. "삭제"를 클릭하면 해당 브랜드가 *브랜드 포함* 목록에 더 이상 나타나지 않습니다.

## <a name="exclude-brands-from-the-model"></a>모델에서 브랜드 제외

**브랜드 제외** 섹션에는 Video Indexer가 검색하지 않도록 하려는 브랜드가 표시됩니다.

### <a name="add-a-brand"></a>브랜드 추가

1. "+ 브랜드 추가"를 클릭합니다.

    이름(필수), 범주(선택)를 제공합니다.
2. "브랜드 추가"를 클릭하면 *브랜드 제외* 목록에 해당 브랜드가 추가된 것을 볼 수 있습니다.

### <a name="edit-a-brand"></a>브랜드 편집

1. 편집하려는 브랜드 옆에 있는 연필 아이콘을 클릭합니다.

    브랜드의 범주만 업데이트할 수 있습니다. 브랜드 이름은 고유하므로 변경할 수 없습니다. 브랜드 이름을 변경해야 할 경우 전체 브랜드를 삭제하고(다음 섹션 참조) 새 이름으로 새 브랜드를 만듭니다.
2. **업데이트** 단추를 클릭하여 브랜드를 새 정보로 업데이트합니다.

### <a name="delete-a-brand"></a>브랜드 삭제

1. 삭제하려는 브랜드 옆에 있는 휴지통 아이콘을 클릭합니다.
2. "삭제"를 클릭하면 해당 브랜드가 *브랜드 제외* 목록에 더 이상 나타나지 않습니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)
