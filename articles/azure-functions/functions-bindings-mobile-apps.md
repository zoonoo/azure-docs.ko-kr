---
title: Azure Functions 모바일 앱 바인딩 | Microsoft Docs
description: Azure Functions에서 Azure 모바일 앱 바인딩을 사용하는 방법을 파악합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/30/2016
ms.author: glenga

---
# Azure Functions 모바일 앱 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure 모바일 앱 바인딩을 구성하고 코딩하는 방법을 설명합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure 앱 서비스 모바일 앱을 사용하면 테이블 끝점 데이터를 모바일 클라이언트에 노출할 수 있습니다. 이 동일한 테이블 형식 데이터는 Azure Functions에서 입력 및 출력 바인딩 모두에 사용할 수 있습니다. 동적 스키마를 지원하기 때문에, Node.js 백 엔드 모바일 앱은 함수에 사용할 테이블 형식 데이터를 노출하는데 이상적입니다. 동적 스키마를 기본적으로 사용하며 프로덕션 모바일 앱에서 사용하지 않아야 합니다. Node.js 백 엔드의 테이블 끝점에 대한 자세한 내용은 [개요: 테이블 작업](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)을 참조하세요. 모바일 앱에서, Node.js 백 엔드는 포털 내 테이블 탐색 및 편집을 지원합니다. 자세한 내용은 Node.js SDK 항목에서 [in-portal editing](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)(포털 내 편집)을 참조하세요. Azure Functions에서 .NET 백 엔드 모바일 앱을 사용하는 경우에는, 함수의 요구에 따라 데이터 모델을 수동으로 업데이트해야 합니다. .NET 백 엔드 모바일 앱의 테이블 끝점에 대한 자세한 내용은 .NET 백 엔드 SDK 항목에서 [방법: 테이블 컨트롤러 정의](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)를 참조하세요.

## 모바일 앱 백 엔드 URL에 대한 환경 변수를 만듭니다.
모바일 앱 바인딩을 위해서는 모바일 앱 백 엔드 자체의 URL을 반환하는 환경 변수를 만들어야 합니다. 이 URL은 모바일 앱을 찾아 블레이드를 연 후에 [Azure 포털](https://portal.azure.com)에서 찾을 수 있습니다.

![Azure 포털에서의 모바일 앱 블레이드](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

함수 앱에서 이 URL을 환경 변수로 설정하려면

1. [Azure Functions 포털](https://functions.azure.com/signin)의 함수 앱에서 **함수 앱 설정** > **앱 서비스 설정으로 이동**을 클릭합니다.
   
    ![함수 앱 설정 블레이드](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)
2. 함수 앱에서 **모든 설정**을 클릭하고 **응용 프로그램 설정** 아래로 스크롤한 후 **앱 설정**에서 환경 변수의 새 **이름**을 입력하고 URL을 **값**에 붙여 넣은 후 HTTPS 스키마를 사용하는지 확인하고 **저장**을 클릭한 다음 함수 앱 블레이드를 닫아 함수 포털로 돌아갑니다.
   
    ![앱 설정 환경 변수 추가](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)

이제 바인딩에서 이 새 환경 변수를 *연결* 필드로 설정할 수 있습니다.

## <a id="mobiletablesapikey"></a> API 키를 사용하여 모바일 앱 테이블 끝점에 대한 액세스 보호.
Azure Functions에서, 모바일 테이블 바인딩을 사용하면 API 키를 지정할 수 있으며 이는 함수 이외의 앱에서 원치 않는 액세스를 방지하는 데 사용될 수 있는 공유 암호입니다. 모바일 앱은 API 키 인증에 기본으로 제공되는 지원이 없습니다. 그러나 [API 키를 구현하는 Azure 앱 서비스 모바일 앱 백 엔드](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)의 예제를 수행하여 Node.js 백 엔드 모바일 앱에 API 키를 구현할 수 있습니다. [.NET 백 엔드 모바일 앱](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)에서 API 키를 유사하게 구현할 수 있습니다.

> [!IMPORTANT]
> 이 API 키는 모바일 앱 클라이언트와 함께 배포해서는 안되며 안전하게 Azure Functions와 같은 서비스 측 클라이언트에만 배포되어야 합니다.
> 
> 

## <a id="mobiletablesinput"></a> Azure 모바일 앱 입력 바인딩
입력 바인딩은 모바일 테이블 끝점에서 레코드를 로드하고 바인딩에 직접 전달할 수 있습니다. 함수를 호출한 트리거에 따라 레코드 ID가 결정됩니다. C# 함수에서 함수가 성공적으로 종료될 경우 레코드에 변경한 내용을 자동으로 다시 테이블에 전송합니다.

#### 모바일 앱 입력 바인딩에 대한 function.json
*function.json* 파일은 다음 속성을 지원합니다.

* `name`: 새 레코드에 대한 함수 코드에 사용되는 변수 이름입니다.
* `type`: 바인딩 유형은 *mobileTable*로 설정해야 합니다.
* `tableName`: 새 레코드가 생성될 테이블입니다.
* `id`: 검색할 레코드의 ID입니다. 이 속성은 `{queueTrigger}`와 유사한 바인딩을 지원하며 이는 큐 메시지의 문자열 값을 레코드 ID로 사용합니다.
* `apiKey`: 모바일 앱에 대한 선택적 API 키를 지정하는 응용 프로그램 설정인 문자열입니다. 모바일 앱이 API 키를 사용하여 클라이언트 액세스를 제한하는 경우 필요합니다.
* `connection`: 응용 프로그램 설정에서 모바일 앱 백 엔드의 URL을 지정하는 환경 변수 이름을 나타내는 문자열입니다.
* `direction`: 바인딩 방향이며 *in*으로 설정해야 합니다.

예제 *function.json* 파일:

    {
      "bindings": [
        {
          "name": "record",
          "type": "mobileTable",
          "tableName": "MyTable",
          "id" : "{queueTrigger}",
          "connection": "My_MobileApp_Url",
          "apiKey": "My_MobileApp_Key",
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### C# 큐 트리거에 대한 Azure 모바일 앱 코드 예제
위의 function.json 예제에 따라 입력 바인딩은 큐 메시지 문자열과 일치하는 ID로 모바일 앱 테이블 끝점으로부터 레코드를 검색하고 이를 *record* 매개 변수에 전달합니다. 레코드가 없는 경우 매개 변수는 null입니다. 레코드는 함수가 종료될 때 새 *Text* 값으로 업데이트됩니다.

    #r "Newtonsoft.Json"    
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, JObject record)
    {
        if (record != null)
        {
            record["Text"] = "This has changed.";
        }    
    }

#### Node.js 큐 트리거에 대한 Azure 모바일 앱 코드 예제
위의 function.json 예제에 따라 입력 바인딩은 큐 메시지 문자열과 일치하는 ID로 모바일 앱 테이블 끝점으로부터 레코드를 검색하고 이를 *record* 매개 변수에 전달합니다. Node.js 함수에서 업데이트된 레코드를 테이블에 다시 전송하지 않습니다. 이 코드 예제는 검색된 레코드를 로그에 작성합니다.

    module.exports = function (context, input) {    
        context.log(context.bindings.record);
        context.done();
    };


## <a id="mobiletablesoutput"></a>Azure 모바일 앱 출력 바인딩
함수는 출력 바인딩을 사용하여 모바일 앱 테이블 끝점에 레코드를 작성할 수 있습니다.

#### 모바일 앱 출력 바인딩에 대한 function.json
function.json 파일은 다음 속성을 지원합니다.

* `name`: 새 레코드에 대한 함수 코드에 사용되는 변수 이름입니다.
* `type`: *mobileTable*로 설정해야 하는 바인딩 유형입니다.
* `tableName`: 새 레코드가 생성되는 테이블입니다.
* `apiKey`: 모바일 앱에 대한 선택적 API 키를 지정하는 응용 프로그램 설정인 문자열입니다. 모바일 앱이 API 키를 사용하여 클라이언트 액세스를 제한하는 경우 필요합니다.
* `connection`: 응용 프로그램 설정에서 모바일 앱 백 엔드의 URL을 지정하는 환경 변수 이름을 나타내는 문자열입니다.
* `direction`: 바인딩 방향이며 *out*으로 설정해야 합니다.

예제 function.json:

    {
      "bindings": [
        {
          "name": "record",
          "type": "mobileTable",
          "tableName": "MyTable",
          "connection": "My_MobileApp_Url",
          "apiKey": "My_MobileApp_Key",
          "direction": "out"
        }
      ],
      "disabled": false
    }

#### C# 큐 트리거에 대한 Azure 모바일 앱 코드 예제
이 C# 코드 예제는 새 레코드를 모바일 앱 테이블 끝점에 삽입하고 *Text* 속성을 위의 바인딩에 지정된 테이블에 삽입합니다.

    public static void Run(string myQueueItem, out object record)
    {
        record = new {
            Text = $"I'm running in a C# function! {myQueueItem}"
        };
    }

#### Node.js 큐 트리거에 대한 Azure 모바일 앱 코드 예제
이 Node.js 코드 예제는 새 레코드를 모바일 앱 테이블 끝점에 삽입하고 *Text* 속성을 위의 바인딩에 지정된 테이블에 삽입합니다.

    module.exports = function (context, input) {

        context.bindings.record = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   

        context.done();
    };

## 다음 단계
[!INCLUDE [다음 단계](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0907_2016-->