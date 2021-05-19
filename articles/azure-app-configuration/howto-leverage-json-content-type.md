---
title: 키-값에 JSON 콘텐츠 형식 사용
titleSuffix: Azure App Configuration
description: 키-값에 JSON content-type을 사용하는 방법을 알아봅니다.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 2be560d16b133696c520b8872406ff6a36b0087b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137120"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>App Configuration에서 content-type을 활용하여 JSON 키-값 저장

데이터는 키-값으로 App Configuration에 저장됩니다. 여기서 값은 기본적으로 문자열 형식으로 처리됩니다. 하지만 각 키-값과 연결된 content-type 속성을 활용하면 사용자 지정 형식을 지정할 수 있습니다. 따라서 데이터의 원래 형식을 유지하거나 애플리케이션이 content-type에 따라 다르게 동작하도록 할 수 있습니다.


## <a name="overview"></a>개요

App Configuration에서 JSON 미디어 형식을 키-값의 content-type으로 사용하면 다음과 같은 이점을 얻을 수 있습니다.
- **간단한 데이터 관리**: Azure Portal에서 배열과 같은 키-값을 관리하기가 훨씬 더 쉬워집니다.
- **향상된 데이터 내보내기**: 데이터를 내보낼 때 기본 형식, 배열 및 JSON 개체가 보존됩니다.
- **App Configuration 공급자에서 기본 지원**: JSON content-type의 키-값은 애플리케이션에서 App Configuration 공급자 라이브러리가 사용할 때 정상적으로 작동합니다.

#### <a name="valid-json-content-type"></a>잘못된 JSON content-type

[여기](https://www.iana.org/assignments/media-types/media-types.xhtml)에 정의된 미디어 형식을 각 키-값과 연결된 content-type에 할당할 수 있습니다.
미디어 형식은 형식과 하위 형식으로 구성됩니다. 형식이 `application`이고 하위 형식(또는 접미사)이 `json`이면 미디어 형식이 유효한 JSON content-type으로 간주됩니다.
유효한 JSON content-type의 몇 가지 예는 다음과 같습니다.

- application/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>유효한 JSON 값

키-값에 JSON content-type이 있는 경우 해당 값이 유효한 JSON 형식이어야만 클라이언트에서 올바르게 처리 가능합니다. 그렇지 않으면 클라이언트가 실패하거나 대체하여 문자열 형식으로 처리할 수 있습니다.
유효한 JSON 값의 몇 가지 예는 다음과 같습니다.

- "John Doe"
- 723
- false
- null
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> 이 문서의 나머지 부분에서는 유효한 JSON content-type과 유효한 JSON 값을 포함하는 App Configuration의 키-값을 **JSON 키-값** 이라고 합니다. 

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * App Configuration에서 JSON 키-값을 만듭니다.
> * JSON 파일에서 JSON 키-값을 가져옵니다.
> * JSON 키-값을 JSON 파일로 내보냅니다.
> * 애플리케이션에서 JSON 키-값을 사용합니다.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 Azure CLI 버전 2.10.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>App Configuration에서 JSON 키-값 만들기

JSON 키-값은 Azure Portal 또는 Azure CLI를 사용하나 JSON 파일에서 가져와서 만들 수 있습니다. 이 섹션에서는 이 세 가지 방법을 모두 사용하여 동일한 JSON 키-값을 만드는 방법을 설명합니다.

### <a name="create-json-key-values-using-azure-portal"></a>Azure Portal을 사용하여 JSON 키-값 만들기

App Configuration 저장소로 이동하여 **구성 탐색기** > **만들기** > **키-값** 을 선택하여 다음 키-값 쌍을 추가합니다.

| 키 | 값 | 콘텐츠 유형 |
|---|---|---|
| Settings:BackgroundColor | "Green" | application/json |
| Settings:FontSize | 24 | application/json |
| Settings:UseDefaultRouting | false | application/json |
| Settings:BlockedUsers | null | application/json |
| Settings:ReleaseDate | "2020-08-04T12:34:56.789Z" | application/json |
| Settings:RolloutPercentage | [25,50,75,100] | application/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | application/json |

**레이블** 을 비워두고 **적용** 을 선택합니다.

### <a name="create-json-key-values-using-azure-cli"></a>Azure CLI를 사용하여 JSON key-value 만들기

다음 명령은 App Configuration 저장소에 JSON key-value를 만듭니다. `<appconfig_name>`을 App Configuration저장소의 이름으로 바꿉니다.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Azure CLI 또는 Azure Cloud Shell을 사용하여 JSON 키-값을 만드는 경우 제공되는 값은 이스케이프된 JSON 문자열이어야 합니다.

### <a name="import-json-key-values-from-a-file"></a>파일에서 JSON 키-값 가져오기

다음 콘텐츠를 사용하여 `Import.json`이라는 JSON 파일을 만들고 App Configuration에 키-값으로 가져옵니다.

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth` 인수는 파일의 계층적 데이터를 키-값 쌍으로 평면화하는 데 사용됩니다. 이 자습서에서는 App Configuration에서 JSON 개체를 값으로 저장할 수도 있음을 보여주기 위해 depth를 지정했습니다. depth를 지정하지 않으면 JSON 개체가 기본적으로 가장 깊은 수준으로 평면화됩니다.

사용자가 만든 JSON 키-값은 App Configuration에서 다음과 같이 표시됩니다.

![JSON 키-값이 포함된 Config 저장소](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>JSON 키-값을 파일로 내보내기

JSON 키-값을 사용할 때의 주요 이점 중 하나는 내보낼 때 값의 원래 데이터 형식을 유지할 수 있는 것입니다. App Configuration에서 JSON content-type이 아닌 키-값은 문자열로 처리됩니다. 

다음 키-값은 JSON content-type이 아닌 것으로 간주합니다.

| 키 | 값 | 콘텐츠 유형 |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

이러한 키-값을 JSON 파일로 내보내면 값이 문자열로 내보내집니다.
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

그러나 JSON 키-값을 파일로 내보낼 경우 모든 값이 원래 데이터 형식을 유지 합니다. 이를 확인하려면 App Configuration의 키-값을 JSON 파일로 내보냅니다. 내보낸 파일의 내용이 이전에 가져온 `Import.json` 파일과 같은 것을 볼 수 있습니다.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> App Configuration 저장소에 JSON content-type이 아닌 키-값이 있는 경우 이 키-값도 문자열 형식으로 동일한 파일로 내보내게 됩니다.


## <a name="consuming-json-key-values-in-applications"></a>애플리케이션에서 JSON 키-값 사용

애플리케이션에서 JSON 키-값을 사용하는 가장 쉬운 방법은 App Configuration 공급자 라이브러리를 사용하는 것입니다. 이 공급자 라이브러리를 사용하면 애플리케이션에서 JSON 키-값의 특수 처리를 구현할 필요가 없습니다. 애플리케이션의 기본 구성과 일치하도록 구문 분석되고 변환됩니다.

예를 들어 App Configuration에 다음 키-값이 있는 경우:

| 키 | 값 | 콘텐츠 유형 |
|---|---|---|
| 설정 | {"FontSize":24,"UseDefaultRouting":false} | application/json |

.NET 애플리케이션 구성에는 다음 키 값이 있습니다.

| 키 | 값 |
|---|---|
| Settings:FontSize | 24 |
| Settings:UseDefaultRouting | false |

새 키에 직접 액세스하거나 [구성 값을 .NET 개체의 인스턴스에 바인딩](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern)하도록 선택할 수 있습니다.


> [!Important]
> JSON 키-값에 대한 기본 지원은 .NET configuration provider 버전 4.0.0 이상에서 사용할 수 있습니다. 자세한 내용은 [*다음 단계*](#next-steps)를 참조하세요.

SDK 또는 REST API를 사용하여 content-type에 따라 App Configuration에서 키-값을 읽는 경우, 애플리케이션은 JSON 키-값의 값을 구문 분석해야 합니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이제 App Configuration 저장소에서 JSON 키-값을 사용하는 방법을 알아보았으므로 이러한 키-값을 사용하는 애플리케이션을 만들어 보세요.

* [ASP.NET Core 빠른 시작](./quickstart-aspnet-core-app.md)
    * 필수 구성 요소: [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) 패키지 v4.0.0 이상.

* [.NET Core 빠른 시작](./quickstart-dotnet-core-app.md)
    * 필수 구성 요소: [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) 패키지 v4.0.0 이상.
