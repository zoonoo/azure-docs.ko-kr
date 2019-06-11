---
title: 협업
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 앱에는 단일 앱을 여러 사람이 작성하도록 허용하는 단일 소유자 및 선택적 협력자가 필요합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 294905ccfd0ce8db6da8737277b0ce978ba837ea
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473520"
---
# <a name="collaborating-with-other-authors"></a>다른 작성자와 공동 작업

LUIS 앱에는 단일 앱을 여러 사람이 작성하도록 허용하는 단일 소유자 및 선택적 협력자가 필요합니다.

## <a name="luis-account"></a>LUIS 계정
LUIS 계정은 단일 [Microsoft Live](https://login.live.com/) 계정과 연결됩니다. 각 LUIS 계정에는 계정이 액세스할 수 있는 모든 LUIS 앱을 작성하는 데 사용할 체험 [작성 키](luis-concept-keys.md#authoring-key)가 제공됩니다. 

LUIS 계정에는 여러 LUIS 앱이 포함될 수 있습니다.

Active Directory 사용자 계정에 자세히 알아보려면 [Azure Active Directory 테넌트 사용자](luis-how-to-collaborate.md#azure-active-directory-tenant-user)를 참조하세요. 

## <a name="luis-app-owner"></a>LUIS 앱 소유자

앱을 생성 하는 계정 소유자 이며 각 앱에 단일 소유자입니다. 소유자는 앱에 표시 됩니다 **[설정](luis-how-to-collaborate.md)** 페이지입니다. 소유자 끝점 할당량에는 월별 한도의 75%에 도달 하면 전자 메일을 받습니다. 

## <a name="authorization-roles"></a>권한 부여 역할
LUIS는 소유자와 공동 작업자에게 여러 역할을 지원하지 않습니다. 단, 한 가지 예외는 있습니다. 소유자는 앱을 삭제할 수 있는 유일한 계정입니다.

모델에 대한 액세스를 제어하려면 모델을 더 작은 LUIS 앱으로 분할하여 협력자 집합을 더 제한하는 것이 좋습니다. [Dispatch](https://aka.ms/dispatch-tool)를 사용하여 부모 LUIS 앱이 부모 앱과 자식 앱 간의 조정을 관리하도록 허용합니다.

## <a name="transfer-ownership"></a>소유권 이전
LUIS는 소유권 이전을 제공하지 않지만, 모든 협력자는 앱을 내보낸 다음, 가져와서 앱을 만들 수 있습니다. 새 앱에는 다른 앱 ID가 포함됩니다. 새 앱을 학습시키고 게시하고, 새 엔드포인트를 사용해야 합니다.

## <a name="luis-app-collaborators"></a>LUIS 앱 협력자
앱 소유자는 앱에 협력자를 추가할 수 있습니다. 소유자가 앱 **[설정](luis-how-to-collaborate.md)** 에서 협력자의 메일 주소를 추가해야 합니다. 협력자는 앱에 대한 전체 액세스 권한을 가집니다. 협력자가 앱을 삭제하면 앱이 협력자의 계정에서 제거되지만 소유자의 계정에 남아 있습니다. 

협력자와 여러 앱을 공유하려면 각 앱에 협력자의 메일이 추가되어야 합니다. 

## <a name="managing-multiple-authors"></a>여러 작성자 관리
[LUIS](luis-reference-regions.md#luis-website) 웹 사이트는 현재 트랜잭션 수준 작성을 제공하지 않습니다. 작성자가 기본 버전에서 독립 버전 작업을 수행하도록 허용할 수 있습니다. 다음 섹션에서 두 가지 방법을 설명합니다.

## <a name="manage-multiple-versions-inside-the-same-app"></a>동일한 앱 내부에서 여러 버전 관리
먼저 각 작성자에 기본 버전에서 [복제](luis-how-to-manage-versions.md#clone-a-version)합니다. 

각 작성자는 앱의 고유한 버전으로 변경 합니다. 각 작성자가 모델에 만족하면 새 버전을 JSON 파일로 내보냅니다.  

내보낸 앱은 JSON 형식 파일이며 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 JSON 파일을 만듭니다. JSON에서 **versionId** 속성을 변경하여 새 병합 버전을 표시합니다. 해당 버전을 원래 앱으로 가져옵니다. 

이 방법을 사용하면 하나의 활성 버전, 하나의 스테이지 버전 및 하나의 게시된 버전을 사용할 수 있습니다. 세 가지 버전에서 대화형 테스트 창의 결과를 비교할 수 있습니다.

## <a name="manage-multiple-versions-as-apps"></a>여러 버전을 앱으로 관리
기본 버전을 [내보냅니다](luis-how-to-manage-versions.md#export-version). 각 작성자는 버전을 가져옵니다. 앱을 가져오는 사용자는 버전의 소유자입니다. 앱 수정을 완료하면 버전을 내보냅니다. 

내보낸 앱은 JSON 형식 파일이며 기본 내보내기와 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 JSON 파일을 만듭니다. JSON에서 **versionId** 속성을 변경하여 새 병합 버전을 표시합니다. 해당 버전을 원래 앱으로 가져옵니다.

## <a name="collaborator-roles-vs-entity-roles"></a>공동 작업자 역할 및 엔터티 역할

[엔터티 역할](luis-concept-roles.md) LUIS 앱의 데이터 모델에 적용 합니다. 제작 액세스 수준을 협력자 역할 적용 됩니다. 

## <a name="next-steps"></a>다음 단계

[버전 관리](luis-concept-version.md) 개념을 이해합니다. 

LUIS 앱에서 협력자를 관리하는 방법을 알아보려면 [앱 설정](luis-how-to-collaborate.md)을 참조하세요.

작성 API와 함께 [Add email to access list](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58fcccdd5aca2f08a4104342)(메일을 추가하여 목록에 액세스)를 참조하세요.
