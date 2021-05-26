---
title: Azure 리소스 작성 키로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 이메일 계정에서 Azure 리소스로 LUIS(Language Understanding) 작성 인증을 마이그레이션하는 방법을 설명합니다.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 54c0d8aa8ba17beb5a2f5a51c4a19795f767733a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110089438"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure 리소스 작성 키로 마이그레이션

> [!IMPORTANT]
>  12월 3일부터 기존 LUIS 사용자는 LUIS 애플리케이션을 계속 작성하기 위해 마이그레이션 프로세스를 완료해야 합니다.

LUIS(Language Understanding) 작성 인증이 이메일 계정에서 Azure 리소스로 변경되었습니다. 아직 마이그레이션하지 않은 경우 이 문서를 사용하여 계정을 마이그레이션하는 방법을 알아보세요.  


## <a name="what-is-migration"></a>마이그레이션이란?

마이그레이션은 이메일 계정에서 Azure 리소스로 작성 인증을 변경하는 프로세스입니다. 마이그레이션한 후 계정이 Azure 구독 및 Azure 제작 리소스에 연결됩니다.

마이그레이션은 [LUIS 포털](https://www.luis.ai)에서 수행해야 합니다. 예를 들어 LUIS CLI를 사용하여 작성 키를 만드는 경우 LUIS 포털에서 마이그레이션 프로세스를 완료해야 합니다. 마이그레이션 후에도 애플리케이션에 공동 작성자가 있을 수 있지만 이들은 애플리케이션 수준 대신 Azure 리소스 수준에 추가됩니다. 계정 마이그레이션은 취소할 수 없습니다.

> [!Note]
> * 예측 런타임 리소스를 만들어야 하는 경우에는 [별도의 프로세스](luis-how-to-azure-subscription.md#create-luis-resources)를 수행해야 합니다.
> * 애플리케이션 및 기여자가 어떻게 영향을 받는지에 대한 자세한 내용은 아래의 [마이그레이션 참고 사항](#migration-notes) 섹션을 참조하세요. 
> * F0 계층에 표시된 대로 LUIS 앱 작성은 무료입니다. [가격 책정 계층에 대한 자세한 정보](luis-limits.md#key-limits)를 알아봅니다.

## <a name="migration-prerequisites"></a>마이그레이션 필수 조건

* 유효한 Azure 구독. 테넌트 관리자에게 구독에 대한 추가를 요청하거나 [무료로 등록](https://azure.microsoft.com/free/cognitive-services)할 수 있습니다.
* LUIS 포털 또는 [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)의 LUIS Azure 제작 리소스. 
    * LUIS 포털에서 제작 리소스를 만드는 작업은 다음 섹션에서 설명하는 마이그레이션 프로세스의 일부입니다.
* 애플리케이션의 협력자인 경우 애플리케이션은 자동으로 마이그레이션되지 않습니다. 마이그레이션 흐름을 진행하는 동안 이러한 앱을 내보내야 한다는 메시지가 표시됩니다. 또한 [내보내기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)를 사용할 수도 있습니다. 마이그레이션 후 앱을 다시 LUIS로 가져올 수 있습니다. 가져오기 프로세스에서는 새 앱 ID를 사용하여 사용자가 소유자인 새 앱을 만듭니다.        
* 애플리케이션의 소유자인 경우 앱이 자동으로 마이그레이션되므로 앱을 내보낼 필요가 없습니다. 각 애플리케이션에 대한 모든 협력자의 목록이 포함된 이메일 템플릿이 제공되므로 모든 협력자가 마이그레이션 프로세스에 대한 알림을 받을 수 있습니다.

## <a name="migration-steps"></a>마이그레이션 단계

1. [LUIS 포털](https://www.luis.ai)에 로그인하면 마이그레이션 단계가 포함된 Azure 마이그레이션 창이 열립니다. 이 창을 해제하면 LUIS 애플리케이션을 작성할 수 없으며 표시된 작업을 통해서만 마이그레이션을 계속 진행할 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![마이그레이션 창 소개](./media/migrate-authoring-key/notify-azure-migration.png)

2. 앱 협력자가 있는 경우 각 애플리케이션의 작성 지역 및 협력자 이메일과 함께 사용자 소유의 애플리케이션 이름 목록이 표시됩니다. 협력자에게 애플리케이션 이름 왼쪽의 **보내기** 기호 단추를 클릭하여 마이그레이션에 대해 알리는 이메일을 보내는 것이 좋습니다.
협력자에게 애플리케이션에 할당된 예측 리소스가 있는 경우 애플리케이션 이름 옆에 `*` 기호가 표시됩니다. 마이그레이션 후, 협력자에게 애플리케이션을 작성할 권한이 없더라도 이러한 앱에는 이러한 예측 리소스가 할당됩니다. 그러나 예측 리소스의 소유자가 Azure Portal에서 키를 다시 [생성](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)하면 이 할당은 중단됩니다.  

   > [!div class="mx-imgBorder"]
   > ![협력자에게 알림](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   각 협력자 및 앱에 대해 기본 이메일 애플리케이션이 약간의 서식이 지정된 이메일을 사용하여 열립니다. 이메일을 보내기 전에 편집할 수 있습니다. 이메일 템플릿에는 정확한 앱 ID 및 앱 이름이 포함됩니다.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > 계정을 Azure로 마이그레이션한 후 협력자는 더 이상 앱을 사용할 수 없습니다.

3. 앱의 협력자인 경우, 앱 협력자와 공유되는 애플리케이션 이름 목록이 각 애플리케이션의 작성 지역 및 소유자 이메일과 함께 표시됩니다. 애플리케이션 이름 왼쪽에 있는 내보내기 단추를 클릭하여 앱의 복사본을 내보내는 것이 좋습니다. 이러한 앱은 자동으로 마이그레이션되지 않으므로 마이그레이션한 후에 다시 가져올 수 있습니다.
애플리케이션에 할당된 예측 리소스가 있는 경우 애플리케이션 이름 옆에 `*` 기호가 표시됩니다. 마이그레이션 후에는 더 이상 이러한 앱을 작성할 수 있는 권한이 없어도 예측 리소스가 이러한 애플리케이션에 계속 할당됩니다. 예측 리소스와 애플리케이션 간의 할당을 중단하려면 Azure Portal로 이동하여 [키를 다시 생성](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)해야 합니다.

   > [!div class="mx-imgBorder"]
   > ![애플리케이션 내보내기](./media/migrate-authoring-key/migration-export-apps.png)


4. 지역 마이그레이션 창에서, 애플리케이션이 작성된 동일한 지역의 Azure 리소스로 애플리케이션을 마이그레이션하라는 메시지가 표시됩니다. LUIS에는 3개의 작성 지역 [및 포털](./luis-reference-regions.md#luis-authoring-regions)이 있습니다. 창에는 소유한 애플리케이션이 작성된 지역이 표시됩니다. 표시되는 마이그레이션 지역은 사용하는 지역 포털 및 작성한 앱에 따라 다를 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![다중 지역 마이그레이션](./media/migrate-authoring-key/migration-regional-flow.png)

5. 각 지역에 대해 새 LUIS 제작 리소스를 만들도록 선택하거나 단추를 사용하여 기존 제작 리소스로 마이그레이션합니다.

   > [!div class="mx-imgBorder"]
   > ![새 제작 리소스 만들기 또는 기존 제작 리소스 사용 선택](./media/migrate-authoring-key/migration-multiregional-resource.png)

   다음 정보를 지정합니다.

   * **테넌트 이름**: Azure 구독이 연결된 테넌트입니다. 기본적으로 이 값은 현재 사용 중인 테넌트로 설정됩니다. 이 창을 닫고 이니셜이 포함된 화면 오른쪽 위의 아바타를 선택하여 테넌트를 전환할 수 있습니다. **Azure로 마이그레이션** 을 클릭하여 창을 다시 엽니다.
   * **Azure 구독 이름**: 리소스와 연결되는 구독입니다. 테넌트에 속하는 구독이 둘 이상 있는 경우 드롭다운 목록에서 원하는 구독 이름을 선택합니다.
   * **제작 리소스 이름**: 선택한 사용자 지정 이름입니다. 작성 및 예측 엔드포인트 쿼리에 대한 URL의 일부로 사용됩니다. 새 제작 리소스를 만드는 경우 리소스 이름은 영숫자 문자와 `-`만 포함할 수 있고 `-`로 시작하거나 끝날 수 없습니다. 다른 기호가 이름에 포함되어 있으면 리소스 생성 및 마이그레이션이 실패합니다.
   * **Azure 리소스 그룹 이름**: 드롭다운 목록에서 선택한 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다. 현재 구독에 리소스 그룹이 없으면 LUIS 포털에서 리소스 그룹을 만들 수 없습니다. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup)로 이동하여 리소스 그룹을 만든 다음 LUIS로 이동하여 로그인 프로세스를 진행합니다.

6. 모든 지역에서 성공적으로 마이그레이션한 후 마침을 클릭합니다. 이제 애플리케이션에 액세스할 수 있습니다. 포털 내 모든 지역에서 모든 애플리케이션을 계속 작성하고 유지 관리할 수 있습니다.

## <a name="migration-notes"></a>마이그레이션 정보

* 마이그레이션하기 전에 공동 작성자를 LUIS 앱 수준에서 _협력자_ 라고 합니다. 마이그레이션 후 Azure 리소스 수준에서 동일한 기능에 대해 _기여자_ 라는 Azure 역할을 사용합니다.
* 둘 이상의 [LUIS 지역 포털](./luis-reference-regions.md#luis-authoring-regions)에 로그인한 경우 한 번에 여러 지역으로 마이그레이션해야 합니다.
* 애플리케이션의 소유자인 경우 애플리케이션은 자동으로 마이그레이션됩니다. 애플리케이션의 협력자인 경우 애플리케이션이 사용자와 함께 마이그레이션되지 않습니다. 그러나 협력자에게 필요한 앱을 내보내라는 메시지가 표시됩니다.
* 애플리케이션 소유자는 마이그레이션할 앱의 하위 집합을 선택할 수 없으며, 협력자의 마이그레이션 여부는 소유자가 알 수 없습니다.
* 마이그레이션 시 자동으로 협력자를 Azure 제작 리소스로 이동하거나 추가하지 않습니다. 마이그레이션 후 앱 소유자가 이 단계를 완료해야 합니다. 이 단계에서는 [Azure 제작 리소스에 대한 권한](./luis-how-to-collaborate.md)이 필요합니다.
* 기여자를 Azure 리소스에 할당한 후 기여자는 마이그레이션을 수행해야 애플리케이션에 액세스할 수 있습니다. 그러지 않으면 애플리케이션을 작성할 수 없습니다.


## <a name="using-apps-after-migration"></a>마이그레이션 후 앱 사용

마이그레이션 프로세스가 완료되면 이제 소유자의 모든 LUIS 앱이 단일 LUIS 제작 리소스에 할당됩니다.
**내 앱** 목록에는 새 제작 리소스로 마이그레이션된 앱이 표시됩니다. 앱에 액세스하기 전에 **다른 제작 리소스 선택** 을 선택하여 구독 및 제작 리소스를 선택하면 작성할 수 있는 앱을 확인할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![구독 및 제작 리소스 선택](./media/migrate-authoring-key/select-sub-and-resource.png)


앱을 프로그래밍 방식으로 편집하려는 경우 작성 키 값이 필요합니다. 이러한 값은 LUIS 포털의 화면 위쪽에서 **관리** 를 클릭한 다음 **Azure 리소스** 를 선택하면 표시됩니다. Azure Portal에서 리소스의 **키 및 엔드포인트** 페이지에서도 제공됩니다. 또한 더 많은 제작 리소스를 만들어 동일한 페이지에서 할당할 수 있습니다.

## <a name="adding-contributors-to-authoring-resources"></a>제작 리소스에 기여자 추가

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

제작 리소스에 [기여자를 추가하는 방법](luis-how-to-collaborate.md)을 알아봅니다. 기여자는 해당 리소스의 모든 애플리케이션에 액세스할 수 있습니다.

해당 리소스에 대한 **액세스 제어(IAM)** 페이지에서 Azure Portal의 제작 리소스에 기여자를 추가할 수 있습니다. 자세한 내용은 [앱에 기여자 추가](luis-how-to-collaborate.md)를 참조하세요.

> [!Note]
> LUIS 앱의 소유자가 Azure 리소스에서 협력자를 기여자로 마이그레이션하고 추가한 경우 협력자는 마이그레이션하지 않는 한 여전히 앱에 액세스할 수 없습니다.

## <a name="troubleshooting-the-migration-process"></a>마이그레이션 프로세스 문제 해결

드롭다운 목록에서 Azure 구독을 찾을 수 없는 경우:
* Cognitive Services 리소스를 만들 수 있는 유효한 Azure 구독이 있는지 확인합니다. [Azure Portal](https://ms.portal.azure.com)로 이동하여 구독의 상태를 확인합니다. 아직 없는 경우 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/cognitive-services/).
* 유효한 구독과 연결된 적절한 테넌트에 있는지 확인합니다. 사용자 이니셜이 포함된 화면 오른쪽 위에 있는 아바타를 선택하여 테넌트를 전환할 수 있습니다.

  > [!div class="mx-imgBorder"]
  > ![디렉터리 전환 페이지](./media/migrate-authoring-key/switch-directories.png)

**기존 제작 리소스 사용** 옵션을 선택할 때 기존에 있는 제작 리소스를 찾을 수 없는 경우:
* 리소스가 마이그레이션하는 데 사용한 것과 다른 지역에서 만들어졌을 수 있습니다.
* 대신 LUIS 포털에서 새 리소스를 만듭니다.

**새 제작 리소스 만들기** 옵션을 선택하면 "사용자의 Azure 정보를 검색하지 못했습니다. 나중에 다시 시도하세요."라는 오류 메시지와 함께 마이그레이션이 실패합니다.
* 구독에는 구독당 구독 지역별로 10개 이상의 제작 리소스가 있을 수 있습니다. 이 경우 새 제작 리소스를 만들 수 없습니다.
* **기존 제작 리소스 사용** 옵션을 선택하고 구독에서 기존 리소스 중 하나를 선택하여 마이그레이션합니다.

## <a name="create-new-support-request"></a>새 지원 요청 만들기

문제 해결 섹션에서 해결되지 않는 마이그레이션에 문제가 있는 경우 [지원 항목을 만들고](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 다음 필드에서 아래 정보를 제공하세요.

   * **문제 유형**: 기술
   * **구독**: 드롭다운 목록에서 구독을 선택합니다.
   * **서비스**: "Cognitive Services"를 검색하고 선택합니다.
   * **리소스**: 기존 리소스가 있는 경우 LUIS 리소스를 선택합니다. 그렇지 않으면 일반 질문을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [작성 및 런타임 키에 대한 개념](luis-how-to-azure-subscription.md) 을 참조하세요.
* [키 할당](luis-how-to-azure-subscription.md) [및 기여자 추가](luis-how-to-collaborate.md) 방법을 참조하세요.
