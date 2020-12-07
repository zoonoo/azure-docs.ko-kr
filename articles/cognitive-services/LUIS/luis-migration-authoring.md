---
title: Azure 리소스 제작 키로 마이그레이션
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 전자 메일 계정에서 Azure 리소스로 Language Understanding (LUIS) 제작 인증을 마이그레이션하는 방법을 설명 합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/07/2020
ms.openlocfilehash: 243c9834aa256e26d620c00ac0fa7a262919aabd
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762684"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure 리소스 제작 키로 마이그레이션

> [!IMPORTANT]
>  12 월 3 일부 터 기존 LUIS 사용자는 LUIS 응용 프로그램을 계속 제작 하기 위해 마이그레이션 프로세스를 완료 해야 합니다.

LUIS (Language Understanding) 제작 인증을 전자 메일 계정에서 Azure 리소스로 변경 했습니다. 아직 마이그레이션하지 않은 경우이 문서를 사용 하 여 계정을 마이그레이션하는 방법을 알아보세요.  


## <a name="what-is-migration"></a>마이그레이션 이란?

마이그레이션은 전자 메일 계정에서 Azure 리소스로의 제작 인증을 변경 하는 프로세스입니다. 마이그레이션한 후 계정이 Azure 구독 및 Azure 제작 리소스에 연결 됩니다. *모든 LUIS 사용자 (앱 소유자 및 협력자)는 결국 마이그레이션해야 합니다.*

마이그레이션은 [LUIS 포털](https://www.luis.ai)에서 수행 해야 합니다. 예를 들어 LUIS CLI를 사용 하 여 제작 키를 만드는 경우 LUIS 포털에서 마이그레이션 프로세스를 완료 해야 합니다. 마이그레이션 후에도 응용 프로그램에 공동 작성자가 있을 수 있지만 응용 프로그램 수준 대신 Azure 리소스 수준에 추가 됩니다.

> [!Note]
> 마이그레이션하기 전에 작성를 LUIS app 수준에서 _협력자_ 라고 합니다. 마이그레이션 후 azure 리소스 수준에서 동일한 기능에 대해 _참가자_ 의 azure 역할을 사용 합니다.

## <a name="notes-before-you-migrate"></a>마이그레이션하기 전 참고 사항

* 마이그레이션은 되돌릴 수 없습니다.
* 둘 이상의 [LUIS 지역 포털](./luis-reference-regions.md#luis-authoring-regions)에 로그인 한 경우 한 번에 여러 지역으로 마이그레이션해야 한다는 메시지가 표시 됩니다.
* 응용 프로그램의 소유자 인 경우 응용 프로그램은 자동으로 마이그레이션됩니다.
* 소유자는 마이그레이션할 앱의 하위 집합을 선택할 수 없으며 프로세스를 되돌릴 수 없습니다.
* 소유자가 마이그레이션한 후에는 협력자의 계정에서 응용 프로그램이 사라집니다.
* 소유자는 사용자에 게 마이그레이션을 알리기 위해 협력자에 게 전자 메일을 보낼지 묻는 메시지를 표시 합니다.
* 응용 프로그램의 협력자 인 경우 응용 프로그램은 사용자와 함께 마이그레이션하지 않습니다. 그러나 공동 작업자에 게 필요한 앱을 내보내도록 요청 하는 메시지가 표시 됩니다.
* 담당자가 공동 작업자의 마이그레이션 여부를 알 수 있는 방법은 없습니다.
* 마이그레이션은 자동으로 협력자를 Azure authoring resource로 이동 하거나 추가 하지 않습니다. 앱 소유자는 마이그레이션 후이 단계를 완료 해야 하는 소유자입니다. 이 단계에서는 [Azure 제작 리소스에 대 한 권한이](./luis-how-to-collaborate.md)필요 합니다.
* Azure 리소스에 협력자를 할당 한 후에는 응용 프로그램에 액세스 하기 전에 마이그레이션해야 합니다. 그렇지 않으면 응용 프로그램을 작성 하기 위한 액세스 권한이 없습니다.
* 마이그레이션된 사용자는 응용 프로그램의 협력자로 추가할 수 없습니다.


> [!Note]
> 예측 런타임 리소스를 만들어야 하는 경우에 [는 별도의 프로세스](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 를 수행 해야 합니다.

## <a name="migration-prerequisites"></a>마이그레이션 필수 조건

* 유효한 Azure 구독과 연결 해야 합니다. 테 넌 트 관리자에 게 구독에 대 한 추가를 요청 하거나 [무료로 등록할](https://azure.microsoft.com/free/cognitive-services)수 있습니다.
* LUIS 포털에서 또는 [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)에서 LUIS Azure 제작 리소스를 만들어야 합니다. LUIS 포털에서 제작 리소스를 만드는 작업은 다음 섹션에서 설명 하는 마이그레이션 프로세스의 일부입니다.
* 응용 프로그램의 협력자 인 경우 응용 프로그램은 자동으로 마이그레이션되지 않습니다. 마이그레이션 흐름을 진행 하는 동안 이러한 앱을 내보내야 한다는 메시지가 표시 됩니다. 또한 [내보내기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)를 사용할 수 있습니다. 마이그레이션 후 앱을 다시 LUIS으로 가져올 수 있습니다. 가져오기 프로세스에서는 사용자가 소유자 인 새 앱 ID를 사용 하 여 새 앱을 만듭니다.
* 응용 프로그램의 소유자 인 경우 자동으로 마이그레이션될 앱을 내보낼 필요가 없습니다. 각 응용 프로그램에 대 한 모든 협력자의 목록이 포함 된 전자 메일 템플릿이 제공 되므로 마이그레이션 프로세스에 대 한 알림이 제공 될 수 있습니다.

> [!Note]
> F0 계층에 표시 된 대로 LUIS 앱을 제작 하는 것은 무료입니다. [가격 책정 계층에](luis-limits.md#key-limits)대해 자세히 알아보세요.


## <a name="migration-steps"></a>마이그레이션 단계

1. [LUIS 포털](https://www.luis.ai)에 로그인 하면 마이그레이션 단계를 포함 하는 Azure 마이그레이션 창이 열립니다. 이 작업을 해제 하면 LUIS 응용 프로그램을 작성할 수 없으며 마이그레이션을 계속 진행 하기 위해 표시 되는 유일한 작업은입니다.

    > [!div class="mx-imgBorder"]
    > ![마이그레이션 창 소개](./media/migrate-authoring-key/notify-azure-migration.png)

2. 앱에 협력자가 있는 경우 각 응용 프로그램의 제작 지역 및 협력자 메일과 함께 사용자 소유의 응용 프로그램 이름 목록이 표시 됩니다. 공동 작업자에 게 응용 프로그램 이름 왼쪽의 기호 **보내기** 단추를 클릭 하 여 마이그레이션에 대해 알리는 전자 메일을 보내는 것이 좋습니다.
`*`협력자가 응용 프로그램에 할당 된 예측 리소스를가지고 있으면 응용 프로그램 이름 옆에 기호가 표시 됩니다. 공동 작업자는 응용 프로그램을 작성 하는 데 액세스할 수 없더라도 마이그레이션 후에도 이러한 앱에는 이러한 예측 리소스가 할당 됩니다. 그러나 예측 리소스의 소유자가 Azure Portal에서 키를 다시 [생성](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) 하면이 할당은 중단 됩니다.  

   > [!div class="mx-imgBorder"]
   > ![협력자에 게 알림](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   각 협력자 및 앱에 대해 기본 전자 메일 응용 프로그램은 약간의 서식이 지정 된 전자 메일을 사용 하 여 열립니다. 전자 메일을 보내기 전에 편집할 수 있습니다. 전자 메일 템플릿에는 정확한 앱 ID 및 앱 이름이 포함 됩니다.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > 계정을 Azure로 마이그레이션한 후에는 공동 작업자에 게 더 이상 앱을 사용할 수 없습니다.

3. 앱의 협력자 인 경우 사용자와 공유 되는 응용 프로그램 이름 목록이 각 응용 프로그램의 제작 지역 및 소유자 전자 메일과 함께 표시 됩니다. 응용 프로그램 이름 왼쪽에 있는 내보내기 단추를 클릭 하 여 앱의 복사본을 내보내는 것이 좋습니다. 이러한 앱은 자동으로 마이그레이션되지 않으므로 마이그레이션한 후에 다시 가져올 수 있습니다.
`*`응용 프로그램에 할당 된 예측 리소스가 있으면 응용 프로그램 이름 옆에 기호가 표시 됩니다. 마이그레이션 후에는 더 이상 이러한 앱을 제작할 수 있는 권한이 없어도 예측 리소스가 이러한 응용 프로그램에 계속 할당 됩니다. 예측 리소스와 응용 프로그램 간의 할당을 중단 하려면 Azure Portal로 이동 하 여 [키를 다시 생성](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)해야 합니다.

   > [!div class="mx-imgBorder"]
   > ![응용 프로그램을 내보냅니다.](./media/migrate-authoring-key/migration-export-apps.png)


4. 영역을 마이그레이션하기 위한 창에서, 응용 프로그램을 작성 된 동일한 지역의 Azure 리소스로 마이그레이션 하 라는 메시지가 표시 됩니다. LUIS에는 세 가지 제작 지역과 [포털이](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)있습니다. 창에는 소유한 응용 프로그램이 작성 된 지역이 표시 됩니다. 표시 되는 마이그레이션 지역은 사용 하는 지역 포털 및 사용자가 작성 한 앱에 따라 다를 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![다중 지역 마이그레이션.](./media/migrate-authoring-key/migration-regional-flow.png)

5. 각 지역에 대해 새 LUIS authoring 리소스를 만들도록 선택 하거나 단추를 사용 하 여 기존 항목으로 마이그레이션합니다.

   > [!div class="mx-imgBorder"]
   > ![또는 기존 제작 리소스를 만들도록 선택 합니다.](./media/migrate-authoring-key/migration-multiregional-resource.png)

   다음 정보를 지정합니다.

   * **테 넌 트 이름**: Azure 구독과 연결 된 테 넌 트입니다. 기본적으로이는 현재 사용 중인 테 넌 트로 설정 됩니다. 이 창을 닫고 이니셜을 포함 하 여 화면 오른쪽 위에 있는 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다. **Azure로 마이그레이션** 을 클릭 하 여 창을 다시 엽니다.
   * **Azure 구독 이름**: 리소스와 연결 되는 구독입니다. 테 넌 트에 속하는 구독이 둘 이상 있는 경우 드롭다운 목록에서 원하는 하나를 선택 합니다.
   * **제작 리소스 이름**: 선택한 사용자 지정 이름입니다. 작성 및 예측 끝점 쿼리에 대 한 URL의 일부로 사용 됩니다. 새 제작 리소스를 만드는 경우 리소스 이름에는 영숫자 문자인만 포함할 수 `-` 있고로 시작 하거나 끝날 수 없습니다 `-` . 다른 기호가 이름에 포함 되어 있으면 리소스를 만들고 마이그레이션하지 못합니다.
   * **Azure 리소스 그룹 이름**: 드롭다운 목록에서 선택한 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다. 현재 구독에 리소스 그룹이 없으면 LUIS 포털에서 리소스 그룹을 만들 수 없습니다. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) 로 이동 하 여 LUIS으로 이동 하 여 로그인 프로세스를 계속 합니다.

6. 모든 지역에서 성공적으로 마이그레이션한 후 마침을 클릭 합니다. 이제 응용 프로그램에 액세스할 수 있습니다. 포털 내 모든 지역에서 모든 응용 프로그램을 계속 작성 하 고 유지 관리할 수 있습니다.


## <a name="using-apps-after-migration"></a>마이그레이션 후 앱 사용

마이그레이션 프로세스가 완료 되 면 소유자 인 모든 LUIS apps가 단일 LUIS authoring resource에 할당 됩니다.
**내 앱** 목록에는 새 제작 리소스로 마이그레이션된 앱이 표시 됩니다. 앱에 액세스 하기 전에 **다른 제작 리소스 선택** 을 선택 하 여 구독 및 제작 리소스를 선택 하 여 작성할 수 있는 앱을 확인 합니다.

> [!div class="mx-imgBorder"]
> ![구독 및 제작 리소스 선택](./media/migrate-authoring-key/select-sub-and-resource.png)


앱을 프로그래밍 방식으로 편집 하려는 경우 제작 키 값이 필요 합니다. 이러한 값은 LUIS 포털의 화면 위쪽에서 **관리** 를 클릭 한 다음 **Azure 리소스** 를 선택 하 여 표시 됩니다. 리소스의 **키 및 끝점** 페이지에 있는 Azure Portal 에서도 사용할 수 있습니다. 또한 더 많은 제작 리소스를 만들어 동일한 페이지에서 할당할 수 있습니다.

## <a name="adding-contributors-to-authoring-resources"></a>제작 리소스에 참가자 추가

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

제작 리소스에 [참가자를 추가 하는 방법을](luis-how-to-collaborate.md) 알아봅니다. 기여자는 해당 리소스의 모든 응용 프로그램에 액세스할 수 있습니다.

Azure Portal, 해당 리소스에 대 한 **Access Control (IAM)** 페이지에서 제작 리소스에 참가자를 추가할 수 있습니다. 자세한 내용은 [앱에 참가자 추가](luis-how-to-collaborate.md)를 참조 하세요.

> [!Note]
> LUIS 앱의 소유자가 Azure 리소스에서 협력자로 협력자를 마이그레이션하고 추가 하는 경우에도 해당 협력자는 마이그레이션하지 않는 한 여전히 앱에 액세스할 수 없습니다.


## <a name="troubleshooting-the-migration-process"></a>마이그레이션 프로세스 문제 해결

드롭다운 목록에서 Azure 구독을 찾을 수 없는 경우:
* Cognitive Services 리소스를 만들 수 있는 유효한 Azure 구독이 있는지 확인 합니다. [Azure Portal](https://ms.portal.azure.com) 로 이동 하 여 구독의 상태를 확인 합니다. 계정이 없는 경우 [무료 Azure 계정을 만듭니다](https://azure.microsoft.com/free/cognitive-services/).
* 유효한 구독과 연결 된 적절 한 테 넌 트에 있는지 확인 합니다. 사용자 이니셜을 포함 하 여 화면 오른쪽 위에 있는 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다.

  > [!div class="mx-imgBorder"]
  > ![디렉터리를 전환 하기 위한 페이지](./media/migrate-authoring-key/switch-directories.png)

기존 제작 리소스가 있지만 **기존 제작 리소스 사용** 옵션을 선택 하는 경우이 리소스를 찾을 수 없는 경우:
* 리소스가 마이그레이션하는 데 사용한 것과 다른 지역에서 만들어졌을 수 있습니다.
* 대신 LUIS 포털에서 새 리소스를 만듭니다.

**새 제작 리소스 만들기** 옵션을 선택 하면 "사용자의 Azure 정보를 검색 하지 못했습니다. 나중에 다시 시도 하십시오." 라는 오류 메시지와 함께 마이그레이션이 실패 합니다.
* 구독에는 구독 당 지역 별로 10 개 이상의 제작 리소스가 있을 수 있습니다. 이 경우 새 제작 리소스를 만들 수 없습니다.
* **기존 제작 리소스 사용** 옵션을 선택 하 고 구독에서 기존 리소스 중 하나를 선택 하 여 마이그레이션합니다.

## <a name="create-new-support-request"></a>새 지원 요청 만들기

문제 해결 섹션에서 해결 되지 않은 마이그레이션에 문제가 있는 경우 [지원 항목을 만들고](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 다음 필드를 사용 하 여 아래 정보를 제공 하세요.

   * **문제 유형**: 기술
   * **구독**: 드롭다운 목록에서 구독을 선택 합니다.
   * **서비스**: "Cognitive Services"을 검색 하 고 선택 합니다.
   * **리소스**: LUIS 리소스 (기존 리소스를 선택 하는 경우)를 선택 합니다. 그렇지 않으면 일반 질문을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [제작 및 런타임 키에 대 한 개념](luis-how-to-azure-subscription.md) 검토
* [키를 할당](luis-how-to-azure-subscription.md) 하 고 [참가자를 추가](luis-how-to-collaborate.md) 하는 방법 검토
