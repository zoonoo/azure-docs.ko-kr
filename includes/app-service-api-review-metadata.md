## API 앱 메타데이터 검토

Web API 프로젝트를 API 앱으로 배포할 수 있게 해주는 메타데이터는 *apiapp.json* 파일 및 *메타데이터* 폴더에 포함됩니다.

![](./media/app-service-api-review-metadata/metadatainse.png)

*apiapp.json* 파일의 기본 콘텐츠는 다음 예제와 유사합니다.

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

`apiDefinition`끝점`/swagger/docs/v1`을 참고하십시오: 기본적으로 API 앱 프로젝트는 자동 [Swagger](http://swagger.io/) 메타데이터 생성을 제공하는 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 패키지를 사용합니다.

이 자습서에서는 기본값을 적용할 수 있습니다. 자습서의 뒷부분에 있는 [API 앱 메타데이터](#api-app-metadata) 섹션에서는 이 메타데이터를 사용자 지정하는 방법을 설명합니다.

<!---HONumber=July15_HO4-->