
<properties
	pageTitle="논리 앱용 API 앱 향상"
	description="이 문서에서는 논리 앱을 깔끔하게 작업할 API 앱을 데코레이트하는 방법을 보여줍니다."
	services="app-service\api"
	documentationCenter=".net"
	authors="sameerch"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="sameerch;guayan;tarcher"/>

# 논리 앱용 API 앱 향상 #

이 문서에서는 API 앱의 API 정의를 정의하는 방법을 배워서 논리 앱과 함께 잘 작동되도록 합니다. 이것이 논리 앱 디자이너에서 사용되는 경우 API 앱에 대한 최종 사용자 환경을 향상시킵니다.

## 필수 조건

[Azure 앱 서비스](../app-service/app-service-value-prop-what-is.md)에서 [API 앱](app-service-api-apps-why-best-platform.md)을 처음 사용하는 경우, [API 앱 만들기](app-service-dotnet-create-api-app.md)에서 여러 요소 시리즈를 읽는 것이 좋습니다.


## 표시 이름 추가 ##
논리 앱 디자이너는 프로그래밍 방식으로 생성되어 때로 읽기가 복잡한 작업, 필드 및 매개 변수 이름을 표시합니다. 가독성을 높이기 위해 사용할 수 있다면 어디서든 논리 앱 디자이너는 작업, 필드 및 매개 변수 기본 이름 대신*표시 이름*으로 알려진 보다 읽기 쉬운 텍스트 값을 표시할 수 있습니다. 이것을 위해서 논리 앱 디자이너는 API 앱이 제공하는 swagger 메타데이터에서 특정 속성의 현재 상태를 검색합니다. 표시 이름으로는 다음과 같은 속성이 사용됩니다.

* 작업 (동작 및 트리거) **요약** 속성의 값(있는 경우)은 그렇지 않은 경우 **작업 Id** 속성의 값입니다. Swagger 2.0 사양은 **요약** 속성에 최대 120자를 허용합니다.

* 매개 변수 (입력) **x-ms-요약** 확장 속성의 값(있는 경우)은 그렇지 않은 경우 **이름** 속성의 값입니다. **x-ms-요약** 확장 속성은 코드에서 동적으로 설정해야 합니다. 해당 프로세스는 이 항목의 "사용자 지정 특성을 확장 속성의 주석으로 사용" 섹션에 설명되어 있습니다. / / / 설명을 사용하여 **이름** 속성을 설정할 수 있습니다. 해당 프로세스는 이 항목의 "XML 설명을 API 정의 생성에 사용" 섹션에 설명되어 있습니다.

* 스키마 필드 (출력 응답) **x-ms-요약** 확장 속성의 값(있는 경우)은 그렇지 않은 경우 **이름** 속성의 값입니다. **x-ms-요약** 확장 속성은 코드에서 동적으로 설정해야 합니다. 해당 프로세스는 이 항목의 "사용자 지정 특성을 확장 속성의 주석으로 사용" 섹션에 설명되어 있습니다. / / / 설명을 사용하여 **이름** 속성을 설정할 수 있습니다. 해당 프로세스는 이 항목의 "XML 설명을 API 정의 생성에 사용" 섹션에 설명되어 있습니다.

**참고:** 30자 이하로 표시 이름의 길이를 유지하도록 권장됩니다.

### XML 설명을 API 정의 생성에 사용

Visual Studio를 사용하여 개발하기 위해 일반적으로 [XML 설명](https://msdn.microsoft.com/library/b2s063f7.aspx)을 사용하여 API 컨트롤러에 주석을 추가합니다. [/doc](https://msdn.microsoft.com/library/3260k4x7.aspx)로 컴파일하는 경우, 컴파일러는 XML 문서 파일을 만듭니다. API 앱 SDK에 포함된 Swashbuckle 도구 집합은 API 메타데이터를 생성하는 동안 이러한 의견을 통합할 수 있고, 그렇게 하기 위해 단계를 수행하여 API 프로젝트를 구성할 수 있습니다.

1. Visual Studio에서 새 프로젝트를 엽니다.

2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.

	![프로젝트 속성](./media/app-service-api-optimize-for-logic-apps/project-properties.png)

3. 프로젝트의 속성 페이지가 표시되면 다음 단계를 수행합니다.

	- 설정을 적용하기 위해 **구성**을 선택합니다. 일반적으로 모든 구성을 선택하여 지정한 설정을 빌드의 디버그 및 릴리스에 적용합니다.
	
	- 왼쪽에서 **빌드** 탭 선택
	
	- **XML 문서 파일** 옵션이 선택되었는지 확인합니다. Visual Studio는 프로젝트의 이름을 기반으로 기본 파일 이름을 제공합니다. 명명 규칙이 필요한 무엇이든 해당 값을 설정하거나 그대로 둘 수 있습니다.

	![XML 문서 속성 설정](./media/app-service-api-optimize-for-logic-apps/xml-documentation-file-property.png)

4. *SwaggerConfig.cs* 파일을 엽니다.(프로젝트의 **App_Start** 폴더에 위치)

5. **System** 및 **System.Globalization** 네임스페이스용 *SwaggerConfig.cs*파일의 위쪽에 **using** 지시문을 추가합니다.

		using System;
		using System.Globalization;
 
6. **GetXmlCommentsPath**을 호출하기 위해 *SwaggerConfig.cs* 파일을 찾고 줄의 주석 처리를 제거하여 실행되도록 합니다. 아직 이 메서드를 구현하지 않았기 때문에 Visual Studio는 **GetXmlCommentsPath**의 호출을 강조하고 현재 컨텍스트에 정의되지 않았음을 나타냅니다. 좋은 현상입니다. 다음 단계에서 이를 구현할 수 있습니다.

7. (*SwaggerConfig.cs*파일에 정의된) **SwaggerConfig** 클래스에 대한 **GetXmlCommentsPath** 메서드에 다음 구현을 추가합니다. 이 메서드는 프로젝트의 설정에서 이전에 지정한 XML 문서 파일을 반환하기만 하면 됩니다.

        public static string GetXmlCommentsPath()
        {
            return String.Format(CultureInfo.InvariantCulture, 
								 @"{0}\bin\ContactsList.xml", 
								 AppDomain.CurrentDomain.BaseDirectory);
        }

8. 마지막으로, 컨트롤러 메서드에 대한 XML 주석을 지정합니다. 이 작업을 수행하기 위해 API 앱의 컨트롤러 파일 중 하나를 열고, 문서화하려면 컨트롤러 메서드 앞 빈 줄에 / / /을 입력합니다. Visual Studio는 매개 변수 및 반환 값 정보에 더해 매서드 요약을 지정할 수 있는 주석 있는 섹션을 자동으로 삽입합니다.

이제 API 앱을 빌드하고 게시할 때 설명서 파일 페이로드에 존재하고 나머지 API 앱과 함께 업로드된 것을 볼 수 있습니다.

## 고급 작업 및 속성 분류

논리 앱 디자이너에서는 작업, 매개 변수 및 속성을 표시하기 위해 화면 부동산을 제한했습니다. 또한 API 앱은 광범위한 작업 및 속성 집합을 정의할 수 있습니다. 작은 영역에서 많은 양의 정보가 표시된 결과, 디자이너를 사용하여 최종 사용자가 힘들어지게 될 수 있습니다.

이 잡동사니를 줄이기 위해 논리 앱 디자이너를 사용하면 API 앱의 작업 및 속성을 사용자 정의 범주로 그룹화할 수 있습니다. 작업 및 속성의 적절한 분류를 사용하여 API 앱은 가장 기본적이고 유용한 작업 및 속성을 우선 제공하여 사용자 환경을 향상시킬 수 있습니다.

이 기능을 제공하기 위해 논리 앱 디자이너는 API 앱의 swagger API 정의에서 특정 사용자 지정 공급 업체 확장 속성의 현재 상태를 찾습니다. 이 속성의 이름은 **x-ms-표시**이며 다음 값을 취할 수 있습니다.

* 비어 있거나 "none" 이런 작업 및 속성은 사용자가 쉽게 볼 수 있습니다.

* "고급" 이러한 작업 및 속성은 고급이며 기본적으로 숨겨져 있습니다. 그러나 필요한 경우 사용자는 쉽게 액세스할 수 있습니다.

* "내부" 이러한 작업 및 속성은 시스템 또는 내부 속성으로 처리되며 사용자가 직접 사용할 수 없습니다. 결과적으로, 디자이너는 이것을 숨기고 코드 보기에서만 사용할 수 있습니다. 이러한 속성에 대해 논리 앱 디자이너를 통해 값을 설정하여 **x-ms-스케줄러-권장** 확장 속성을 지정할 수도 있습니다. 예를 들어, [API 앱에 트리거를 추가](app-service-api-dotnet-triggers.md)하는 문서를 참조하십시오.


## 확장 속성에 주석을 추가하려면 사용자 지정 특성을 사용

위에서 설명했듯이 논리 앱 디자이너가 사용할 수 있는 다양한 정보를 제공하는 API 메타데이터에 주석을 추가하려면 사용자 지정 공급 업체 확장 속성을 사용합니다. API 앱을 설명하기 위해 정적 메타데이터를 사용하는 경우, 프로젝트에 필요한 확장 속성을 수동으로 추가하려면 */metadata/apiDefinition.swagger.json*를 직접 편집할 수 있습니다.

동적 메타데이터를 사용하는 API 앱에 대해 코드에 주석을 추가하려면 사용자 지정 특성을 사용할 수 있습니다. 그런 다음에 사용자 지정 특성에 대해 살펴보고 필요한 공급 업체 확장에 추가하여 *SwaggerConfig.cs* 파일에서 작업 필터를 정의할 수 있습니다. **x-ms-요약** 확장 속성을 동적으로 생성하기 위해 이 방법을 아래에서 자세히 설명합니다.

1. 코드에 주석을 추가하기 위해 사용하는 **CustomSummaryAttribute**라고 하는 특성 클래스를 정의합니다.

	    [AttributeUsage(AttributeTargets.All)]
	    public class CustomSummaryAttribute : Attribute
	    {
	        public string SummaryText { get; internal set; }

	        public CustomSummaryAttribute(string summaryText)
	        {
	            this.SummaryText = summaryText;
	        }
	    }

2. 작업 매개 변수에서 사용자 지정 특성을 찾는 **AddCustomSummaryFilter**라고 하는 작업 필터를 정의합니다.


	    using Swashbuckle.Swagger;

		...

		public class AddCustomSummaryFilter : IOperationFilter
	    {
	        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
	        {
	            if (operation.parameters == null)
	            {
	                // no parameter
	                return;
	            }

	            foreach (var param in operation.parameters)
	            {
	                var summaryAttributes = apiDescription.ParameterDescriptions.First(x => x.Name.Equals(param.name))
	                                        .ParameterDescriptor.GetCustomAttributes<CustomSummaryAttribute>();

	                if (summaryAttributes != null && summaryAttributes.Count > 0)
	                {
	                    // add x-ms-summary extension
	                    if (param.vendorExtensions == null)
	                    {
	                        param.vendorExtensions = new Dictionary<string, object>();
	                    }
	                    param.vendorExtensions.Add("x-ms-summary", summaryAttributes[0].SummaryText);
	                }
	            }
	        }
	    }

3. *SwaggerConfig.cs* 파일을 편집하고 위에 정의된 필터 클래스를 추가합니다.


            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                    {
                        ...
                        c.OperationFilter<AddCustomSummaryFilter>();
                        ...
                    }

4. 사용자 코드에 주석을 추가하려면 다음 코드 조각에 보이는 것처럼 **CustomSummaryAttribute** 클래스를 사용합니다.

        /// <summary>
        /// Send Message
        /// </summary>
        /// <param name="queueName">The name of the Storage Queue</param>
        /// <param name="messageText">The message text to be sent</param>
        public void SendMessage(
            [CustomSummary("Queue Name")] string queueName,
            [CustomSummary("Message Text")] string messageText)
        {
             ...
        }

	위의 API 앱을 작성할 때 다음과 같은 API 메타데이터가 생성됩니다.


			...
            "post": {
                ...
                "parameters": [
                    {
                        "name": "queueName",
                        "in": "query",
                        "description": "The name of the Storage Queue",
                        "required": true,
                        "x-ms-summary": "Queue Name",
                        "type": "string"
                    },
                    {
                        "name": "messageText",
                        "in": "query",
                        "description": "The message text to be sent",
                        "required": true,
                        "x-ms-summary": "Message Text",
                        "type": "string"
                    }
                ],
                ...

5. 마찬가지로, 다음 예제와 같이 스키마 모델에 대해 **x-ms-요약** 확장 속성의 주석을 자동으로 지정하려면 스키마 필터**AddCustomSummarySchemaFilter**를 정의할 수 있습니다.

	    public class AddCustomSummarySchemaFilter: ISchemaFilter
	    {
	        public void Apply(Schema schema, SchemaRegistry schemaRegistry, Type type)
	        {
	            SetCustomSummary(schema, type.GetCustomAttribute<CustomSummaryAttribute>());

	            if (schema.properties != null)
	            {
	                foreach (var property in schema.properties)
	                {
	                    var summaryAttribute = type.GetProperty(property.Key).GetCustomAttribute<CustomSummaryAttribute>();
	                    SetCustomSummary(property.Value, summaryAttribute);
	                }
	            }
	        }

	        private static void SetCustomSummary(Schema schema, CustomSummaryAttribute summaryAttribute)
	        {
	            if (summaryAttribute != null)
	            {
	                if (schema.vendorExtensions == null)
	                {
	                    schema.vendorExtensions = new Dictionary<string, object>();
	                }
	                schema.vendorExtensions.Add("x-ms-summary", summaryAttribute.SummaryText);
	            }
	        }
	    }

## 요약

이 문서에서는 논리 앱 디자이너에서 사용되는 경우 API 앱의 사용자 환경을 개선하는 방법을 살펴보았습니다. 모범 사례로, 모든 작업(동작 및 트리거) 매개 변수 및 속성에 대한 적절하고 친숙한 이름을 제공하는 것이 좋습니다. 또한 5개 이하의 기본 작업을 제공하는 것이 좋습니다. 입력된 매개 변수에 대해 기본 속성 개수를 4개 이하로 제한하도록 권장하고 속성에 대해서는 5개 이하로 권장합니다. 사용자의 작업 및 속성의 나머지 부분은 고급으로 표시해야 합니다.
 

<!---HONumber=July15_HO3-->