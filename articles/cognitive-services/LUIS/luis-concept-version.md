---
title: 버전 관리
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS의 버전은 기존의 프로그래밍 버전과 유사합니다. 각 버전은 앱의 시간에 대한 스냅샷입니다. 앱을 변경하기 전에 새 버전을 만듭니다. 정확한 앱으로 돌아가서 앱의 의도와 발언을 이전 상태로 복원하는 것이 더 쉽습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: 9da79e5b744f8ba70c0e265f0d1f0126b37eba49
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870655"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>LUIS 버전을 사용하는 방법 및 시기 이해

LUIS의 버전은 기존의 프로그래밍 버전과 유사합니다. 각 버전은 앱의 시간에 대한 스냅샷입니다. 앱을 변경하기 전에 새 버전을 만듭니다. 정확한 버전으로 돌아가서 의도와 발화를 제거해 이전 상태로 되돌려 보는 것이 더 쉽습니다.

[버전](luis-how-to-manage-versions.md)을 사용하여 동일한 앱의 여러 가지 모델을 만듭니다. 

## <a name="version-id"></a>버전 ID
버전 ID는 문자, 숫자 또는 ‘.’으로 구성되며 10자 이하여야 합니다.

## <a name="initial-version"></a>초기 버전
초기 버전(0.1)은 기본 활성 버전입니다. 

## <a name="active-version"></a>활성 버전
활성으로 [버전을 설정](luis-how-to-manage-versions.md#set-active-version)하는 것은 버전이 현재 [LUIS](luis-reference-regions.md) 웹 사이트에서 편집 및 테스트됨을 의미합니다. 버전을 활성으로 설정하여 해당 데이터에 액세스하고, 업데이트하고, 해당 버전을 테스트 및 게시합니다.

현재 활성 버전의 이름이 앱 이름 뒤의 왼쪽 위 패널에 표시됩니다. 

[![현재 버전 변경](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>버전 및 게시 슬롯
스테이지 및 제품 슬롯에 게시합니다. 각 슬롯에는 다른 버전 또는 동일한 버전이 있을 수 있습니다. 이는 엔드포인트를 통해 모델 버전 간의 변경 내용을 확인하는 데 유용하며, 봇 또는 기타 LUIS 호출 애플리케이션에 사용할 수 있습니다. 

## <a name="clone-a-version"></a>버전 복제
버전을 복제하여 기존 버전의 복사본을 만들고 새 버전으로 저장합니다. 버전을 복제하여 기존 버전의 동일한 콘텐츠를 새 버전의 시작점으로 사용합니다. 버전을 복제하면 새 버전이 **활성** 버전이 됩니다. 

## <a name="import-and-export-a-version"></a>버전 가져오기 및 내보내기
앱 수준에서 버전을 가져올 수 있습니다. 해당 버전은 활성 버전이 되고 앱 파일의 “versionId” 속성에 있는 버전 ID를 사용했습니다. 버전 수준에서 기존 앱으로 가져올 수도 있습니다. 새 버전이 활성 버전이 됩니다. 

앱 수준에서 버전을 내보내거나 버전 수준에서 버전을 내보낼 수 있습니다. 유일한 차이는 앱 수준에서 내보낸 버전이 현재 활성 버전이지만, 버전 수준에서는 **[설정](luis-how-to-manage-versions.md)** 페이지에서 내보낼 버전을 선택할 수 있다는 것입니다. 

앱을 가져온 후에 앱이 보존되므로 내보낸 파일에는 기계 학습 정보가 없습니다. 내보낸 파일에는 협력자가 없습니다. 버전을 새 앱으로 가져온 후 협력자를 다시 추가해야 합니다.

## <a name="export-each-version-as-app-backup"></a>앱 백업으로 각 버전 내보내기
LUIS 앱을 백업하려면 **[설정](luis-how-to-manage-versions.md)** 페이지에서 각 버전을 내보냅니다.

## <a name="delete-a-version"></a>버전 삭제
설정 페이지의 버전 목록에서 활성 버전을 제외한 모든 버전을 삭제할 수 있습니다. 

## <a name="version-availability-at-the-endpoint"></a>엔드포인트의 버전 가용성
학습된 버전은 앱 [엔드포인트](luis-glossary.md#endpoint)에서 자동으로 제공되지 않습니다. 앱 엔드포인트에서 사용할 수 있으려면 버전을 [게시](luis-how-to-publish-app.md)하거나 다시 게시해야 합니다. **스테이징** 및 **프로덕션**에 게시하면 엔드포인트에서 사용 가능한 최대 두 개의 앱 버전이 제공됩니다. 엔드포인트에서 사용할 수 있는 추가 앱 버전이 필요한 경우, 버전을 내보내고 새 앱으로 다시 가져와야 합니다. 새 앱에는 다른 앱 ID가 포함됩니다.

## <a name="collaborators"></a>협력자
소유자 및 모든 [협력자](luis-how-to-collaborate.md)가 모든 앱 버전의 전체 액세스 권한을 가집니다.

## <a name="next-steps"></a>다음 단계

앱 설정 페이지에서 [버전 관리](luis-how-to-manage-versions.md)를 추가하는 방법을 참조하세요. 

[의도](luis-concept-intent.md)를 모델로 디자인하는 방법에 대해 알아봅니다.
