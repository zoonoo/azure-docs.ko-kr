---
title: 작성을 위해 Azure 리소스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Azure 제작 리소스 키로 마이그레이션합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 86a7575c173b4515eaf0f690bcd00c6fb70ee581
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492726"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure 리소스 제작 키로 마이그레이션

LUIS () 제작 인증을 전자 메일 계정에서 Azure 리소스로 변경 했습니다. Language Understanding 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="why-migrate"></a>마이그레이션해야 하는 이유는 무엇인가요?

작성을 위해 Azure 리소스를 사용 하면 리소스 소유자가 작성에 대 한 액세스를 제어할 수 있습니다. 제작 리소스를 만들고 이름을 지정할 수 있습니다. 

예를 들어, 두 가지 유형의 LUIS 앱을 작성 하는 경우 서로 다른 멤버를 사용 하 여 두 개의 서로 다른 제작 리소스를 만들고 기여자를 할당할 수 있습니다. Azure 제작 리소스는 권한 부여를 제어 합니다. 

> [!Note]
> 마이그레이션하기 전에 공동 작성자를 _협력자_라고 합니다. 마이그레이션 후에는 _참가자_ 의 Azure 역할이 동일한 기능에 사용 됩니다.

## <a name="what-is-migrating"></a>마이그레이션 이란?

마이그레이션에는 다음이 포함 됩니다.

* LUIS, 소유자 및 참가자의 모든 사용자입니다.
* **모든** 앱.
* **단방향** 마이그레이션.

소유자는 마이그레이션할 앱의 하위 집합을 선택할 수 없으며 프로세스를 되돌릴 수 없습니다. 

마이그레이션은 다음과 같습니다. 

* 협력자를 수집 하 고 자동으로 이동 하거나 Azure 제작 리소스에 추가 하는 프로세스입니다. 앱 소유자는이 단계를 완료 해야 합니다. 이 단계를 수행 하려면 적절 한 리소스에 대 한 권한이 필요 합니다.
* 예측 런타임 리소스를 만들고 할당 하는 프로세스입니다. 예측 런타임 리소스를 필요로 하는 경우 [별도의 프로세스](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 이며 변경 되지 않습니다. 

## <a name="how-are-the-apps-migrating"></a>앱이 어떻게 마이그레이션 되나요?

[LUIS 포털](https://www.luis.ai) 은 마이그레이션 프로세스를 제공 합니다. 

다음의 경우 마이그레이션해야 한다는 메시지가 표시 됩니다.

* 제작을 위해 전자 메일 인증 시스템에 앱이 있습니다.
* 앱 소유자입니다. 

창을 취소 하 여 마이그레이션 프로세스를 연기할 수 있습니다. 마이그레이션하기 전 또는 마이그레이션 최종 기한에 도달할 때까지 마이그레이션을 정기적으로 요청 합니다. 위쪽 탐색 모음의 잠금 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다.

## <a name="migration-for-the-app-owner"></a>앱 소유자를 위한 마이그레이션

### <a name="before-you-migrate"></a>마이그레이션하기 전에

* **필요에 따라**각 앱을 내보내거나 내보내기 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)를 사용 하 여 LUIS 포털의 앱 목록에서 앱을 백업 합니다.
* **필요에 따라**각 앱의 collaborator's 목록을 저장 합니다. 이 전자 메일 목록은 마이그레이션 프로세스의 일부로 제공 됩니다.
* **필수**, [Azure 구독이](https://azure.microsoft.com/free/)있어야 합니다. 구독 프로세스의 일부에는 청구 정보가 필요 합니다. 그러나 LUIS를 사용 하는 경우 무료 (`F0`) 가격 책정 계층을 사용할 수 있습니다. 

**LUIS 앱 작성은 `F0` 계층으로 표시 되는 무료입니다**. [가격 책정 계층에](luis-boundaries.md#key-limits)대해 자세히 알아보세요.

Azure 구독이 없는 경우 [등록](https://azure.microsoft.com/free/)합니다. 

### <a name="migration-steps"></a>마이그레이션 단계

[이러한 마이그레이션 단계](luis-migration-authoring-steps.md)를 따릅니다.

### <a name="after-you-migrate"></a>마이그레이션 후 

마이그레이션 프로세스가 완료 되 면 이제 모든 LUIS apps가 단일 LUIS authoring resource에 할당 됩니다.

_LUIS 포털_의 **관리-> Azure 리소스** 페이지에서 더 많은 제작 리소스를 만들고 할당할 수 있습니다. 

_Azure Portal_, 해당 리소스에 대 한 **Access Control (IAM)** 페이지에서 제작 리소스에 참가자를 추가할 수 있습니다. 자세한 내용은 [참가자 액세스 추가](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) 를 참조 하세요.

|포털|목적|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 예측 및 제작 리소스를 만듭니다.<br>* 참가자를 할당 합니다.|
|[LUIS](https://www.luis.ai)|* 새 제작 리소스로 마이그레이션<br>* **관리-> Azure 리소스** 페이지에서 예측을 할당 하거나 할당을 취소 하 고 앱에 리소스를 제작 합니다.| 

## <a name="migration-for-the-app-contributor"></a>앱 참가자에 대 한 마이그레이션

LUIS의 모든 사용자는 협력자/contributors를 포함 하 여 마이그레이션해야 합니다. 

### <a name="before-the-app-is-migrated"></a>앱을 마이그레이션하기 전에

협력자 인 앱을 내보낸 다음 앱을 다시 LUIS으로 가져올 수 있습니다. 가져오기 프로세스에서는 사용자가 소유자 인 새 앱 ID를 사용 하 여 새 앱을 만듭니다.

### <a name="after-the-app-is-migrated"></a>앱이 마이그레이션된 후

앱 소유자는 [Azure authoring resource에 협력자로 전자 메일을 추가](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)해야 합니다. 

마이그레이션 프로세스 후 소유 하는 모든 앱은 LUIS 포털의 **내 앱** 페이지에서 사용할 수 있습니다.  

## <a name="troubleshooting"></a>문제 해결

LUIS 제작 키는 마이그레이션 프로세스가 완료 된 후에만 LUIS 포털에 표시 됩니다. LUIS CLI를 사용 하는 등 제작 키를 만드는 경우에도 사용자는 마이그레이션 프로세스를 완료 해야 합니다. 

## <a name="next-steps"></a>다음 단계

* [앱을 제작 리소스로 마이그레이션하는 방법](luis-migration-authoring-steps.md)