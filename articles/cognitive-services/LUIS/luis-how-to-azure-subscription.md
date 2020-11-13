---
title: 제작 및 런타임 키 사용-LUIS
description: LUIS를 처음 사용 하는 경우 제작 키를 만들 필요가 없습니다. 앱을 게시 한 다음 런타임 끝점을 사용 하려는 경우에는 앱에 런타임 키를 만들고 할당 해야 합니다.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07a274bd4ac227b6260f7891b24dad0eacdfb4f7
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561526"
---
# <a name="create-luis-resources"></a>LUIS 리소스 만들기

작성 및 쿼리 예측 런타임 리소스는 Language Understanding (LUIS) 앱 및 예측 끝점에 대 한 인증을 제공 합니다.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 리소스

LUIS는 세 가지 유형의 Azure 리소스와 Azure가 아닌 리소스 하나를 허용 합니다.

|리소스|목적|인식 서비스 `kind`|인식 서비스 `type`|
|--|--|--|--|
|제작 리소스|응용 프로그램을 만들고, 관리 하 고, 학습 하 고, 테스트 하 고, 게시할 수 있습니다. LUIS apps를 프로그래밍 방식으로 또는 LUIS 포털에서 작성 하려는 경우 [LUIS authoring 리소스를 만드세요](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) . Azure authoring resources를 응용 프로그램에 연결 하기 전에 [LUIS 계정을 마이그레이션해야](luis-migration-authoring.md#what-is-migration) 합니다. [참가자 역할](#contributions-from-other-authors)을 사용자에 게 할당 하 여 제작 리소스에 대 한 사용 권한을 제어할 수 있습니다. <br><br> LUIS authoring 리소스에 대해 하나의 계층을 사용할 수 있습니다.<br> <ul> <li>무료 **F0 authoring resource** -100만 무료 제작 트랜잭션을 제공 하 고 1000 무료 테스트 예측 끝점 요청을 매월 제공 합니다. |`LUIS.Authoring`|`Cognitive Services`|
|예측 리소스| LUIS 응용 프로그램을 게시 한 후 예측 리소스/키를 사용 하 여 예측 끝점 요청을 쿼리 합니다. 클라이언트 앱이 authoring 또는 스타터 리소스에서 제공 하는 1000 요청 보다 많은 예측을 요청 하기 전에 LUIS 예측 리소스를 만듭니다. <br><br> 예측 리소스에 대해 두 개의 계층을 사용할 수 있습니다.<br><ul> <li> 무료 예측 끝점 요청을 월간 1만 제공 하는 **무료 F0 예측 리소스** 입니다.<br> <li> **표준 S0 예측 리소스** (유료 계층)입니다. [가격 책정에 대해 자세히 알아보세요.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|시작/평가판 리소스|응용 프로그램을 만들고, 관리 하 고, 학습 하 고, 테스트 하 고, 게시할 수 있습니다. LUIS에 처음 로그인 할 때 시작 리소스 옵션을 선택 하면 기본적으로이 리소스가 생성 됩니다. 시작 키는 결국 사용 되지 않습니다. 모든 LUIS 사용자는 [자신의 계정을 마이그레이션하고](luis-migration-authoring.md#what-is-migration) LUIS 응용 프로그램을 제작 리소스에 연결 해야 합니다. 제작 리소스와 달리이 리소스는 Azure 역할 기반 액세스 제어에 대 한 권한을 제공 하지 않습니다. <br><br> 제작 리소스와 마찬가지로 스타터 리소스는 100만 무료 제작 트랜잭션과 1000 무료 테스트 예측 끝점 요청을 제공 합니다.|-|Azure 리소스가 아닙니다.|
|[Cognitive Services multiservice 리소스 키](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS 및 기타 지원 되는 인식 서비스와 공유 되는 쿼리 예측 끝점 요청|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS는 두 가지 유형의 F0 (무료 계층) 리소스를 제공 합니다. 하나는 트랜잭션을 작성 하 고 다른 하나는 예측 트랜잭션에 사용 됩니다. 예측 트랜잭션에 대 한 무료 할당량을 모두 사용 하는 경우 F0 예측 리소스를 사용 하 고 있습니다 .이 리소스를 사용 하 여 월별 1000 예측 트랜잭션을 제공 하는 제작 리소스는 물론 1만 개의 무료 트랜잭션을 제공할 수 있습니다.

Azure 리소스 만들기 프로세스가 완료 되 면 LUIS 포털에서 리소스를 앱에 [할당](#assign-a-resource-to-an-app) 합니다.

> [!important]
> 게시 하 고 쿼리 하려는 [지역](luis-reference-regions.md#publishing-regions) 에서 LUIS apps를 작성 해야 합니다.

## <a name="resource-ownership"></a>리소스 소유권

LUIS 리소스와 같은 Azure 리소스는 리소스를 포함 하는 구독이 소유 합니다.

리소스의 소유권을 변경 하려면 다음 작업 중 하나를 수행 하면 됩니다.
* 구독의 [소유권](../../cost-management-billing/manage/billing-subscription-transfer.md) 을 양도 합니다.
* LUIS 앱을 파일로 내보낸 다음 다른 구독에서 앱을 가져옵니다. 내보내기는 LUIS 포털의 **내 앱** 페이지에서 사용할 수 있습니다.


## <a name="resource-limits"></a>리소스 한계

### <a name="authoring-key-creation-limits"></a>작성 키 생성 제한

구독 당 지역별 제작 키를 최대 10 개까지 만들 수 있습니다.

자세한 내용은 [키 제한](luis-limits.md#key-limits) 및 [Azure 지역](luis-reference-regions.md)을 참조 하세요.

게시 지역은 작성 지역과 다릅니다. 클라이언트 응용 프로그램을 배치할 게시 영역에 해당 하는 제작 지역에서 앱을 만들어야 합니다.

### <a name="errors-for-key-usage-limits"></a>키 사용 제한에 대 한 오류

사용 제한은 가격 책정 계층을 기반으로 합니다.

TPS(초당 트랜잭션 수) 할당량을 초과하는 경우 HTTP 429 오류가 표시됩니다. 월간 트랜잭션 (TPM) 할당량을 초과 하는 경우 HTTP 403 오류를 받게 됩니다.


### <a name="reset-an-authoring-key"></a>제작 키 다시 설정

[마이그레이션된 제작 리소스](luis-migration-authoring.md) 앱의 경우: 제작 키가 손상 되 면 제작 리소스에 대 한 **키** 페이지의 Azure Portal에서 키를 다시 설정 합니다.

마이그레이션되지 않은 앱의 경우: LUIS 포털의 모든 앱에서 키가 다시 설정 됩니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다.

### <a name="regenerate-an-azure-key"></a>Azure 키 다시 생성

Azure Portal의 **키** 페이지에서 Azure 키를 다시 생성할 수 있습니다.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>앱 소유권, 액세스 및 보안

앱은 소유자의 구독에 의해 결정 되는 Azure 리소스에 의해 정의 됩니다.

LUIS 앱을 이동할 수 있습니다. Azure Portal 또는 Azure CLI를 사용 하 여이 작업을 수행 하는 데 도움이 되는 다음 리소스를 사용 합니다.

* [LUIS authoring resources 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [동일한 구독 내 또는 구독 간에 리소스 이동](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>다른 작성자의 기여

[마이그레이션된 제작 리소스](luis-migration-authoring.md) 앱의 경우: **액세스 제어 (IAM)** 페이지를 사용 하 여 Azure Portal에서 제작 리소스에 대 한 _참가자_ 를 관리할 수 있습니다. 협력자의 이메일 주소 및 참가자 역할을 사용 하 여 [사용자를 추가 하는 방법을](luis-how-to-collaborate.md) 알아봅니다.

아직 마이그레이션하지 않은 앱의 경우: LUIS 포털의 **관리-> 협력자** 페이지에서 모든 _협력자_ 를 관리할 수 있습니다.

### <a name="query-prediction-access-for-private-and-public-apps"></a>개인 및 공용 앱에 대 한 쿼리 예측 액세스

개인 앱의 경우 소유자 및 참가자에 대해 쿼리 예측 런타임 액세스를 사용할 수 있습니다. 공용 앱의 경우 고유한 Azure [인식 서비스](../cognitive-services-apis-create-account.md) 또는 [LUIS](#create-resources-in-the-azure-portal) 런타임 리소스와 공용 앱 ID를 가진 사용자가 런타임 액세스를 사용할 수 있습니다.

현재는 공용 앱의 카탈로그가 없습니다.

### <a name="authoring-permissions-and-access"></a>제작 권한 및 액세스
[LUIS](luis-reference-regions.md#luis-website) 포털 또는 [제작 api](https://go.microsoft.com/fwlink/?linkid=2092087) 에서 앱에 액세스 하는 것은 Azure authoring resource에 의해 제어 됩니다.

소유자와 모든 참가자는 앱을 제작할 수 있는 권한이 있습니다.

|액세스 작성에는 다음이 포함 됩니다.|참고|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용 이면 제작 또는 끝점 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|학습|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>예측 끝점 런타임 액세스

예측 끝점을 쿼리 하기 위한 액세스는 **관리** 섹션의 **응용 프로그램 정보** 페이지에 있는 설정에 의해 제어 됩니다.

|[개인 끝점](#runtime-security-for-private-apps)|[공용 엔드포인트](#runtime-security-for-public-apps)|
|:--|:--|
|소유자 및 참가자가 사용할 수 있음|소유자, 참가자 및 앱 ID를 알고 있는 모든 사용자가 사용할 수 있음|

서버 간 환경에서 LUIS 런타임 키를 호출 하 여 해당 키를 볼 수 있는 사용자를 제어할 수 있습니다. Bot에서 LUIS를 사용 하는 경우 봇과 LUIS 간의 연결은 이미 더 안전 합니다. LUIS 끝점을 직접 호출 하는 경우 [AZURE AD](https://azure.microsoft.com/services/active-directory/)와 같은 제어 된 액세스 권한이 있는 서버 쪽 API (예: azure [function](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API를 호출 하 여 인증 하 고 권한 부여를 확인 한 경우에는에 대 한 호출을 LUIS에 전달 합니다. 이 전략은 메시지 가로채기 (man-in-the-middle) 공격을 방지 하지 않습니다. 그러나 사용자의 키 및 끝점 URL을 난독 처리 하 여 액세스를 추적할 수 있으며, 끝점 응답 로깅 (예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

### <a name="runtime-security-for-private-apps"></a>전용 앱에 대 한 런타임 보안

개인 앱의 런타임은 다음 키에만 사용할 수 있습니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1000 끝점 적중 수|
|협력자/기여자 제작 키| 최대 1000 끝점 적중 수|
|작성자 또는 협력자/기여자에 의해 LUIS에 할당 된 모든 키|키 사용량 계층 기반|

### <a name="runtime-security-for-public-apps"></a>공용 앱에 대 한 런타임 보안

앱이 공용으로 구성 된 경우 키가 전체 끝점 할당량을 사용 하지 않는 한 유효한 LUIS authoring key 또는 LUIS 끝점 키가 _이를 쿼리할_ 수 있습니다.

소유자 또는 참가자가 아닌 사용자는 앱 ID가 지정 된 경우에만 공용 앱의 런타임에 액세스할 수 있습니다. LUIS에는 공용 시장 또는 사용자가 공용 앱을 검색할 수 있는 다른 방법이 없습니다.

공용 앱은 모든 지역에 게시 됩니다. 따라서 지역 기반 LUIS 리소스 키가 있는 사용자는 리소스 키와 연결 된 지역에 상관 없이 앱에 액세스할 수 있습니다.


### <a name="control-access-to-your-query-prediction-endpoint"></a>쿼리 예측 끝점에 대 한 액세스 제어

서버 간 환경에서 호출 하 여 LUIS 예측 런타임 끝점 키를 볼 수 있는 사람을 제어할 수 있습니다. Bot에서 LUIS를 사용 하는 경우 봇과 LUIS 간의 연결은 이미 더 안전 합니다. LUIS 끝점을 직접 호출 하는 경우 [AZURE AD](https://azure.microsoft.com/services/active-directory/)와 같은 제어 된 액세스 권한이 있는 서버 쪽 API (예: azure [function](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 메시지 가로채기 (man-in-the-middle) 공격을 방지 하지 않습니다. 그러나 사용자의 끝점을 난독 처리 하 고, 사용자가 액세스를 추적할 수 있도록 하 고, 끝점 응답 로깅 (예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>LUIS 포털에 로그인 하 고 제작을 시작 합니다.

1. [LUIS 포털](https://www.luis.ai) 에 로그인 하 고 사용 약관에 동의 합니다.
1. Azure LUIS authoring key를 선택 하 여 LUIS 앱 제작을 시작 합니다.

   ![시작 화면을 보여 주는 스크린샷](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 리소스 선택 프로세스를 완료 하면 [새 앱을 만듭니다](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Azure CLI에서 리소스 만들기

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 를 사용 하 여 각 리소스를 개별적으로 만들 수 있습니다.

리소스 `kind` :

* 만들 `LUIS.Authoring`
* 예측 `LUIS`

1. Azure CLI에 로그인 합니다.

    ```azurecli
    az login
    ```

    이 명령은 올바른 계정을 선택 하 고 인증을 제공할 수 있도록 브라우저를 엽니다.

1. 이라는 종류의 LUIS authoring 리소스를 만듭니다 `LUIS.Authoring` `my-luis-authoring-resource` . 해당 지역에 대해 이라는 _기존_ 리소스 그룹에 만듭니다 `my-resource-group` `westus` .

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 이라는 종류의 LUIS 예측 끝점 리소스를 만듭니다 `LUIS` `my-luis-prediction-resource` . 해당 지역에 대해 이라는 _기존_ 리소스 그룹에 만듭니다 `my-resource-group` `westus` . 무료 계층에서 제공 하는 것 보다 높은 처리량을 원하는 경우 `F0` 로 변경 `S0` 합니다. [가격 책정 계층 및 처리량에 대해 자세히 알아보세요.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > 이러한 키는 **Manage**  >  LUIS 포털의 **Azure 리소스** 관리 페이지에서 할당 될 때까지 LUIS 포털에서 사용 되지 않습니다.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>LUIS 포털에서 리소스 할당

단일 앱 또는 LUIS의 모든 앱에 대 한 제작 리소스를 할당할 수 있습니다. 다음 절차에서는 단일 제작 리소스에 모든 앱을 할당 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다.
1. 오른쪽 위 모서리에서 사용자 계정을 선택 하 고 **설정** 을 선택 합니다.
1. **사용자 설정** 페이지에서 **제작 리소스 추가** 를 선택 하 고 기존 제작 리소스를 선택 합니다. **저장** 을 선택합니다.

## <a name="assign-a-resource-to-an-app"></a>앱에 리소스 할당

>[!NOTE]
>Azure 구독이 없는 경우 새 리소스를 할당 하거나 만들 수 없습니다. [Azure 무료 계정을](https://azure.microsoft.com/en-us/free/) 만든 다음 LUIS로 돌아와서 포털에서 새 리소스를 만들어야 합니다.

이 절차를 사용 하 여 제작 또는 예측 리소스를 만들거나 응용 프로그램에 하나를 할당할 수 있습니다. 

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다. **내 앱** 목록에서 앱을 선택합니다.
1. Azure 리소스 **관리** 로 이동 합니다  >  **Azure Resources**.

    ![Azure 리소스 페이지를 보여 주는 스크린샷](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. **예측 리소스** 또는 **제작 리소스** 탭에서 **예측 리소스 추가** 또는 **제작 리소스 추가** 단추를 선택 합니다.
1. 양식의 필드를 사용 하 여 올바른 리소스를 찾은 다음 **저장** 을 선택 합니다.
1. 기존 리소스가 없는 경우 창 맨 아래에서 **새 LUIS 리소스 만들기** 를 선택 하 여 만들 수 있습니다.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>LUIS 포털을 사용 하지 않고 쿼리 예측 런타임 리소스 할당

CI/CD 파이프라인과 같은 자동화 된 프로세스의 경우 LUIS 런타임 리소스를 LUIS 앱에 할당 하는 것을 자동화할 수 있습니다. 이렇게 하려면 다음 단계를 완료 합니다.

1. [이 웹 사이트](https://resources.azure.com/api/token?plaintext=true)에서 Azure Resource Manager 토큰을 가져옵니다. 이 토큰은 만료 되므로 바로 사용 하세요. 요청은 Azure Resource Manager 토큰을 반환합니다.

    ![Azure Resource Manager 토큰을 요청 하는 웹 사이트를 보여 주는 스크린샷](./media/luis-manage-keys/get-arm-token.png)

1. 토큰을 사용 하 여 구독 간에 LUIS 런타임 리소스를 요청 합니다. 사용자 계정이 액세스할 수 있는 [GET LUIS Azure ACCOUNTS API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)를 사용 합니다.

    이 POST API에는 다음 값이 필요 합니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 단어 `Bearer` 와 공백이와 야 합니다.|
    |`Ocp-Apim-Subscription-Key`|작성 키|

    API는 LUIS 구독을 나타내는 JSON 개체의 배열을 반환 합니다. 반환 되는 값에는로 반환 되는 구독 ID, 리소스 그룹 및 리소스 이름이 포함 됩니다 `AccountName` . LUIS 앱에 할당 하려는 LUIS 리소스인 배열의 항목을 찾습니다.

1. [LUIS Azure 계정을 응용 프로그램 API에 할당](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) 을 사용 하 여 LUIS 리소스에 토큰을 할당 합니다.

    이 POST API에는 다음 값이 필요 합니다.

    |Type|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 단어 `Bearer` 와 공백이와 야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|작성 키|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID
    |본문||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    이 API는 성공 하면를 반환 `201 - created status` 합니다.

## <a name="unassign-a-resource"></a>리소스 할당 해제

1. [LUIS 포털](https://www.luis.ai)에 로그인 한 다음 **내 앱** 목록에서 앱을 선택 합니다.
1. **Manage**  >  **Azure 리소스** 관리로 이동 합니다.
1. **예측 리소스** 또는 **제작 리소스** 탭에서 리소스에 대 한 **리소스 할당** 해제 단추를 선택 합니다.

리소스 할당을 취소 하면 Azure에서 삭제 되지 않습니다. LUIS에서 연결 되지 않습니다.


## <a name="delete-an-account"></a>계정 삭제

계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 스토리지 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요.

## <a name="change-the-pricing-tier"></a>가격 책정 계층 변경

1.  [Azure Portal](https://portal.azure.com)에서 LUIS 구독을 찾아 선택 합니다.

    ![Azure Portal에서 LUIS 구독을 보여 주는 스크린샷](./media/luis-usage-tiers/find.png)
1.  **가격 책정 계층** 을 선택 하 여 사용 가능한 가격 책정 계층을 표시 합니다.

    ![가격 책정 계층 메뉴 항목을 보여 주는 스크린샷](./media/luis-usage-tiers/subscription.png)
1.  가격 책정 계층을 선택 하 고 **선택** 을 클릭 하 여 변경 내용을 저장 합니다.

    ![가격 책정 계층을 선택 하 고 저장 하는 방법을 보여 주는 스크린샷](./media/luis-usage-tiers/plans.png)

    가격 책정 변경이 완료 되 면 팝업 창에서 가격 책정 계층 업데이트를 확인 합니다.

    ![가격 책정 업데이트를 확인 하는 팝업 창의 스크린샷](./media/luis-usage-tiers/updated.png)
1. **게시** 페이지에서 [이 엔드포인트 키를 할당](#assign-a-resource-to-an-app)하고 모든 엔드포인트 쿼리에서 이 엔드포인트 키를 사용해야 합니다.

## <a name="view-azure-resource-metrics"></a>Azure 리소스 메트릭 보기

### <a name="view-a-summary-of-azure-resource-usage"></a>Azure 리소스 사용 요약 보기
Azure Portal에서 LUIS 사용 정보를 볼 수 있습니다. **개요** 페이지는 최근 호출 및 오류를 포함 하 여 요약을 표시 합니다. LUIS 끝점을 요청 하는 경우 변경 내용이 표시 될 때까지 최대 5 분이 허용 됩니다.

![개요 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure 리소스 사용량 차트 사용자 지정
**메트릭** 페이지에는 데이터에 대 한 보다 자세한 보기가 제공 됩니다.

![메트릭 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/metrics-default.png)

특정 기간 및 메트릭 유형에 대 한 메트릭 차트를 구성할 수 있습니다.

![사용자 지정 된 차트를 보여 주는 스크린샷](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
특정 트랜잭션 임계값 (예: 1만 트랜잭션)에 도달 하는 시기를 확인 하려는 경우 경고를 만들 수 있습니다.

![경고 규칙 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/alert-default.png)

특정 기간 동안 **총 호출** 메트릭에 대한 메트릭 경고를 추가합니다. 경고를 받을 모든 사람의 전자 메일 주소를 추가 합니다. 경고를 수신 해야 하는 모든 시스템에 대해 웹 후크를 추가 합니다. 경고가 트리거될 때 논리 앱을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* 버전을 사용 하 여 앱 수명 주기를 제어 하 [는 방법을](luis-how-to-manage-versions.md) 알아봅니다.
* 새 [제작 리소스로](luis-migration-authoring.md)마이그레이션합니다.
