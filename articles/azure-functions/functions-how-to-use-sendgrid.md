---
title: "Azure Functions에서 SendGrid를 사용하는 방법 | Microsoft Docs"
description: "Azure Functions에서 SendGrid를 사용하는 방법을 보여 줍니다."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: glenga
ms.openlocfilehash: 444e06ff24ea7f909a24d482aba26f890040d980
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Azure Functions에서 SendGrid를 사용하는 방법

## <a name="sendgrid-overview"></a>SendGrid 개요

Azure Functions는 함수에서 몇 줄의 코드와 SendGrid 계정으로 전자 메일 메시지를 보낼 수 있도록 SendGrid 출력 바인딩을 지원합니다.

Azure Function의 SendGrid API를 사용하려면 [SendGrid 계정](http://SendGrid.com)이 있어야 합니다. 또한 SendGrid API 키가 있어야 합니다. SendGrid 계정에 로그인하고 **설정**을 클릭한 다음 **API 키**를 클릭하여 API 키를 생성합니다. 이후 단계에서 사용되므로 이 키를 사용 가능한 상태로 유지합니다.

이제 Azure Function 앱을 만들 준비가 되었습니다.

## <a name="create-an-azure-function-app"></a>Azure Function 앱 만들기 

Azure Function Appls는 하나 이상의 Azure Functions에 대한 컨테이너입니다. Azure Functions는 함수일 뿐입니다. 각 Azure Function은 함수 실행을 야기하는 이벤트에 해당하는 하나의 트리거에 연결됩니다.
각 함수는 임의 개수의 입력 또는 출력 바인딩을 포함할 수 있습니다. 바인딩은 함수에서 사용할 수 있는 서비스입니다. SendGrid는 전자 메일을 보내는 데 사용할 수 있는 출력 바인딩입니다. 

1. Azure Portal에 로그인하고 [Azure Function 앱을 만들거나](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) 기존 함수 앱을 엽니다. 
2. Azure Function을 만듭니다. 간단한 작업을 위해 수동 트리거 및 C#을 선택합니다. 

 ![Azure Function 만들기](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Azure Function 앱에서 사용할 SendGrid 구성

함수에서 사용할 수 있게 SendGrid API 키를 앱 설정으로 저장해야 합니다. ApiKey 필드는 실제 SendGrid API 키가 아니지만 정의하는 앱 설정은 실제 API 키를 나타냅니다. 이러한 방식으로 키를 저장하면 소스 코드 제어에 체크인될 수 있는 코드 또는 파일에서 분리되므로 보안에 도움이 됩니다.

- 함수 앱의 **응용 프로그램 설정**에서 **AppSettings**를 만듭니다.

 ![Azure Function 만들기](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>SendGrid 출력 바인딩 구성

SendGrid는 Azure Function 출력 바인딩으로 사용할 수 있습니다. SendGrid 출력 바인딩을 만들려면

1. Azure Portal에서 함수의 **통합** 탭으로 이동합니다.
2. **새 출력**을 클릭하여 SendGrid 출력 바인딩을 만듭니다.
3. **API 키** 및 **메시지 매개 변수 이름** 속성을 입력합니다. 원할 경우 지금 다른 속성을 입력하거나 코드를 대신 작성할 수 있습니다. 이러한 설정은 기본값으로 사용할 수 있습니다.

 ![SendGrid 출력 바인딩 구성](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

함수에 바인딩을 추가하면 함수 폴더에 **function.json**이라는 파일이 만들어집니다. 이 파일에는 Azure Function의 **통합** 탭에서 볼 수 있는 것과 동일한 모든 정보가 Json 형식으로 포함되어 있습니다. **ApiKey**, **메시지** 및 **보낸 사람** 필드를 설정하면 **function.json** 파일에 다음 항목이 만들어집니다. 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

원할 경우 이 파일을 직접 수정할 수 있습니다.

이제 함수 앱 및 함수를 만들고 구성했으므로 전자 메일을 보내는 코드를 작성할 수 있습니다.

## <a name="write-code-that-creates-and-sends-email"></a>전자 메일을 만들어 보내는 코드 작성

SendGrid API에는 전자 메일을 만들어 보내는 데 필요한 모든 명령이 포함되어 있습니다.  

- 함수의 코드를 다음 코드로 바꿉니다.

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

첫 번째 줄에는 SendGrid 어셈블리를 참조하는 ```#r``` 지시문이 포함되어 있습니다. 그 후에는 ```using``` 문을 사용하여 해당 네임스페이스의 개체에 쉽게 액세스할 수 있습니다. 코드에서 SendGrid API로부터 전자 메일을 구성하는 ```Mail```, ```Personalization``` 및 ```Content``` 개체 인스턴스를 만듭니다. 메시지를 반환하는 경우 SendGrid가 전달합니다. 

함수의 서명에도 ```message```라는 ```Mail``` 형식의 추가 출력 매개 변수가 포함됩니다. 입력 및 출력 바인딩 모두 코드에서 함수 매개 변수를 나타냅니다. 

2. **테스트**를 클릭하고 메시지를 **요청 본문** 필드에 입력한 후 **실행** 단추를 클릭하여 코드를 테스트합니다.

 ![코드 테스트](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. 전자 메일을 검토하여 SendGrid에서 전자 메일을 보냈는지 확인합니다. 1단계의 코드에서 주소로 이동한 후 **요청 본문**의 메시지를 포함합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 SendGrid 서비스를 사용하여 전자 메일을 만들고 전송하는 방법을 살펴보았습니다. 앱에서 Azure Functions를 사용하는 방법에 대해 자세히 알아보려면 다음 항목을 참조하세요. 

- [Azure Functions에 대한 모범 사례](functions-best-practices.md) Azure Functions를 만들 때 사용할 몇 가지 모범 사례를 나열합니다.

- [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.

- [Azure Functions 테스트](functions-test-a-function.md) 함수를 테스트하는 다양한 도구와 기법을 설명합니다.