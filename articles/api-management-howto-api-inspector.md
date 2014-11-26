<properties pageTitle="How to use the API Inspector to trace calls in Azure API Management" metaKeywords="" description="Learn how to trace calls using the API Inspector in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to use the API Inspector to trace calls in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# API 검사기를 사용하여 Azure API 관리에서 호출을 추적하는 방법

API 관리(미리 보기)는 API 디버깅 및 문제 해결로 사용자를 돕는 API 검사기 도구를 제공합니다. 응용 프로그램에서 API 검사기를 프로그래밍 방식으로 사용할 수 있으며 개발자 포털에서 직접 사용할 수도 있습니다. 이 안내서는 API 검사기 사용을 단계적으로 안내해 줍니다.

## 항목 내용

- [API 검사기를 사용하여 호출 추적][API 검사기를 사용하여 호출 추적]
- [추적 검사][추적 검사]
- [다음 단계][다음 단계]

## <a name="trace-call"></a> API 검사기를 사용하여 호출 추적

API 검사기를 사용하려면 **ocp-apim-trace: true** 요청 헤더를 작업 호출에 추가한 다음 **ocp-apim-trace-location** 응답 헤더로 표시된 URL을 사용하여 추적을 다운로드하고 검사합니다. 이 작업은 프로그래밍 방식으로 수행할 수도 있고, 개발자 포털에서 직접 수행할 수도 있습니다.

이 자습서에서는 API 검사기를 통해 Echo API를 사용하는 작업을 추적하는 방법을 보여 줍니다.

>각 API 관리 서비스 인스턴스는 실험해 보고 API 관리에 대해 알아보는 데 사용할 수 있는 Echo API가 미리 구성되어 제공됩니다. Echo API는 전송된 입력을 다시 반환합니다. 이를 사용하려면 HTTP 동사를 호출하세요. 반환 값은 단순히 사용자가 보낸 값입니다.

시작하려면 Azure 포털에서 API 관리 서비스에 대한 **개발 포털**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![API 관리 개발자 포털][API 관리 개발자 포털]

개발자 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다. 이 자습서 단계에서는 **Echo API**가 **GET 리소스** 메서드를 호출합니다.

상단 메뉴에서 **API**를 클릭한 다음 **Echo API**를 클릭합니다.

![Echo API][Echo API]

>API 한 개만 구성했거나 계정에 표시한 경우에는 API를 클릭하면 해당 API에 대한 작업으로 직접 연결됩니다.

**GET 리소스** 작업을 선택하고 **콘솔 시작**을 클릭합니다.

![콘솔 시작][콘솔 시작]

기본 매개 변수 값을 유지하고 **구독 키** 드롭다운에서 구독 키를 선택합니다.

**요청 헤더** 입력란에 **ocp-apim-trace: true**를 입력하고 **HTTP Get**를 클릭합니다.

![HTTP Get][HTTP Get]

응답 헤더에서는 다음 예제와 유사한 값이 포함된 **ocp-apim-trace-location**이 됩니다.

	ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

다음 단계에 설명된 대로 지정된 위치에서 추적을 다운로드하고 검토할 수 있습니다.

## <a name="inspect-trace"></a>추적 검사

추적에서 값을 검토하려면 **ocp-apim-trace-location** URL에서 추적 파일을 다운로드합니다. 이 파일은 JSON 형식의 텍스트 파일이며 다음 예제와 유사한 항목을 포함합니다.

	{
	  "validityGuid":"a43a07a03de04fcb8b1425df38514742",
	  "logEntries":[
		{
			"timestamp":"2014-05-03T21:00:13.2182473Z",
			"source":"Microsoft.WindowsAzure.ApiManagement.Proxy.Gateway.Handlers.DebugLoggingHandler",
			"data":{
			"originalRequest":{
				"method":"GET",
				"url":"https://contosoltd.current.int-azure-api.net/echo/resource?param1=sample&subscription-key=...",
				"headers":[
				{
					"name":"ocp-apim-tracing",
					"value":"true"
				},
				{
					"name":"Host",
					"value":"contosoltd.current.int-azure-api.net"
				}
				]
			}
			}
		},
		{
		  "timestamp":"2014-05-03T21:00:13.2182473Z",
		  "source":"request handler",
		  "data":{
			"configuration":{
			  "api":{
				"from":"echo",
				"to":"http://echoapi.cloudapp.net/api"
			  },
			  "operation":{
				"method":"GET",
				"uriTemplate":"/resource"
			  },
			  "user":{
				"id":1,
				"groups":[
              
				]
			  },
			  "product":{
				"id":1
			  }
			}
		  }
		},
		{
		  "timestamp":"2014-05-03T21:00:13.2182473Z",
		  "source":"backend call handler",
		  "data":{
			"message":"Sending request to the service.",
			"request":{
			  "method":"GET",
			  "url":"http://echoapi.cloudapp.net/api/resource?param1=sample&subscription-key=...",
			  "headers":[
				{
				  "name":"X-Forwarded-For",
				  "value":"138.91.78.77"
				},
				{
				  "name":"ocp-apim-tracing",
				  "value":"true"
				}
			  ]
			}
		  }
		},
		{
		  "timestamp":"2014-05-03T21:00:13.2182473Z",
		  "source":"backend call handler",
		  "data":{
			"status":{
			  "code":200,
			  "reason":"OK"
			},
			"headers":[
			  {
				"name":"Pragma",
				"value":"no-cache"
			  },
			  {
				"name":"Host",
				"value":"echoapi.cloudapp.net"
			  },
			  {
				"name":"X-Forwarded-For",
				"value":"138.91.78.77"
			  },
			  {
				"name":"ocp-apim-tracing",
				"value":"true"
			  },
			  {
				"name":"Content-Length",
				"value":"0"
			  },
			  {
				"name":"Cache-Control",
				"value":"no-cache"
			  },
			  {
				"name":"Expires",
				"value":"-1"
			  },
			  {
				"name":"Server",
				"value":"Microsoft-IIS/8.0"
			  },
			  {
				"name":"X-AspNet-Version",
				"value":"4.0.30319"
			  },
			  {
				"name":"X-Powered-By",
				"value":"Azure API Management - http://api.azure.com/,ASP.NET"
			  },
			  {
				"name":"Date",
				"value":"Sat, 03 May 2014 21:00:17 GMT"
			  }
			]
		  }
		}
	  ]
	}


## <a name="next-steps"></a>다음 단계

- [고급 API 구성 시작][고급 API 구성 시작] 자습서에서 다른 항목을 확인하세요.

[API 검사기를 사용하여 호출 추적]: #trace-call
[추적 검사]: #inspect-trace
[다음 단계]: #next-steps
[Azure API 관리 시작]: ../api-management-get-started
[API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
[API 관리 개발자 포털]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[Echo API]: ./media/api-management-howto-api-inspector/api-management-echo-api.png
[콘솔 시작]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[HTTP Get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[고급 API 구성 시작]: ../api-management-get-started-advanced
