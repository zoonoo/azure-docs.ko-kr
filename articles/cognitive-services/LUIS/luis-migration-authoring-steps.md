---
title: Azure 작성 리소스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Azure 작성 리소스로 마이그레이션합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194644"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Azure 작성 리소스로 마이그레이션하는 단계

LUIS(언어 이해) 포털에서 소유한 모든 앱을 마이그레이션하여 Azure 작성 리소스를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* **선택적으로**는 각 앱을 내보내거나 내보내기 [API를](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)사용하여 LUIS 포털의 앱 목록에서 앱을 백업합니다.
* **선택적으로**각 앱의 공동 작업자 목록을 저장합니다. 모든 공동 작업자는 마이그레이션 프로세스의 일부로 전자 메일을 보낼 수 있습니다.
* **필수**. [Azure subscription](https://azure.microsoft.com/free/) 구독 프로세스의 일부에는 청구 정보가 필요합니다. 그러나 LUIS를 사용할 때 무료(F0) 가격 책정 계층을 사용할 수 있습니다. 사용량이 증가함에 따라 유료 계층이 필요할 수 있습니다.

Azure 구독이 없는 경우 [에 등록합니다.](https://azure.microsoft.com/free/)

## <a name="access-the-migration-process"></a>마이그레이션 프로세스에 액세스

매주 앱을 마이그레이션하라는 메시지가 표시됩니다. 마이그레이션하지 않고 이 창을 취소할 수 있습니다. 다음 예약된 기간 전에 마이그레이션하려는 경우 LUIS 포털의 맨 위 도구 모음에 있는 **Azure** 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![마이그레이션 아이콘](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>앱 소유자가 마이그레이션 프로세스를 시작합니다.

LUIS 앱의 소유자인 경우 마이그레이션 프로세스를 사용할 수 있습니다.

1. [LUIS 포털에](https://www.luis.ai) 로그인하고 이용 약관에 동의합니다.
1. 마이그레이션 팝업 창을 사용하면 마이그레이션을 계속하거나 나중에 마이그레이션할 수 있습니다. **지금 마이그레이션을 선택합니다.** 나중에 마이그레이션하도록 선택한 경우 Azure의 새 작성 키로 마이그레이션하는 데 9개월이 있습니다.

    ![마이그레이션 프로세스의 첫 번째 팝업 창을 선택합니다.](./media/migrate-authoring-key/migrate-now.png)

1. 선택적으로 앱 중 공동 작업자가 있는 경우 마이그레이션에 대해 알리는 **전자 메일을 보내라는** 메시지가 표시됩니다. 선택적 단계입니다.

    계정을 Azure로 마이그레이션하면 공동 작업자가 앱을 더 이상 사용할 수 없습니다.

    각 공동 작업자 및 앱에 대해 기본 전자 메일 응용 프로그램이 가볍게 형식이 지정된 전자 메일로 열립니다. 이메일을 보내기 전에 편집할 수 있습니다.

    이메일 템플릿에는 정확한 앱 ID와 앱 이름이 포함되어 있습니다.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. 기존 작성 리소스를 사용하거나 새 작성 리소스를 만들도록 선택하여 LUIS 작성 리소스를 만들도록 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![제작 리소스 만들기](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. 다음 창에서 리소스 키 정보를 입력합니다. 정보를 입력한 후 **리소스 만들기를**선택합니다. 구독당 지역당 10개의 무료 작성 리소스를 가질 수 있습니다.

    ![제작 리소스 만들기](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **새 제작 리소스를 만들** 때 다음 정보를 제공합니다.

    * **리소스 이름** - 사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 엔드포인트 쿼리의 URL 중 일부로 사용됩니다.
    * **테넌트** - Azure 구독이 연결된 테넌트입니다.
    * **구독 이름** - 리소스에 대해 청구되는 구독입니다.
    * **리소스 그룹** - 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.
    * **위치** - 선택한 위치는 **리소스 그룹** 선택에 따라 결정됩니다.
    * **가격 책정 계층** - 가격 책정 계층은 초 및 월별 최대 트랜잭션 수를 결정합니다.

1. 작성 리소스의 유효성을 검사하고 **지금 마이그레이션합니다.**

    ![제작 리소스 만들기](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. 작성 리소스가 만들어지면 성공 메시지가 표시됩니다. **닫기를** 선택하여 팝업 창을 닫습니다.

    ![작성 리소스가 성공적으로 만들어졌습니다.](./media/migrate-authoring-key/migration-success.png)

    **내 앱** 목록에는 새 작성 리소스로 마이그레이션된 앱이 표시됩니다.

    LUIS 포털에서 앱을 계속 편집하려면 작성 리소스의 키를 알 필요가 없습니다. 프로그래밍 방식으로 앱을 편집하려는 경우 작성 키 값이 필요합니다. 이러한 값은 LUIS 포털의 **> Azure 리소스 관리** 페이지에 표시되며 리소스의 **키** 페이지의 Azure Portal에서도 사용할 수 있습니다.

1. 앱에 액세스하기 전에 구독 및 LUIS 작성 리소스를 선택하여 작성할 수 있는 앱을 확인합니다.

    ![구독 및 LUIS 작성 리소스를 선택하여 사용자가 작성할 수 있는 앱을 확인합니다.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>앱 기여자가 마이그레이션 프로세스를 시작합니다.

마이그레이션을 위해 앱 소유자와 동일한 단계를 따릅니다. 이 프로세스는 종류의 `LUIS.Authoring`새 작성 리소스를 만듭니다.

다른 사용자가 소유한 마이그레이션된 앱에 기여자로 추가하려면 계정을 마이그레이션해야 합니다.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>마이그레이션 프로세스 후 작성 리소스에 기여자를 추가합니다.

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[기여자를 추가하는 방법에](luis-how-to-collaborate.md)대해 알아봅니다.

## <a name="troubleshooting-errors-with-the-migration-process"></a>마이그레이션 프로세스로 오류 문제 해결

마이그레이션 프로세스 `MissingSubscriptionRegistration` 중에 빨간색 알림 표시줄이 있는 LUIS 포털에 오류가 발생하면 [Azure 포털](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 또는 [Azure CLI에서](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)인지 서비스 리소스를 만듭니다. [이 오류의 원인에](../../azure-resource-manager/templates/error-register-resource-provider.md#cause)대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계


* 작성 및 런타임 키에 대한 [개념](luis-concept-keys.md) 검토
* [키 할당 및](luis-how-to-azure-subscription.md) [기여자](luis-how-to-collaborate.md) 추가 방법 검토
