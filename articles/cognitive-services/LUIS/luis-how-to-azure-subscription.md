---
title: 작성 및 런타임 키 사용 방법 - LUIS
titleSuffix: Azure Cognitive Services
description: 언어 이해(LUIS)를 처음 사용하는 경우 작성 키를 만들 필요가 없습니다. 앱을 게시하려는 경우 런타임 끝점을 사용하여 런타임 키를 만들고 앱에 할당해야 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221326"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>작성 및 런타임 리소스 키 사용

작성 및 런타임 리소스는 LUIS 앱 및 예측 끝점에 인증을 제공합니다.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

LUIS 포털에 로그인할 때 다음을 계속하도록 선택할 수 있습니다.

* 무료 [평가판 키](#trial-key) - 작성 및 몇 가지 예측 끝점 쿼리를 제공합니다.
* Azure [LUIS 작성](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 리소스입니다. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>LUIS 포털에 로그인하고 작성 시작

1. [LUIS 포털에](https://www.luis.ai) 로그인하고 이용 약관에 동의합니다.
1. 사용할 LUIS 작성 키 유형(무료 평가판 키 또는 새 Azure LUIS 작성 키)을 선택하여 LUIS 앱을 시작합니다. 

    ![Language Understanding 제작 리소스의 유형을 선택합니다.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 리소스 선택 프로세스가 완료되면 새 [앱을 만듭니다.](luis-how-to-start-new-app.md#create-new-app-in-luis) 

## <a name="trial-key"></a>평가판 키

평가판(스타터) 키가 제공됩니다. 한 달에 최대 1,000개의 쿼리를 예측 끝점 런타임을 쿼리하는 인증 키로 사용됩니다. 

LUIS 포털의 **사용자 설정** 페이지와 > Azure 리소스 **관리** 페이지에서 모두 볼 수 있습니다. 

예측 끝점을 게시할 준비가 되면 시작 키 기능을 대체하기 위해 작성 및 예측 런타임 키를 만들고 할당합니다. 

## <a name="create-resources-in-the-azure-portal"></a>Azure 포털에서 리소스 만들기

1. [이 링크를](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 사용하여 리소스 생성을 위해 Azure 포털을 엽니다.
1. 작성 및 예측 끝점 런타임 키를 만들려면 **둘 다를** 선택합니다. 
1. 리소스를 만드는 데 필요한 정보를 입력한 다음 **만들기를** 선택하여 프로세스를 완료합니다.

    ![언어 이해 리소스 만들기](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |이름|목적|
    |--|--|
    |리소스 이름| 작성 및 예측 끝점 쿼리에 대한 URL의 일부로 사용되는 사용자 지정 이름입니다.|
    |구독 이름| 리소스에 대해 청구되는 구독입니다.|
    |Resource group| 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 동일한 리전에서 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.|
    |작성 위치|모델과 연결된 영역입니다.|
    |작성 가격 책정 계층|가격 책정 계층은 초당 최대 트랜잭션과 월을 결정합니다.|
    |런타임 위치|게시된 예측 끝점 런타임과 연결된 지역입니다.|
    |런타임 가격 책정 계층|가격 책정 계층은 초당 최대 트랜잭션과 월을 결정합니다.|

    두 리소스가 모두 만들어지면 LUIS 포털의 리소스를 할당합니다.

## <a name="create-resources-in-azure-cli"></a>Azure CLI에서 리소스 만들기

Azure [CLI를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 사용하여 각 리소스를 개별적으로 만듭니다. 

자원 `kind`:

* 제작:`LUIS.Authoring`
* 예측:`LUIS` 

1. Azure CLI에 로그인합니다.

    ```azurecli
    az login
    ```

    이렇게 하면 올바른 계정을 선택하고 인증을 제공할 수 있는 브라우저가 열립니다.

1. `westus` 지역의 명명된 `my-resource-group` _기존_ 리소스 `LUIS.Authoring`그룹에 `my-luis-authoring-resource` 이름이 지정된 **종류의 LUIS 작성 리소스를**만듭니다. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. 지역에 대 한 명명 `my-resource-group` 된 `LUIS` _기존_ `my-luis-prediction-resource` 리소스 그룹에 명명 된 `westus` 종류의 LUIS 예측 **끝점 리소스를**만듭니다. 프리 티어보다 높은 처리량을 원할 `F0` `S0`경우 을 변경합니다. [가격 책정 계층 및 처리량에](luis-boundaries.md#key-limits)대해 자세히 알아보십시오.

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > 이 키는 Azure **리소스 관리의**LUIS 포털에 할당될 때까지 LUIS 포털에서 사용되지 > **않습니다.**

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>모든 앱에 대해 LUIS 포털에서 작성 리소스 할당

단일 앱 또는 LUIS의 모든 앱에 대한 작성 리소스를 할당할 수 있습니다. 다음 절차는 모든 앱을 단일 작성 리소스에 할당합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다.
1. 상단 탐색 모음에서 맨 오른쪽에 있는 사용자 계정을 선택한 다음 **설정을**선택합니다.
1. 사용자 **설정** 페이지에서 **작성 리소스 추가를** 선택한 다음 기존 작성 리소스를 선택합니다. **저장**을 선택합니다. 

## <a name="assign-a-resource-to-an-app"></a>앱에 리소스 할당

다음 프로시저를 사용하여 앱에 단일 리소스( 작성 또는 예측 끝점 런타임)를 할당할 수 있습니다.

1. [LUIS 포털에](https://www.luis.ai)로그인한 다음 **내 앱** 목록에서 앱을 선택합니다.
1. **> Azure 리소스 관리** 페이지로 이동합니다.

    ![LUIS 포털에서 > Azure 리소스 관리를 선택하여 앱에 리소스를 할당합니다.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. 예측 또는 작성 리소스 탭을 선택한 다음 **예측 리소스 추가** 또는 작성 리소스 추가 **단추를** 선택합니다. 
1. 양식의 필드를 선택하여 올바른 리소스를 찾은 다음 **저장을**선택합니다.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>LUIS 포털을 사용하지 않고 런타임 리소스 할당

CI/CD 파이프라인과 같은 자동화 를 위해 LUIS 런타임 리소스를 LUIS 앱에 할당하는 것을 자동화할 수 있습니다. 이렇게 하려면 다음 단계를 수행해야 합니다.

1. 이 [웹 사이트](https://resources.azure.com/api/token?plaintext=true)에서 Azure Resource Manager 토큰을 가져옵니다. 이 토큰은 만료되므로 즉시 사용합니다. 요청은 Azure Resource Manager 토큰을 반환합니다.

    ![Azure Resource Manager 토큰 요청 및 Azure Resource Manager 토큰 받기](./media/luis-manage-keys/get-arm-token.png)

1. 토큰을 사용하여 사용자 계정이 액세스할 수 있는 [Luis Azure 계정 API에서](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)구독 간에 LUIS 런타임 리소스를 요청합니다. 

    이 POST API에는 다음 설정이 필요합니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization`의 값은 `Bearer {token}`입니다. 토큰 값 앞에 단어 `Bearer`와 공백이 와야 합니다.| 
    |`Ocp-Apim-Subscription-Key`|작성 키|

    이 API는 계정 이름으로 반환된 리소스 이름, 리소스 그룹 및 구독 ID를 포함하여 LUIS 구독의 JSON 개체 배열을 반환합니다. LUIS 앱에 할당할 LUIS 리소스인 배열에서 항목 하나를 찾습니다. 

1. [애플리케이션에 LUIS azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API를 사용하여 LUIS 리소스에 토큰을 할당합니다. 

    이 POST API에는 다음 설정이 필요합니다.

    |Type|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization`의 값은 `Bearer {token}`입니다. 토큰 값 앞에 단어 `Bearer`와 공백이 와야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|작성 키|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID 
    |본문||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    이 API가 성공하면 201 - 생성된 상태를 반환합니다. 

## <a name="unassign-resource"></a>리소스 할당 해제

1. [LUIS 포털에](https://www.luis.ai)로그인한 다음 **내 앱** 목록에서 앱을 선택합니다.
1. **> Azure 리소스 관리** 페이지로 이동합니다.
1. 예측 또는 작성 리소스 탭을 선택한 다음 리소스에 대한 **리소스 할당 취소** 단추를 선택합니다. 

리소스 할당을 취소하면 Azure에서 리소스가 삭제되지 않습니다. LUIS에서만 할당 해제됩니다. 

## <a name="reset-authoring-key"></a>작성 키 다시 설정

**[리소스 마이그레이션된](luis-migration-authoring.md) 앱 작성의 경우:** 작성 키가 손상된 경우 해당 작성 리소스에 대한 **키** 페이지의 Azure Portal에서 키를 재설정합니다. 

**아직 마이그레이션되지 않은 앱의 경우**LUIS 포털의 모든 앱에서 키가 재설정됩니다. 작성 API를 통해 앱을 작성한 경우 Ocp-Apim-구독 키 값을 새 키로 변경해야 합니다.

## <a name="regenerate-azure-key"></a>Azure 키 재생성

**키** 페이지에서 Azure 포털에서 Azure 키를 다시 생성합니다.

## <a name="delete-account"></a>계정 삭제

계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 스토리지 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요.

## <a name="change-pricing-tier"></a>가격 책정 계층 변경

1.  [Azure](https://portal.azure.com)에서 LUIS 구독을 찾습니다. LUIS 구독을 선택합니다.
    ![LUIS 구독 찾기](./media/luis-usage-tiers/find.png)
1.  사용 가능한 가격 책정 계층을 보려면 **가격 책정 계층**을 선택합니다. 
    ![가격 책정 계층 보기](./media/luis-usage-tiers/subscription.png)
1.  가격 책정 계층을 선택하고 **선택**을 선택하여 변경 내용을 저장합니다. 
    ![LUIS 결제 계층 변경](./media/luis-usage-tiers/plans.png)
1.  가격 변경이 완료되면 팝업 창에서 새로운 가격 책정 계층을 확인합니다. 
    ![LUIS 결제 계층 확인](./media/luis-usage-tiers/updated.png)
1. **게시** 페이지에서 [이 엔드포인트 키를 할당](#assign-a-resource-to-an-app)하고 모든 엔드포인트 쿼리에서 이 엔드포인트 키를 사용해야 합니다. 

## <a name="viewing-azure-resource-metrics"></a>Azure 리소스 메트릭 보기

### <a name="viewing-azure-resource-summary-usage"></a>Azure 리소스 요약 사용량 보기
Azure에서 LUIS 사용량 정보를 볼 수 있습니다. **개요** 페이지에는 호출 및 오류를 포함한 최근 요약 정보가 표시됩니다. LUIS 엔드포인트 요청을 만들고 나서 **개요 페이지**를 즉시 확인할 경우 사용량이 표시되는 데 최대 5분이 걸릴 수 있습니다.

![요약 사용량 보기](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure 리소스 사용 차트 사용자 지정
메트릭은 데이터에 대한 더 자세한 보기를 제공합니다.

![기본 메트릭](./media/luis-usage-tiers/metrics-default.png)

기간 및 메트릭 유형에 대한 메트릭 차트를 구성할 수 있습니다. 

![사용자 지정 메트릭](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
언제 특정 트랜잭션 임계값(예: 10,000개의 트랜잭션)에 도달했는지 확인하려는 경우 경고를 만들 수 있습니다. 

![기본 경고](./media/luis-usage-tiers/alert-default.png)

특정 기간 동안 **총 호출** 메트릭에 대한 메트릭 경고를 추가합니다. 경고를 받아야 하는 모든 사람의 메일 주소를 추가합니다. 경고를 받아야 하는 모든 시스템의 웹후크를 추가합니다. 경고가 트리거될 때 논리 앱을 실행할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

* [버전을 사용하여](luis-how-to-manage-versions.md) 앱 수명 주기를 제어하는 방법을 알아보세요.
* 해당 [리소스의 작성 리소스](luis-concept-keys.md#authoring-key) 및 참여자 등의 개념을 [이해합니다.](luis-concept-keys.md#contributions-from-other-authors)
* 작성 및 런타임 [리소스를 만드는 방법](luis-how-to-azure-subscription.md) 알아보기
* 새 [작성 리소스로](luis-migration-authoring.md) 마이그레이션 
