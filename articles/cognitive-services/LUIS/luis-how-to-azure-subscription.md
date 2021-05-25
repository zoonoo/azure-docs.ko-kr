---
title: 작성 및 런타임 키 사용 - LUIS
description: LUIS를 처음 사용하는 경우 작성 키를 만들 필요가 없습니다. 앱을 게시한 다음, 런타임 엔드포인트를 사용하려면 런타임 키를 만들어 앱에 할당해야 합니다.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99988152"
---
# <a name="create-luis-resources"></a>LUIS 리소스 만들기

작성 및 쿼리 예측 런타임 리소스는 LUIS(Language Understanding) 앱 및 예측 엔드포인트에 대한 인증을 제공합니다.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS 리소스

LUIS는 세 가지 종류의 Azure 리소스와 하나의 비 Azure 리소스를 허용합니다.

|리소스|목적|`kind` 인지 서비스|`type` 인지 서비스|
|--|--|--|--|
|작성 리소스|애플리케이션을 만들고, 관리하고, 학습시키고, 테스트하고, 게시할 수 있습니다. LUIS 앱을 프로그래밍 방식으로 작성하거나 LUIS 포털에서 작성하려는 경우 [LUIS 작성 리소스를 만듭니다](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal). Azure 작성 리소스를 애플리케이션에 연결하려면 먼저 [LUIS 계정을 마이그레이션](luis-migration-authoring.md#what-is-migration)해야 합니다. 사용자에게 [기여자 역할](#contributions-from-other-authors)을 할당하여 작성 리소스에 대한 권한을 제어할 수 있습니다. <br><br> 다음 한 가지 계층을 LUIS 작성 리소스에 사용할 수 있습니다.<br> <ul> <li>**체험 F0 작성 리소스** - 매월 100만 개의 체험 작성 트랜잭션 및 1,000개의 체험 테스트 예측 엔드포인트 요청을 제공합니다. |`LUIS.Authoring`|`Cognitive Services`|
|예측 리소스| LUIS 애플리케이션을 게시한 후 예측 리소스/키를 사용하여 예측 엔드포인트 요청을 쿼리합니다. 클라이언트 앱이 작성 또는 시작 리소스에서 제공하는 1,000개의 요청보다 많은 예측을 요청하기 전에 LUIS 예측 리소스를 만듭니다. <br><br> 다음 두 가지 계층을 예측 리소스에 사용할 수 있습니다.<br><ul> <li> **체험 F0 예측 리소스** - 매월 10,000개의 체험 예측 엔드포인트 요청을 제공합니다.<br> <li> **표준 S0 예측 리소스** - 유료 계층입니다. [가격 책정에 대한 자세한 정보](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|시작/평가판 리소스|애플리케이션을 만들고, 관리하고, 학습시키고, 테스트하고, 게시할 수 있습니다. LUIS에 처음 로그인할 때 시작 리소스 옵션을 선택하면 이 리소스가 기본적으로 만들어집니다. 시작 키는 궁극적으로 더 이상 사용되지 않습니다. 모든 LUIS 사용자는 [자신의 계정을 마이그레이션](luis-migration-authoring.md#what-is-migration)하고 LUIS 애플리케이션을 작성 리소스에 연결해야 합니다. 작성 리소스와 달리 이 리소스는 Azure 역할 기반 액세스 제어 권한을 부여하지 않습니다. <br><br> 작성 리소스와 마찬가지로 시작 리소스는 100만 개의 체험 작성 트랜잭션 및 1,000개의 체험 테스트 예측 엔드포인트 요청을 제공합니다.|-|Azure 리소스입니다.|
|[Cognitive Services 다중 서비스 리소스 키](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS 및 기타 지원되는 인지 서비스와 공유되는 예측 엔드포인트 요청을 쿼리합니다.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS는 두 가지 종류의 F0(체험 계층) 리소스를 제공합니다. 하나는 작성 트랜잭션용이고, 다른 하나는 예측 트랜잭션용입니다. 예측 트랜잭션에 대한 체험 할당량이 부족한 경우 매월 1,000개의 예측 트랜잭션을 제공하는 작성 리소스가 아니라 매월 10,000개의 체험 트랜잭션을 제공하는 F0 예측 리소스를 사용하고 있는지 확인하세요.

Azure 리소스 만들기 프로세스가 완료되면 LUIS 포털에서 [ 리소스를 앱에 할당](#assign-a-resource-to-an-app)합니다.

> [!important]
> LUIS 앱은 게시 및 쿼리하려는 [지역](luis-reference-regions.md#publishing-regions)에 작성해야 합니다.

## <a name="resource-ownership"></a>리소스 소유권

LUIS 리소스와 같은 Azure 리소스는 리소스를 포함하는 구독에서 소유합니다.

리소스 소유권을 변경하려면 다음 작업 중 하나를 수행할 수 있습니다.
* 구독 [소유권](../../cost-management-billing/manage/billing-subscription-transfer.md)을 양도합니다.
* LUIS 앱을 파일로 내보낸 다음, 다른 구독에서 앱을 가져옵니다. 내보내기는 LUIS 포털의 **내 앱** 페이지에서 사용할 수 있습니다.

## <a name="resource-limits"></a>리소스 제한

### <a name="authoring-key-creation-limits"></a>작성 키 만들기 제한

구독당 지역별 최대 10개의 작성 키를 만들 수 있습니다. 게시 지역은 작성 지역과 다릅니다. 클라이언트 애플리케이션을 배치할 게시 지역에 해당하는 작성 지역에 앱을 만들어야 합니다. 작성 지역이 게시 지역에 매핑되는 방법에 대한 내용은 [작성 및 게시 지역](luis-reference-regions.md)을 참조하세요. 

키 제한에 대한 자세한 내용은 [키 제한](luis-limits.md#key-limits)을 참조하세요.

### <a name="errors-for-key-usage-limits"></a>키 사용량 제한 오류

사용량 제한은 가격 책정 계층을 기반으로 합니다.

TPS(초당 트랜잭션 수) 할당량을 초과하는 경우 HTTP 429 오류가 표시됩니다. TPM(월별 트랜잭션 수) 할당량을 초과하는 경우 HTTP 403 오류가 표시됩니다.


### <a name="reset-an-authoring-key"></a>작성 키 다시 설정

[마이그레이션된 작성 리소스](luis-migration-authoring.md) 앱의 경우: 작성 키가 손상되면 Azure Portal의 작성 리소스에 대한 **키** 페이지에서 키를 다시 설정합니다.

마이그레이션되지 않은 앱의 경우: LUIS 포털의 모든 앱에서 키를 다시 설정합니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다.

### <a name="regenerate-an-azure-key"></a>Azure 키 다시 생성

Azure 키는 Azure Portal의 **키** 페이지에서 다시 생성할 수 있습니다.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>앱 소유권, 액세스 및 보안

앱은 소유자의 구독에 따라 결정되는 Azure 리소스에서 정의됩니다.

LUIS 앱은 이동할 수 있습니다. 다음 리소스를 사용하면 Azure Portal 또는 Azure CLI를 통해 이 작업을 수행할 수 있습니다.

* [LUIS 작성 리소스 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [동일한 구독 내에서 또는 구독 간에 리소스 이동](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>다른 작성자의 기여

[마이그레이션된 작성 리소스](luis-migration-authoring.md) 앱의 경우: Azure Portal에서 **액세스 제어(IAM)** 페이지를 사용하여 작성 리소스에 대한 _기여자_ 를 관리할 수 있습니다. 협력자의 이메일 주소와 기여자 역할을 사용하여 [사용자를 추가하는 방법](luis-how-to-collaborate.md)에 대해 알아보세요.

아직 마이그레이션되지 않은 앱의 경우: LUIS 포털의 **관리 > 협력자** 페이지에서 모든 _협력자_ 를 관리할 수 있습니다.

### <a name="query-prediction-access-for-private-and-public-apps"></a>프라이빗 및 퍼블릭 앱에 대한 쿼리 예측 액세스

프라이빗 앱의 경우 소유자 및 기여자는 쿼리 예측 런타임 액세스를 사용할 수 있습니다. 퍼블릭 앱의 경우 고유한 Azure [Cognitive Service](../cognitive-services-apis-create-account.md) 또는 [LUIS](#create-resources-in-the-azure-portal) 런타임 리소스 및 퍼블릭 앱의 ID가 있는 사용자는 런타임 액세스를 사용할 수 있습니다.

현재 퍼블릭 앱의 카탈로그가 없습니다.

### <a name="authoring-permissions-and-access"></a>작성 권한 및 액세스
[LUIS](luis-reference-regions.md#luis-website) 포털 또는 [작성 API](https://go.microsoft.com/fwlink/?linkid=2092087)의 앱에 대한 액세스는 Azure 작성 리소스를 통해 제어됩니다.

소유자 및 모든 기여자에게는 앱을 작성할 수 있는 액세스 권한이 있습니다.

|작성 액세스에 포함되는 작업|참고|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|퍼블릭 앱인 경우 작성 또는 엔드포인트 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|학습|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>예측 엔드포인트 런타임 액세스

예측 엔드포인트 쿼리에 대한 액세스는 **애플리케이션 정보** 페이지의 **관리** 섹션에 있는 설정을 통해 제어됩니다.

|[프라이빗 엔드포인트](#runtime-security-for-private-apps)|[공용 엔드포인트](#runtime-security-for-public-apps)|
|:--|:--|
|소유자 및 기여자가 사용할 수 있음|소유자, 기여자 및 앱 ID를 알고 있는 모든 사용자가 사용할 수 있음|

서버 간 환경에서 LUIS 런타임 키를 호출하여 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우 봇과 LUIS 간의 연결이 이미 더 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [Azure AD](https://azure.microsoft.com/services/active-directory/)와 같은 것을 통해)를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고, 인증되고, 권한 부여가 확인되면 해당 호출을 LUIS에 전달합니다. 이 전략은 중간자(man-in-the-middle) 공격을 방지하지 않습니다. 그러나 사용자의 키 및 엔드포인트 URL을 난독 처리하고, 액세스를 추적할 수 있으며, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

### <a name="runtime-security-for-private-apps"></a>프라이빗 앱에 대한 런타임 보안

프라이빗 앱의 런타임은 다음 키에만 사용할 수 있습니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1,000회 엔드포인트 적중|
|협력자/기여자 작성 키| 최대 1,000회 엔드포인트 적중|
|작성자 또는 협력자/기여자가 LUIS에 할당한 모든 키|키 사용량 계층 기반|

### <a name="runtime-security-for-public-apps"></a>퍼블릭 앱에 대한 런타임 보안

퍼블릭으로 구성된 앱인 경우 키에서 전체 엔드포인트 할당량을 사용하지 않는 한 _모든_ 유효한 LUIS 작성 키 또는 LUIS 엔드포인트 키에서 이러한 앱을 쿼리할 수 있습니다.

소유자 또는 기여자가 아닌 사용자는 앱 ID가 지정된 경우에만 퍼블릭 앱의 런타임에 액세스할 수 있습니다. LUIS에는 공개 시장 또는 사용자가 퍼블릭 앱을 검색할 수 있는 다른 방법이 없습니다.

퍼블릭 앱은 모든 지역에 게시됩니다. 따라서 지역 기반 LUIS 리소스 키가 있는 사용자는 해당 리소스 키와 연결된 모든 지역에서 앱에 액세스할 수 있습니다.


### <a name="control-access-to-your-query-prediction-endpoint"></a>쿼리 예측 엔드포인트에 대한 액세스 제어

서버 간 환경에서 LUIS 예측 런타임 엔드포인트 키를 호출하여 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우 봇과 LUIS 간의 연결이 이미 더 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [Azure AD](https://azure.microsoft.com/services/active-directory/)와 같은 것을 통해)를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 중간자(man-in-the-middle) 공격을 방지하지 않습니다. 그러나 사용자의 엔드포인트를 난독 처리하고, 액세스를 추적할 수 있으며, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>LUIS 포털에 로그인 및 작성 시작

1. [LUIS 포털](https://www.luis.ai)에 로그인하고, 사용 약관에 동의합니다.
1. Azure LUIS 작성 키를 선택하여 LUIS 앱 작성을 시작합니다.

   ![시작 화면을 보여 주는 스크린샷](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 리소스 선택 프로세스가 완료되면 [새 앱](luis-how-to-start-new-app.md#create-new-app-in-luis)을 만듭니다.


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Azure CLI에서 리소스 만들기

[Azure CLI](/cli/azure/install-azure-cli)를 사용하여 각 리소스를 개별적으로 만듭니다.

`kind` 리소스:

* 작성: `LUIS.Authoring`
* 예측: `LUIS`

1. Azure CLI에 로그인합니다.

    ```azurecli
    az login
    ```

    이 명령은 올바른 계정을 선택하고 인증을 제공할 수 있도록 브라우저를 엽니다.

1. `LUIS.Authoring` 종류의 `my-luis-authoring-resource`라는 LUIS 작성 리소스를 만듭니다. 이 리소스는 `westus` 지역에 대한 `my-resource-group`이라는 _기존_ 리소스 그룹에 만들어집니다.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. `LUIS` 종류의 `my-luis-prediction-resource`라는 LUIS 예측 엔드포인트 리소스를 만듭니다. 이 리소스는 `westus` 지역에 대한 `my-resource-group`이라는 _기존_ 리소스 그룹에 만들어집니다. 체험 계층에서 제공하는 것보다 더 높은 처리량을 원하는 경우 `F0`을 `S0`으로 변경합니다. [가격 책정 계층 및 처리량에 대해 자세히 알아보세요.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > 이러한 키는 LUIS 포털의 **관리** > **Azure 리소스** 페이지에서 할당될 때까지 LUIS 포털에서 사용되지 않습니다.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>LUIS 포털에서 리소스 할당

단일 앱 또는 LUIS의 모든 앱에 대한 작성 리소스를 할당할 수 있습니다. 다음 절차에서는 모든 앱을 단일 작성 리소스에 할당합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다.
1. 오른쪽 위 모서리에서 사용자 계정을 선택한 다음, **설정** 을 선택합니다.
1. **사용자 설정** 페이지에서 **작성 리소스 추가** 를 선택한 다음, 기존 작성 리소스를 선택합니다. **저장** 을 선택합니다.

## <a name="assign-a-resource-to-an-app"></a>앱에 리소스 할당

>[!NOTE]
>Azure 구독이 없는 경우 새 리소스를 할당하거나 만들 수 없습니다. [Azure 체험 계정](https://azure.microsoft.com/en-us/free/)을 만든 다음, LUIS로 돌아가서 포털에서 새 리소스를 만들어야 합니다.

다음 절차를 사용하여 작성 또는 예측 리소스를 만들거나 하나를 애플리케이션에 할당할 수 있습니다. 

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다. **내 앱** 목록에서 앱을 선택합니다.
1. **관리** > **Azure 리소스** 로 차례로 이동합니다.

    ![Azure 리소스 페이지를 보여 주는 스크린샷](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. **예측 리소스** 또는 **작성 리소스** 탭에서 **예측 리소스 추가** 또는 **작성 리소스 추가** 단추를 선택합니다.
1. 양식의 필드를 사용하여 올바른 리소스를 찾은 다음, **저장** 을 선택합니다.
1. 기존 리소스가 없는 경우 창 아래쪽에서 **새 LUIS 리소스를 만드시겠습니까?** 를 선택하여 하나를 만들 수 있습니다.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>LUIS 포털을 사용하지 않고 쿼리 예측 런타임 리소스 할당

CI/CD 파이프라인과 같은 자동화된 프로세스의 경우 LUIS 앱에 대한 LUIS 런타임 리소스 할당을 자동화할 수 있습니다. 이렇게 하려면 다음 단계를 완료합니다.

1. [이 웹 사이트](https://resources.azure.com/api/token?plaintext=true)에서 Azure Resource Manager 토큰을 가져옵니다. 이 토큰은 곧 만료되므로 즉시 사용합니다. 요청은 Azure Resource Manager 토큰을 반환합니다.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Azure Resource Manager 토큰을 요청하는 웹 사이트를 보여 주는 스크린샷](./media/luis-manage-keys/get-arm-token.png)

1. 토큰을 사용하여 구독 간에 LUIS 런타임 리소스를 요청합니다. 사용자 계정에서 액세스할 수 있는 [LUIS Azure 계정 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)를 사용합니다.

    이 POST API에 필요한 값은 다음과 같습니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |`Ocp-Apim-Subscription-Key`|작성 키|

    API는 LUIS 구독을 나타내는 JSON 개체 배열을 반환합니다. 반환되는 값에는 `AccountName`으로 반환되는 구독 ID, 리소스 그룹 및 리소스 이름이 포함됩니다. LUIS 앱에 할당하려는 LUIS 리소스인 배열에서 해당 항목을 찾습니다.

1. [애플리케이션에 LUIS Azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API를 사용하여 토큰을 LUIS 리소스에 할당합니다.

    이 POST API에 필요한 값은 다음과 같습니다.

    |유형|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|작성 키|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID
    |본문||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    이 API가 성공하면 `201 - created status`를 반환합니다.

## <a name="unassign-a-resource"></a>리소스 할당 해제

1. [LUIS 포털](https://www.luis.ai)에 로그인한 다음, **내 앱** 목록에서 앱을 선택합니다.
1. **관리** > **Azure 리소스** 로 차례로 이동합니다.
1. **예측 리소스** 또는 **작성 리소스** 탭에서 리소스에 대한 **예측 리소스 할당 해제** 단추를 선택합니다.

리소스 할당을 해제하는 경우 해당 리소스는 Azure에서 삭제되지 않습니다. 다만 LUIS에서 연결이 해제될 뿐입니다.


## <a name="delete-an-account"></a>계정 삭제

계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 스토리지 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요.

## <a name="change-the-pricing-tier"></a>가격 책정 계층 변경

1.  [Azure Portal](https://portal.azure.com)에서 LUIS 구독을 찾아서 선택합니다.

    ![Azure Portal의 LUIS 구독을 보여 주는 스크린샷](./media/luis-usage-tiers/find.png)
1.  **가격 책정 계층** 을 선택하여 사용 가능한 가격 책정 계층을 확인합니다.

    ![가격 책정 계층 메뉴 항목을 보여 주는 스크린샷](./media/luis-usage-tiers/subscription.png)
1.  가격 책정 계층을 선택한 다음, **선택** 을 클릭하여 변경 내용을 저장합니다.

    ![가격 책정 계층을 선택하고 저장하는 방법을 보여 주는 스크린샷](./media/luis-usage-tiers/plans.png)

    가격 책정 변경이 완료되면 팝업 창에서 가격 책정 계층 업데이트를 확인합니다.

    ![가격 책정 업데이트를 확인하는 팝업 창의 스크린샷](./media/luis-usage-tiers/updated.png)
1. **게시** 페이지에서 [이 엔드포인트 키를 할당](#assign-a-resource-to-an-app)하고 모든 엔드포인트 쿼리에서 이 엔드포인트 키를 사용해야 합니다.

## <a name="view-azure-resource-metrics"></a>Azure 리소스 메트릭 보기

### <a name="view-a-summary-of-azure-resource-usage"></a>Azure 리소스 사용량 요약 보기
LUIS 사용량 정보는 Azure Portal에서 볼 수 있습니다. **개요** 페이지에는 최근 호출 및 오류를 포함한 요약이 표시됩니다. LUIS 엔드포인트를 요청하는 경우 변경 내용이 표시될 때까지 최대 5분 정도 기다립니다.

![개요 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure 리소스 사용량 차트 사용자 지정
**메트릭** 페이지는 데이터에 대한 자세한 보기를 제공합니다.

![메트릭 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/metrics-default.png)

특정 기간 및 메트릭 유형에 대한 메트릭 차트를 구성할 수 있습니다.

![사용자 지정된 차트를 보여 주는 스크린샷](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
특정 트랜잭션 임계값(예: 10,000개 트랜잭션)에 도달한 시기를 확인하려는 경우 경고를 만들 수 있습니다.

![경고 규칙 페이지를 보여 주는 스크린샷](./media/luis-usage-tiers/alert-default.png)

특정 기간 동안 **총 호출** 메트릭에 대한 메트릭 경고를 추가합니다. 경고를 받아야 하는 모든 사용자의 이메일 주소를 추가합니다. 경고를 받아야 하는 모든 시스템에 대한 웹후크를 추가합니다. 경고가 트리거될 때 논리 앱을 실행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [버전을 사용](luis-how-to-manage-versions.md)하여 앱 수명 주기를 제어하는 방법을 알아봅니다.
* 새 [작성 리소스](luis-migration-authoring.md)로 마이그레이션합니다.
