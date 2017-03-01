
---
title: "Swashbuckle 생성 API 정의 사용자 지정"
description: "Azure 앱 서비스에서  API 앱용 Swashbuckle에 의해 생성된 Swagger API 정의를 사용자 지정하는 방법에 대해 알아봅니다."
services: app-service\api
documentationcenter: .net
author: bradygaster
manager: erikre
editor: jimbe
ms.assetid: 6b8cbc38-d282-4a0f-b0c5-762631bae6f3
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: c83905a97fb2ee988fe06fc1f9a7379c1741fd02
ms.lasthandoff: 03/01/2017


---
# <a name="customize-swashbuckle-generated-api-definitions"></a>Swashbuckle 생성 API 정의 사용자 지정
## <a name="overview"></a>개요
이 문서에서는 기본 동작을 변경하려는 일반적인 시나리오를 처리하도록 Swashbuckle을 사용자 지정하는 방법을 설명합니다.

* Swashbuckle는 컨트롤러 메서드의 오버 로드에 대한 중복 작업 식별자를 생성합니다.
* Swashbuckle은 메서드에서 유효한 응답이 HTTP 200(정상)이라고 가정합니다. 

## <a name="customize-operation-identifier-generation"></a>작업 식별자 생성 사용자 지정
Swashbuckle은 컨트롤러 이름과 메서드 이름을 연결하여 Swagger 작업 식별자를 생성합니다. 이 패턴은 메서드가 여러 번 오버로드되는 경우 문제가 발생합니다. Swashbuckle에서 중복된 작업 ID를 생성하며 이는 잘못된 Swagger JSON입니다.

예를 들어 다음 컨트롤러 코드를 사용하면 Swashbuckle에서 Contact_Get 작업 ID를 생성합니다.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

이 예제의 경우 다음과 같이 메서드에 고유한 이름을 지정하여 문제를 수동으로 해결할 수 있습니다.

* 가져오기
* GetById
* GetPage

대신 고유한 작업 ID를 자동으로 생성하도록 Swashbuckle을 확장할 수 있습니다.

다음 단계에서는 Visual Studio API Apps Preview 프로젝트 템플릿에 의해 프로젝트에 포함된 *SwaggerConfig.cs* 파일을 사용하여 Swashbuckle을 사용자 지정하는 방법을 보여 줍니다.  또한 API 앱으로 배포하도록 구성한 Web API 프로젝트에서 Swashbuckle을 사용자 지정할 수도 있습니다.

1. 사용자 지정 `IOperationFilter` 구현 만들기 
   
    `IOperationFilter` 인터페이스는 Swagger 메타데이터 프로세스의 다양한 측면을 사용자 지정하려는 Swashbuckle 사용자를 위해 확장성 지점을 제공합니다. 다음 코드는 operation-id-generation 동작을 변경하는 한 가지 방법을 보여 줍니다. 이 코드는 매개 변수 이름을 작업 ID 이름에 추가합니다.  
   
        using Swashbuckle.Swagger;
        using System.Web.Http.Description;
   
        namespace ContactsList
        {
            public class MultipleOperationsWithSameVerbFilter : IOperationFilter
            {
                public void Apply(
                    Operation operation,
                    SchemaRegistry schemaRegistry,
                    ApiDescription apiDescription)
                {
                    if (operation.parameters != null)
                    {
                        operation.operationId += "By";
                        foreach (var parm in operation.parameters)
                        {
                            operation.operationId += string.Format("{0}",parm.name);
                        }
                    }
                }
            }
        }
2. *App_Start\SwaggerConfig.cs* 파일에서 `OperationFilter` 메서드를 호출하여 Swashbuckle에서 새 `IOperationFilter` 구현을 사용하도록 합니다.
   
        c.OperationFilter<MultipleOperationsWithSameVerbFilter>();
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)
   
    Swashbuckle NuGet 패키지에서 삭제된 *SwaggerConfig.cs* 파일에는 확장성 지점의 주석 처리된 많은 예제가 포함되어 있습니다. 추가 주석은 여기에 나와 있지 않습니다. 
   
    이렇게 변경하면 `IOperationFilter` 구현이 사용되고 고유한 작업 ID가 생성됩니다.
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>

## <a name="allow-response-codes-other-than-200"></a>200 이외의 응답 코드 허용
기본적으로 Swashbuckle은 HTTP 200(정상) 응답이 웹 API 메서드에서 *유일하게* 올바른 응답이라고 가정합니다. 일부 경우에 클라이언트가 예외를 발생시키지 않는 다른 응답 코드를 반환할 수 있습니다.  예를 들어, 다음 웹 API 코드에는 클라이언트가 유효한 응답으로 200 또는 404를 사용할 수 있는 시나리오를 보여줍니다.

    [ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

이 시나리오에서 기본적으로 Swashbuckle이 생성하는 Swagger는 하나의 올바른 HTTP 상태 코드인, HTTP 200만을 지정합니다.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Visual Studio는 Swagger API 정의를 사용하여 클라이언트에 대한 코드를 생성하므로, HTTP 200 이외의 모든 응답에 대한 예외를 발생시키는 클라이언트 코드를 만듭니다. 아래 코드에서 이 샘플 웹 API 메서드에 대해 생성된 C# 클라이언트입니다.

    if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle에서는 XML 주석이나 `SwaggerResponse` 특성을 사용하여 생성하는 예상된 HTTP 응답 코드의 목록을 사용자 지정하는 두 가지 방법을 제공합니다. 특성은 보다 쉽지만 Swashbuckle 5.1.5 이상에서만 사용할 수 있습니다. Visual Studio 2013의 API 앱 미리 보기 새 프로젝트 템플릿은 Swashbuckle 버전 5.0.0을 포함하므로, 템플릿을 사용하고 Swashbuckle를 업데이트 하지 않으려는 경우 유일한 방법은 XML 주석을 사용하는 것입니다. 

### <a name="customize-expected-response-codes-using-xml-comments"></a>XML 주석을 사용하여 예상되는 응답 코드 사용자 지정
Swashbuckle 버전이 5.1.5 이전인 경우 응답 코드를 지정하려면 이 메서드를 사용합니다.

1. 먼저, HTTP 응답 코드를 지정할 메소드에 대해 XML 문서 주석을 추가합니다. 위에 표시된 샘플 웹 API 조치를 취하고 XML 문서를 적용하면 다음 예제와 마찬가지로 코드에서 반환됩니다. 
   
        /// <summary>
        /// Returns the specified contact.
        /// </summary>
        /// <param name="id">The ID of the contact.</param>
        /// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
        /// <response code="200">OK</response>
        /// <response code="404">Not Found</response>
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
   
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
   
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }
2. *SwaggerConfig.cs* 파일에 지침을 추가하여 Swashbuckle이 XML 문서 파일을 사용하도록 합니다.
   
   * *SwaggerConfig.cs*를 열고 *SwaggerConfig* 클래스에 대한 메서드를 만들어 설명서 XML 파일의 경로를 지정합니다. 
     
           private static string GetXmlCommentsPath()
           {
               return string.Format(@"{0}\XmlComments.xml", 
                   System.AppDomain.CurrentDomain.BaseDirectory);
           }
   * 아래 스크린샷과 같은 코드의 주석 처리된 줄이 표시될 때까지 *SwaggerConfig.cs* 파일로 아래로 스크롤합니다. 
     
       ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
   * Swagger 생성 중 처리하는 XML 주석을 사용할 수 있도록 줄 주석 처리를 제거합니다. 
     
       ![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
3. XML 문서 파일을 생성하기 위해 프로젝트의 속성으로 이동하고 아래 스크린샷에 표시된 대로 XML 문서 파일을 사용하도록 설정합니다. 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png) 

이러한 단계를 수행한 후 Swashbuckle에서 생성된 Swagger JSON은 XML 주석에 지정된 HTTP 응답 코드를 반영합니다. 다음 스크린샷은 이 새 JSON 페이로드를 보여줍니다. 

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Visual Studio를 사용하여 REST API에 대한 클라이언트 코드를 다시 생성하려면, C# 코드는 예외가 발생 하지 않고 HTTP OK와 찾을 수 없음 상태 코드를 모두 허용하여, 사용자 지정 코드가 null 연락처 레코드를 처리하는 방법을 결정할 수 있습니다. 

        if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
        {
            HttpOperationException<object> ex = new HttpOperationException<object>();
            ex.Request = httpRequest;
            ex.Response = httpResponse;
            ex.Body = null;
            if (shouldTrace)
            {
                ServiceClientTracing.Error(invocationId, ex);
            }
                throw ex;
        }

이 데모에 대한 코드는 [이 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse)에 있을 수 있습니다. XML 문서 주석으로 표시된 Web API 프로젝트와 함께 이 API에 대해 생성된 클라이언트를 포함하는 콘솔 응용 프로그램이 제공됩니다. 

### <a name="customize-expected-response-codes-using-the-swaggerresponse-attribute"></a>SwaggerResponse 특성을 사용하여 예상되는 응답 코드 사용자 지정
[SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) 특성은 Swashbuckle 5.1.5 이상에서 사용할 수 있습니다. 프로젝트에 이전 버전이 있는 경우, 이 특성을 사용할 수 있도록 Swashbuckle NuGet 패키지를 업데이트하는 방법을 설명하여 이섹션을 시작합니다.

1. **솔루션 탐색기**에서 웹 API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)
2. *Swashbuckle* NuGet 패키지 옆의 *업데이트*를 클릭합니다. 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)
3. *SwaggerResponse* 특성을 유효한 HTTP 응답 코드를 지정할 웹 API 작업 메서드에 추가합니다. 
   
        [SwaggerResponse(HttpStatusCode.OK)]
        [SwaggerResponse(HttpStatusCode.NotFound)]
        [ResponseType(typeof(Contact))]
        public HttpResponseMessage Get(int id)
        {
            var contacts = GetContacts();
   
            var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
            if (requestedContact == null)
            {
                return Request.CreateResponse(HttpStatusCode.NotFound);
            }
            else
            {
                return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
            }
        }
4. 특성의 네임 스페이스에 대한 `using` 문 추가:
   
        using Swashbuckle.Swagger.Annotations;
5. 프로젝트의 */swagger/docs/v1* URL로 이동하면 다양한 HTTP 응답 코드를 Swagger JSON에서 볼 수 있습니다. 
   
    ![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

이 데모에 대한 코드는 [이 GitHub 리포지토리](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes)에 있을 수 있습니다. *SwaggerResponse* 특성으로 표시된 웹 API 프로젝트와 함께 이 API에 대해 생성된 클라이언트를 포함하는 콘솔 응용 프로그램이 제공됩니다. 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Swashbuckle이 작업 ID 및 유효한 응답 코드를 생성하는 방법을 사용자 지정하는 방법을 설명했습니다. 자세한 내용은 [GitHub의 Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)을 참조하세요.


