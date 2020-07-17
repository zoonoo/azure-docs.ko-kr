---
title: CLI를 사용하여 Internet Analyzer 테스트 만들기 | Microsoft Docs
description: 이 문서에서는 첫 Internet Analyzer 테스트를 만드는 방법을 알아봅니다.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184275"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>CLI를 사용하여 Internet Analyzer 테스트 만들기(미리 보기)

Internet Analyzer 리소스는 두 가지 방식, 즉 [Azure Portal](internet-analyzer-create-test-portal.md) 또는 CLI를 사용하여 만들 수 있습니다. 이 섹션을 참조하면 CLI 환경을 사용하여 새 Azure Internet Analyzer 리소스를 쉽게 만들 수 있습니다. 


> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

공개 미리 보기는 전 세계에서 사용할 수 있습니다. 하지만 데이터 스토리지는 미리 보기 기간 동안 *미국 서부 2*로 제한됩니다.

## <a name="object-model"></a>개체 모델
Internet Analyzer CLI는 다음 유형의 리소스를 표시합니다.
* **테스트** - 테스트가 시간 경과에 따른 두 인터넷 엔드포인트(A 및 B)의 최종 사용자 성능을 비교합니다.
* **프로필** - 테스트가 Internet Analyzer 프로필 아래에 만들어집니다. 프로필을 사용하면 관련 테스트를 그룹화할 수 있습니다. 단일 프로필에는 하나 이상의 테스트가 포함될 수 있습니다.
* **미리 구성된 엔드포인트** - 엔드포인트가 다양한 구성(지역, 가속 기술 등)을 통해 미리 설정되었습니다. 이러한 미리 구성된 엔드포인트를 테스트에 사용할 수 있습니다.
* **성과 기록표** - 성과 기록표는 빠르고 의미 있는 측정 결과 요약을 제공합니다. [성과 기록표 해석](internet-analyzer-scorecard.md)을 참조하세요.
* **시계열** - 시계열은 시간 경과에 따른 메트릭의 변화를 보여줍니다.

## <a name="profile-and-test-creation"></a>프로필 및 테스트 만들기
1. [Azure Internet Analyzer FAQ](internet-analyzer-faq.md)의 **미리 보기에 참여하는 방법** 지침에 따라 Internet Analyzer 미리 보기에 대한 액세스 권한을 얻으세요.
2. [Azure CLI를 설치합니다](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. `login` 명령을 실행하여 CLI 세션을 시작합니다.
    ```azurecli-interactive
    az login
    ```

    CLI는 기본 브라우저를 열 수 있으면 기본 브라우저를 열고 Azure 로그인 페이지를 로드합니다.
    그렇지 않으면 https://aka.ms/devicelogin 에서 브라우저 페이지를 열고 터미널에 표시된 권한 부여 코드를 입력합니다.

4. 브라우저에서 계정 자격 증명으로 로그인합니다.

5. Internet Analyzer 공개 미리 보기에 대한 액세스 권한을 부여받은 구독 ID를 선택합니다.

    로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 로그인한 후 현재 활성화된 구독입니다. 다른 구독을 선택하려면 전환할 구독 ID와 함께 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 명령을 사용합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)을 참조하세요.

    비 대화형으로 로그인할 수 있는 방법도 여러 가지가 있으며, 이는 [Azure CLI에 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)에서 자세히 설명됩니다.

6. **[선택 사항]** 새 Azure 리소스 그룹을 만듭니다.
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Azure CLI Internet Analyzer 확장을 설치합니다.
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. 새 Internet Analyzer 프로필을 만듭니다.
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. 이용 가능한 미리 구성된 모든 엔드포인트를 새로 만든 프로필에 나열합니다.
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. 새로 만든 Internet Analyzer 프로필에 아래에 새 테스트를 만듭니다.
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    위의 명령은 `www.contoso.com`과 `www.microsoft.com`이 모두 사용자 지정 경로에서 1픽셀 이미지([trans.gif](https://fpc.msedge.net/apc/trans.gif))를 호스팅한다고 가정합니다. 개체 경로를 지정하지 않으면 Internet Analyzer는 `/apc/trans.gif`를 기본 개체 경로로 사용합니다. 이 경우 미리 구성된 엔드포인트가 1픽셀 이미지를 호스트합니다. 또한 스키마(https/http)는 지정할 필요가 없습니다. Internet Analyzer는 HTTPS 엔드포인트만 지원하므로 스키마가 HTTPS라고 간주합니다.

11. 새 테스트는 Internet Analyzer 프로필 아래에 나타납니다.
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    예제 출력:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. 측정 생성을 시작하려면 테스트의 **scriptFileUri**가 가리키는 JavaScript 파일이 웹 애플리케이션에 포함되어야 합니다. 구체적인 지침은 [Internet Analyzer 클라이언트 포함](internet-analyzer-embed-client.md) 페이지에서 찾을 수 있습니다.

13. "상태" 값을 추적하여 테스트 진행률을 모니터링할 수 있습니다.
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. 시계열 또는 성과 기록표를 생성하여 테스트의 수집 결과를 검사할 수 있습니다.
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>다음 단계

* 지원되는 명령 및 사용 예제의 전체 목록을 보려면 [Internet Analyzer CLI 참조](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest)를 찾아봅니다.
* [인터넷 분석기 FAQ](internet-analyzer-faq.md)를 읽어보세요.
* [Internet Analyzer 클라이언트](internet-analyzer-embed-client.md)를 포함하고 [사용자 지정 엔드포인트](internet-analyzer-custom-endpoint.md)를 만드는 방법에 대해 자세히 알아보세요. 
