---
title: 작성을 위해 Azure 리소스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Azure 작성 리소스 키로 마이그레이션합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194512"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure 리소스 작성 키로 마이그레이션

언어 이해(LUIS) 작성 인증이 전자 메일 계정에서 Azure 리소스로 변경되었습니다. 현재 는 필요하지 않지만 Azure 리소스로 전환하는 것은 나중에 적용됩니다.

## <a name="why-migrate"></a>마이그레이션해야 하는 이유는 무엇인가요?

작성에 Azure 리소스를 사용하면 리소스 의 소유자로서 작성에 대한 액세스를 제어할 수 있습니다. 작성 리소스를 만들고 이름을 지정하여 다양한 작성자 그룹을 관리할 수 있습니다.

예를 들어 2개의 LUIS 앱의 소유자이며 각 앱에서 공동 작업자인 다른 구성원이 있습니다. 서로 다른 두 개의 작성 리소스를 만들고 각 개별 리소스에 각 앱을 할당할 수 있습니다. 그런 다음 공동 작업하는 앱에 따라 각 멤버를 적절한 작성 리소스에 기여자로 할당합니다. Azure 작성 리소스는 권한 부여를 제어합니다.

> [!Note]
> 마이그레이션하기 전에 공동 작성자는 LUIS 앱 수준에서 _공동 작업자로_ 알려져 있습니다. 마이그레이션 후 _기여자의_ Azure 역할은 동일한 기능에 사용되지만 Azure 리소스 수준에서 사용됩니다.

## <a name="what-is-migrating"></a>마이그레이션이란 무엇입니까?

마이그레이션에는 다음이 포함됩니다.

* LUIS, 소유자 및 기여자의 모든 사용자입니다.
* **모든** 앱.
* **단방향** 마이그레이션.

소유자는 마이그레이션할 앱의 하위 집합을 선택할 수 없으며 프로세스를 되돌릴 수 없습니다.

마이그레이션은 다음과 다 되지 않습니다.

* 공동 작업자를 수집하고 Azure 작성 리소스에 자동으로 이동하거나 추가하는 프로세스입니다. 앱 소유자는 이 단계를 완료해야 합니다. 이 단계에는 적절한 리소스에 대한 사용 권한이 필요합니다.
* 예측 런타임 리소스를 만들고 할당하는 프로세스입니다. 예측 런타임 리소스가 필요한 경우 [별도의 프로세스이며](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 변경되지 않습니다.

## <a name="how-are-the-apps-migrating"></a>앱은 어떻게 마이그레이션되나요?

[LUIS 포털은](https://www.luis.ai) 마이그레이션 프로세스를 제공합니다.

다음과 같은 경우 마이그레이션하라는 메시지가 표시됩니다.

* 작성을 위한 전자 메일 인증 시스템에 앱이 있습니다.
* 그리고 당신은 응용 프로그램 소유자입니다.

창 밖으로 취소하여 마이그레이션 프로세스를 지연할 수 있습니다. 마이그레이션하거나 마이그레이션 기한이 경과할 때까지 주기적으로 마이그레이션하라는 메시지가 표시됩니다. 상단 탐색 모음의 잠금 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다.

## <a name="migration-for-the-app-owner"></a>앱 소유자에 대한 마이그레이션

### <a name="before-you-migrate"></a>마이그레이션하기 전에

* **필수**. [Azure subscription](https://azure.microsoft.com/free/) 구독 프로세스의 일부에는 청구 정보가 필요합니다. 그러나 LUIS를 사용할 때는 무료 ()`F0`가격 책정 계층을 사용할 수 있습니다.
* **선택적으로**는 각 앱을 내보내거나 내보내기 [API를](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)사용하여 LUIS 포털의 앱 목록에서 앱을 백업합니다.
* **선택적으로**각 앱의 공동 작업자 목록을 저장합니다. 이 전자 메일 목록은 마이그레이션 프로세스의 일부로 제공됩니다.


**LUIS 앱을 작성하는 것은** `F0` 계층별로 표시되는 무료입니다. [가격 책정 계층에 대해 자세히](luis-boundaries.md#key-limits)알아보십시오.

Azure 구독이 없는 경우 [에 등록합니다.](https://azure.microsoft.com/free/)

### <a name="migration-steps"></a>마이그레이션 단계

[다음 마이그레이션 단계를](luis-migration-authoring-steps.md)따릅니다.

### <a name="after-you-migrate"></a>마이그레이션 후

마이그레이션 프로세스 후 모든 LUIS 앱이 단일 LUIS 작성 리소스에 할당됩니다.

더 많은 작성 리소스를 만들고 _LUIS 포털의_ **-> Azure 리소스 관리** 페이지에서 할당할 수 있습니다.

해당 리소스에 대한 **IAM(액세스 제어)** 페이지에서 _Azure Portal의_작성 리소스에 기여자도 추가할 수 있습니다. 자세한 내용은 [기여자 액세스 추가를](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)참조하십시오.

|포털|목적|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* 예측 및 작성 리소스를 만듭니다.<br>* 기여자를 할당합니다.|
|[루이스](https://www.luis.ai)|* 새 작성 리소스로 마이그레이션합니다.<br>* **관리 -> Azure 리소스** 페이지에서 앱에 예측 및 사용자 지정 리소스를 할당 하거나 할당 취소합니다.|

## <a name="migration-for-the-app-contributor"></a>앱 기여자에 대한 마이그레이션

LUIS의 모든 사용자는 공동 작업자/기여자를 포함하여 마이그레이션해야 합니다. 공동 작업자는 앱에 액세스할 수 있도록 마이그레이션해야 합니다.

> [!Note]
> LUIS 앱의 소유자가 Azure 리소스에서 공동 작업자를 마이그레이션하고 추가한 경우 공동 작업자는 마이그레이션하지 않는 한 앱에 계속 액세스할 수 없습니다.

### <a name="before-the-app-is-migrated"></a>앱을 마이그레이션하기 전에

공동 작업자인 앱을 내보낸 다음 앱을 LUIS로 다시 가져올 수 있습니다. 가져오기 프로세스는 소유자가 되는 새 앱 ID를 가진 새 앱을 만듭니다.

### <a name="after-the-app-is-migrated"></a>앱 마이그레이션 후

앱 소유자는 [공동 작업자로 Azure 작성 리소스에 전자 메일을 추가해야](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource)합니다.

마이그레이션 프로세스 후 소유한 모든 앱은 LUIS 포털의 **내 앱** 페이지에서 사용할 수 있습니다.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>LUIS 작성을 위한 마이그레이션 프로세스 문제 해결

* LUIS 작성 키는 마이그레이션 프로세스가 완료된 후에만 LUIS 포털에 표시됩니다. LUIS CLI와 같은 작성 키를 만드는 경우에도 사용자는 LUIS 포털에서 마이그레이션 프로세스를 완료해야 합니다.
* 마이그레이션된 사용자가 Azure 리소스에 마이그레이션되지 않은 사용자를 기여자로 추가하는 경우 마이그레이션되지 않은 사용자는 마이그레이션하지 않는 한 앱에 액세스할 수 없습니다.
* 마이그레이션되지 않은 사용자가 앱의 소유자가 아니지만 다른 사용자가 소유한 다른 앱의 공동 작업자이고 소유자가 마이그레이션 프로세스를 거친 경우 이 사용자는 앱에 액세스하도록 마이그레이션해야 합니다.
* 마이그레이션되지 않은 사용자가 다른 마이그레이션된 사용자를 앱에 공동 작업자로 추가한 경우 마이그레이션된 사용자를 앱에 공동 작업자로 추가할 수 없는 오류가 발생합니다. 그런 다음 마이그레이션되지 않은 사용자는 마이그레이션 프로세스를 거치고 Azure 리소스를 만들고 마이그레이션된 사용자를 해당 리소스에 기여자로 추가해야 합니다.

다음과 같은 경우 마이그레이션 프로세스 중에 오류가 발생합니다.
* 구독에서 코그너티브 서비스 리소스를 만들 수 있는 권한을 부여하지 않습니다.
* 마이그레이션은 모든 응용 프로그램 런타임에 부정적인 영향을 미칩니다. 마이그레이션할 때 모든 공동 작업자가 앱에서 제거되고 다른 앱에서 공동 작업자로 제거됩니다. 이 프로세스는 할당된 키도 제거됨을 의미합니다. 다른 앱에서 키를 할당한 경우 마이그레이션이 차단됩니다. 마이그레이션하기 전에 안전하게 할당한 키를 제거합니다. 할당한 키가 런타임에 사용되지 않는 경우 마이그레이션을 진행할 수 있도록 해당 키를 제거해야 합니다.

다음 URL 형식을 사용하여 앱의 Azure 리소스 목록에 액세스합니다.

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>다음 단계

* [앱을 작성 리소스로 마이그레이션하는 방법](luis-migration-authoring-steps.md)
