---
title: 키-값에 JSON 콘텐츠 형식 사용
titleSuffix: Azure App Configuration
description: 키-값에 대해 JSON content-type을 사용 하는 방법을 알아봅니다.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: ee262c0eb2431085e71d8ee0035bcdab9833d1cf
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565775"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>콘텐츠 형식을 활용 하 여 앱 구성에 JSON 키-값 저장

데이터는 키 값으로 앱 구성에 저장 됩니다. 여기서 값은 기본적으로 문자열 형식으로 처리 됩니다. 그러나 각 키 값과 연결 된 content-type 속성을 활용 하 여 사용자 지정 형식을 지정할 수 있습니다. 따라서 데이터의 원래 형식을 유지 하거나 응용 프로그램이 콘텐츠 형식에 따라 다르게 동작 하도록 할 수 있습니다.


## <a name="overview"></a>개요

앱 구성에서 JSON 미디어 형식을 키-값의 콘텐츠 형식으로 사용 하 여 다음과 같은 이점을 얻을 수 있습니다.
- **간단한 데이터 관리** : 배열과 같은 키 값을 관리 하는 것이 Azure Portal에서 훨씬 더 쉬워집니다.
- **향상 된 데이터 내보내기** : 기본 형식, 배열 및 JSON 개체는 데이터를 내보내는 동안 보존 됩니다.
- **앱 구성 공급자의 기본 지원** : JSON 콘텐츠가 있는 키-값은 응용 프로그램의 앱 구성 공급자 라이브러리에서 사용 하는 경우 정상적으로 작동 합니다.

#### <a name="valid-json-content-type"></a>유효한 JSON 콘텐츠 형식

[여기](https://www.iana.org/assignments/media-types/media-types.xhtml)에 정의 된 미디어 유형을 각 키 값과 연결 된 content-type에 할당할 수 있습니다.
미디어 유형은 형식 및 하위 형식으로 구성 됩니다. 형식이이 `application` 고 하위 형식 (또는 접미사)이 이면 `json` 미디어 형식이 유효한 JSON 콘텐츠 형식으로 간주 됩니다.
유효한 JSON 콘텐츠 형식에 대 한 몇 가지 예는 다음과 같습니다.

- application/json
- 응용 프로그램/작업 + json
- application/vnd. foobar + json; charset = utf-8

#### <a name="valid-json-values"></a>유효한 JSON 값

키-값에 JSON content-type이 있는 경우 해당 값은 클라이언트에서 올바르게 처리 하기 위해 유효한 JSON 형식 이어야 합니다. 그렇지 않으면 클라이언트는 실패 하거나 대체 하 여 문자열 형식으로 처리할 수 있습니다.
유효한 JSON 값의 몇 가지 예는 다음과 같습니다.

- "John Doe"
- 723
- false
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"대상 지정": {"Default": true, "Level": "Information"}}}

> [!NOTE]
> 이 문서의 나머지 부분에서는 유효한 JSON content-type 및 유효한 JSON 값을 포함 하는 앱 구성의 키-값을 **json 키-값** 이라고 합니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.
> [!div class="checklist"]
> * 앱 구성에서 JSON 키-값을 만듭니다.
> * Json 파일에서 JSON 키-값을 가져옵니다.
> * Json 키 값을 JSON 파일로 내보냅니다.
> * 응용 프로그램에서 JSON 키 값을 사용 합니다.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 자습서에는 버전 2.10.0 이상의 Azure CLI 필요 합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>앱 구성에서 JSON 키-값 만들기

JSON 키-값은 Azure Portal Azure CLI 사용 하거나 JSON 파일에서 가져와서 만들 수 있습니다. 이 섹션에서는 세 가지 방법을 모두 사용 하 여 동일한 JSON 키 값을 만드는 방법에 대 한 지침을 찾을 수 있습니다.

### <a name="create-json-key-values-using-azure-portal"></a>Azure Portal를 사용 하 여 JSON 키-값 만들기

앱 구성 저장소로 이동 하 여 **구성 탐색기**  >  **Create**  >  **키-값** 만들기를 선택 하 여 다음 키-값 쌍을 추가 합니다.

| 키 | 값 | 콘텐츠 유형 |
|---|---|---|
| 설정: BackgroundColor | 녹색 | application/json |
| 설정: FontSize | 24 | application/json |
| 설정: UseDefaultRouting | false | application/json |
| 설정: BlockedUsers | null | application/json |
| 설정: ReleaseDate | "2020-08-04T12:34:56.789 Z" | application/json |
| 설정: RolloutPercentage | [25, 50, 75100] | application/json |
| 설정: 로깅 | {"Test": {"Level": "Debug"}, "Prod": {"Level": "Warning"}} | application/json |

**레이블** 을 비워 두고 **적용** 을 선택 합니다.

### <a name="create-json-key-values-using-azure-cli"></a>Azure CLI를 사용 하 여 JSON 키-값 만들기

다음 명령은 앱 구성 저장소에서 JSON 키 값을 만듭니다. `<appconfig_name>`을 앱 구성 저장소의 이름으로 바꿉니다.

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
> Azure CLI 또는 Azure Cloud Shell를 사용 하 여 JSON 키 값을 만드는 경우 제공 된 값은 이스케이프 된 JSON 문자열 이어야 합니다.

### <a name="import-json-key-values-from-a-file"></a>파일에서 JSON 키-값 가져오기

다음 콘텐츠를 사용 하 여 이라는 JSON 파일을 만들고 `Import.json` 앱 구성에 키 값으로 가져옵니다.

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
> `--depth`인수는 파일의 계층적 데이터를 키-값 쌍으로 평면화 하는 데 사용 됩니다. 이 자습서에서는 앱 구성에서 JSON 개체를 값으로 저장할 수도 있음을 보여 주는 깊이가 지정 되었습니다. Depth를 지정 하지 않으면 JSON 개체는 기본적으로 가장 깊은 수준으로 평면화 됩니다.

사용자가 만든 JSON 키 값은 앱 구성에서 다음과 같이 표시 됩니다.

![JSON 키 값이 포함 된 구성 저장소](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>JSON 키-값을 파일로 내보내기

JSON 키 값을 사용 하는 경우의 주요 이점 중 하나는 내보내는 동안 값의 원래 데이터 형식을 유지 하는 기능입니다. 앱 구성의 키 값에 JSON 콘텐츠 형식이 없는 경우 해당 값은 문자열로 처리 됩니다. 

JSON 콘텐츠가 없는 키-값을 고려 합니다.

| 키 | 값 | 콘텐츠 유형 |
|---|---|---|
| 설정: FontSize | 24 | |
| 설정: UseDefaultRouting | false | |

이러한 키 값을 JSON 파일로 내보내면 값이 문자열로 내보내집니다.
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

그러나 JSON 키 값을 파일로 내보낼 경우 모든 값이 원래 데이터 형식을 유지 합니다. 이를 확인 하려면 앱 구성의 키-값을 JSON 파일로 내보냅니다. 내보낸 파일의 내용이 `Import.json` 이전에 가져온 파일과 동일 하다는 것을 볼 수 있습니다.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> 앱 구성 저장소에 JSON 콘텐츠 형식이 없는 키 값이 있는 경우에는 문자열 형식의 동일한 파일로도 내보내집니다. JSON 키 값만 내보내려면 JSON 키 값에 고유 레이블 또는 접두사를 할당 하 고 내보내기 중에 레이블 또는 접두사 필터링을 사용 합니다.


## <a name="consuming-json-key-values-in-applications"></a>응용 프로그램에서 JSON 키-값 사용

응용 프로그램에서 JSON 키 값을 사용 하는 가장 쉬운 방법은 앱 구성 공급자 라이브러리를 사용 하는 것입니다. 공급자 라이브러리를 사용 하면 응용 프로그램에서 JSON 키 값의 특수 처리를 구현할 필요가 없습니다. 다른 JSON 구성 공급자 라이브러리와 동일한 방식으로 응용 프로그램에 대해 항상 deserialize 됩니다. 

> [!Important]
> JSON 키 값에 대 한 기본 지원은 .NET configuration provider 버전 4.0.0 이상에서 사용할 수 있습니다. 자세한 내용은 [*다음 단계*](#next-steps) 섹션을 참조 하세요.

SDK 또는 REST API를 사용 하 여 콘텐츠 형식에 따라 앱 구성에서 키-값을 읽는 경우 응용 프로그램은 표준 JSON 역직렬 변환기를 사용 하 여 JSON 키 값의 값을 deserialize 해야 합니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이제 앱 구성 저장소에서 JSON 키 값을 사용 하는 방법을 배웠으므로 이러한 키 값을 사용 하기 위한 응용 프로그램을 만듭니다.

* [ASP.NET Core 빠른 시작](./quickstart-aspnet-core-app.md)
    * 필수 구성 요소: [AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) 패키지 v 4.0.0 이상.

* [.NET Core 빠른 시작](./quickstart-dotnet-core-app.md)
    * 필수 구성 요소: [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) 패키지 v 4.0.0 이상
