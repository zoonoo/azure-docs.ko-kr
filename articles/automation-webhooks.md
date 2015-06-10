<properties 
   pageTitle="Azure 자동화 Webhook"
   description="클라이언트가 Azure 자동화에서 HTTP 호출을 통해 runbook을 시작하는 데 사용되는 webhook입니다. 이 문서는 webhook을 만드는 방법 및 webhook을 호출하여 runbook을 시작하는 방법에 대해 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2015"
   ms.author="bwren" />

# Azure 자동화 Webhook

*Webhook*을 사용하면 단일 HTTP 요청을 통해 Azure 자동화에서 특정 runbook을 시작할 수 있습니다. 이는 Azure 자동화 API를 사용하여 전체 솔루션을 구현하지 않아도 Visual Studio Online, GitHub 또는 사용자 지정 응용 프로그램과 같은 외부 서비스가 Runbook을 시작할 수 있게 해줍니다.

![Webhook](media/automation-webhooks/webhooks-overview.png)

[Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)에서 Runbook을 시작하는 다른 방법과 Webhook을 비교할 수 있습니다.

## Webhook의 세부 정보

다음 표에서는 Webhook에 대해 구성해야 하는 속성을 설명합니다.

| 속성 | 설명 |
|:---|:---|
|이름 | 클라이언트에 노출되지 않으므로 원하는 Webhook 이름을 제공할 수 있습니다. Azure 자동화에서 사용자가 runbook을 식별하는 용도로만 사용됩니다. <br> 가장 좋은 방법은 webhook을 사용할 클라이언트와 관련된 이름을 지정하는 것입니다. |
|URL |webhook의 URL은 클라이언트가 webhook에 연결된 runbook을 시작하기 위해 HTTP POST로 호출하는 고유한 주소입니다. webhook을 만들 때 자동으로 생성됩니다. 사용자 지정 URL은 지정할 수 없습니다. <br> <br> URL에는 타사 시스템이 추가 인증 없이 runbook을 호출할 수 있게 해주는 보안 토큰이 포함됩니다. 따라서 암호처럼 취급해야 합니다. 보안상의 이유로 이 URL은 Azure Preview 포털에서 webhook이 생성될 때만 볼 수 있습니다. 이 URL을 나중에 사용할 수 있도록 안전한 위치에 기록해 두어야 합니다. |
|만료 날짜 | 각 webhook은 인증서처럼 만료 날짜가 있으며, 이 날짜가 되면 인증서를 더 이상 사용할 수 없습니다. 이 만료 날짜는 webhook이 생성된 후에는 변경할 수 없으며, 만료 날짜에 도달한 후에는 webhook을 다시 사용할 수도 없습니다. 이 경우 다른 webhook을 만들어 현재 webhook을 바꾸고 새 webhook을 사용하도록 클라이언트를 업데이트해야 합니다. |
| 사용 | Runbook은 생성되었을 때 기본적으로 사용하도록 설정됩니다. 사용 안함으로 설정할 경우 클라이언트가 사용할 수 없게 됩니다. **Enabled** 속성은 webhook을 만들 때 또는 webhook이 생성된 후 언제든지 설정할 수 있습니다. |


### 매개 변수
webhook은 runbook을 시작할 때 사용되는 runbook 매개 변수 값을 정의할 수 있습니다. webhook에는 runbook의 모든 필수 매개 변수 값이 포함되어야 하고 선택적 매개 변수 값이 포함 될 수 있습니다. 단일 runbook에 연결된 여러 webhook은 각각 다른 매개 변수 값을 사용할 수 있습니다.

>[AZURE.NOTE]webhook에서 설정한 매개 변수 값은 현재 webhook이 생성된 후에 변경할 수 없습니다. 다른 매개 변수 값을 사용하는 다른 webhook을 만들어야 합니다.

클라이언트는 webhook을 사용하여 runbook을 시작할 때 webhook에 정의된 매개 변수 값을 재정의할 수 없습니다. 클라이언트에서 데이터를 수신하기 위해 runbook은 클라이언트가 POST 요청에 포함하는 데이터가 포함된 [object] 형식의 **$WebhookData**라는 단일 매개 변수를 받아들일 수 있습니다.

![Webhookdata](media/automation-webhooks/webhookdata.png)

**$WebhookData** 개체에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 |
|:--- |:---|
| WebhookName | Webhook의 이름입니다. |
| RequestHeader | 들어오는 POST 요청의 헤더입니다. |
| RequestBody | 들어오는 POST 요청의 본문입니다. |

**$WebhookData** 매개 변수를 지원하는 데 필요한 webhook 구성은 없으며, runbook은 이를 수락할 필요가 없습니다. runbook이 매개 변수를 정의하지 않을 경우 클라이언트에서 전송된 요청의 모든 세부 정보가 무시됩니다.

webhook을 만들 때 $WebhookData에 값을 지정 하면 클라이언트가 요청 본문에 데이터를 포함하지 않을 경우에도 webhook이 클라이언트 POST 요청의 데이터로 runbook을 시작할 때 해당 값이 재정의됩니다. webhook 이외의 방법을 사용하는 $WebhookData가 있는 runbook을 시작하는 경우 runbook에서 인식할 $Webhookdata 값을 제공할 수 있습니다. 이 값은 runbook이 함께 제대로 작동할 수 있도록 $Webhookdata와 동일한 속성을 가진 개체여야 합니다.

>[AZURE.NOTE]모든 입력 매개 변수의 값은 runbook 작업에 기록됩니다. 즉, 클라이언트에서 제공하는 모든 입력이 $WebhookData에 기록되고 자동화 작업에 액세스할 수 있는 모든 사용자가 사용할 수 있게 됩니다. 따라서 webhook 호출에 중요한 정보를 포함할 때는 주의해야 합니다.

## 보안

webhook의 보안은 webhook의 호출에 사용되는 보안 토큰을 포함하는 URL의 개인 정보에 의존합니다. Azure 자동화는 올바른 URL로 설정되어 있는 경우에만 요청에 대한 인증을 수행합니다. 따라서 대체 유효성 검사 방법을 사용하지 않고 매우 중요한 기능을 수행하는 runbook에 webhook을 사용해서는 안 됩니다.

$WebhookData 매개 변수의 **WebhookName** 속성을 확인하여 webhook에서 호출했는지 확인하는 논리를 runbook 내에 포함할 수 있습니다. Runbook은 **RequestHeader** 또는 **RequestBody** 속성에서 특정 정보를 찾아 추가로 유효성 검사를 수행할 수 있습니다.

또 다른 전략은 runbook이 webhook 요청을 받았을 때 외부 조건의 일부 유효성 검사를 수행하도록 하는 것입니다. 예를 들어 GitHub 리포지토리에 대한 새 커밋이 있을 때마다 GitHub에 의해 호출되는 runbook을 가정해 보겠습니다. 이 Runbook은 계속하기 전에 GitHub에 연결하여 새 커밋이 실제로 방금 전에 발생했는지 유효성 검사를 수행할 수 있습니다.

## webhook 만들기

Azure Preview 포털에서 runbook에 연결된 새 webhook을 만들려면 다음 절차를 수행합니다.

1. Azure Preview 포털의 **Runbook 블레이드**에서 webhook이 세부 정보 블레이드를 볼 runbook을 클릭합니다. 
3. 블레이드 맨 위에서 **Webhook**을 클릭하여 **Webhook 추가** 블레이드를 엽니다. <br> ![Webhook 단추](media/automation-webhooks/webhooks-button.png)
4. **새 webhook 만들기**를 클릭하여 **webhook 블레이드 만들기**를 엽니다.
5. webhook의 **이름**, **만료 날짜**와 사용 여부를 지정합니다. 이러한 속성에 대한 자세한 내용은 [webhook 세부 정보](#details-of-a-webhook)를 참조하십시오.
6. 복사 아이콘을 클릭하고 Ctrl+C를 눌러 webhook의 URL을 복사합니다. 그런 다음 안전한 곳에 기록합니다. **webhook을 만들고 나면 URL을 다시 검색할 수 없습니다.** <br> ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)
1. **만들기**를 클릭하여 webhook을 만듭니다.
3. **매개 변수**를 클릭하여 runbook 매개 변수의 값을 제공합니다. <br>
1. webhook 구성을 마쳤으면 **확인**을 클릭합니다.


## webhook 사용

webhook을 사용하려면 클라이언트 응용 프로그램이 webhook의 URL로 HTTP POST를 실행해야 합니다. webhook의 구문 형식은 다음과 같습니다.

	http://<Webhook Server>/token?=<Token Value>


클라이언트는 POST 요청에 대한 응답으로 다음과 같은 코드 중 하나를 받게 됩니다.

| 코드 | Text | 설명 |
|:---|:----|:---|
| 202 | 수락됨 | 요청이 수락되었고 runbook이 시작되었습니다. |
| 400 | 잘못된 요청 | 다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook이 만료되었습니다.</li> <li>webhook이 비활성화되었습니다.</li> <li>URL의 토큰이 잘못되었습니다.</li> </ul>|
| 500 | 내부 서버 오류 | URL은 유효했지만 오류가 발생했습니다. 요청을 다시 제출하십시오. |

webhook에서 runbook 작업에 대한 식별자를 반환하지 않으므로 클라이언트에서 runbook 작업이 완료된 시기 또는 완료 상태를 확인할 수 없습니다. 요청이 성공적으로 제출되었는지만 확인할 수 있습니다.

### 예제

다음 예제는 Windows PowerShell에서 webhook을 사용하여 runbook을 시작 합니다. 이 샘플에는 runbook에서 사용할 수 있는 헤더 및 본문의 데이터가 포함되어 있습니다. HTTP 요청을 만들 수 있는 모든 언어는 webhook을 사용할 수 있습니다.

	$uri = "https://oaaswebhookcurrent.cloudapp.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"header1"="headerval1";"header2"="headerval2"}
	$body = "some request body"

	Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body

다음 샘플 runbook은 이전 요청을 수락하고 webhook에서 데이터를 검색합니다.

	workflow Sample-Webhook
	{
		param (	
				[object]$WebhookData
		)
	
		$WebhookName 	= 	$WebhookData.WebhookName
		$WebhookHeaders = 	$WebhookData.RequestHeader
		$WebhookBody 	= 	$WebhookData.RequestBody
	} 

	

## 관련된 문서

- [Runbook 시작](automation-starting-a-runbook.md)
- [Runbook 작업의 상태 보기](automation-viewing-the-status-of-a-runbook-job.md)

<!---HONumber=58-->