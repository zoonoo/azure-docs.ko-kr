<properties
   pageTitle="Visual Studio Code에서 ASP.NET 5 API 앱 만들기"
   description="이 자습서에서는 Visual Studio Code를 사용하여 ASP.NET 5 API 앱을 만드는 방법을 보여줍니다."
   services="app-service\api"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="erikre"/>

# Visual Studio Code에서 ASP.NET 5 API 앱 만들기

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## 개요

이 자습서에서는 [Visual Studio Code](http://code.visualstudio.com//Docs/whyvscode)를 사용하여 ASP.NET 5 API 앱을 만드는 방법을 보여줍니다. ASP.NET 5는 ASP.NET을 완전히 다시 디자인한 것입니다. ASP.NET 5는 .NET을 사용하여 최신 클라우드 기반 웹 응용 프로그램을 제작할 수 있는 새로운 오픈 소스, 크로스 플랫폼 프레임워크입니다. 자세한 내용은 [ASP.NET 5 소개](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)를 참조하세요. API 앱에 대한 자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.

> [AZURE.NOTE]이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [가입하여 무료 평가판을 사용](/pricing/free-trial/)하거나 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있습니다. 또한 무료로 [앱 서비스 앱 샘플](http://tryappservice.azure.com)을 사용해 볼 수 있습니다.

## 필수 조건  

* [Visual Studio Code](http://code.visualstudio.com/Docs/setup)를 설치하고 설정합니다.
* [Node.js](http://nodejs.org/download/)를 설치합니다.<br>[Node](http://nodejs.org/)는 JavaScript를 사용하여 빠르고 확장성이 뛰어난 서버 응용 프로그램을 빌드할 수 있는 플랫폼입니다. Node는 런타임(Node)이고 [npm](http://www.npmjs.com/)은 Node 모듈에 대한 패키지 관리자입니다. 이 자습서에서는 npm을 사용하여 ASP.NET 5 API 앱을 스캐폴딩합니다.

## ASP.NET 5 및 DNX 설치
ASP.NET 5/DNX는 OS X, Linux 및 Windows에서 실행되는 최신 클라우드 및 웹앱을 제작하기 위한 린(lean) .NET 스택입니다. ASP.NET 5/DNX는 클라우드에 배포되거나 온-프레미스로 실행될 앱에 최적화된 개발 프레임워크를 제공하기 위해 처음부터 다시 제작되었습니다. 오버헤드를 최소화하는 모듈식 구성 요소로 구성되므로 솔루션을 구성하는 동안 유연성이 유지할 수 있습니다.

> [AZURE.NOTE]OS X 및 Linux의 경우 ASP.NET 5 및 DNX(.NET 실행 환경)는 초기 베타/미리 보기 상태입니다.

이 자습서는 ASP.NET 5와 DNX의 최신 개발 버전으로 응용 프로그램 제작을 시작하도록 작성되었습니다. 더 안정적인, 출시된 환경을 원한다면 [http://www.asp.net/vnext](http://www.asp.net/vnext)를 방문하세요. 다음은 Windows에 해당하는 지침입니다. OS X, Linux 및 Windows에 대한 자세한 설치 지침은 [ASP.NET 5 및 DNX 설치](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)를 참조하세요.

1. Windows에서.NET 버전 관리자(DNVM)를 설치하려면 명령 창에서 다음 명령을 실행합니다.

	<pre class="prettyprint">
@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&amp;{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"
</pre>이 명령은 DNVM 스크립트를 다운로드하여 사용자 프로필에 배치합니다.

2. 위 명령을 적용하려면 PATH 환경 변수의 변경 내용이 효력을 발휘하기 위해 명령을 입력한 후 로그오프해야 합니다.
3. 명령 창에서 다음 명령을 실행하여 DNVM의 위치를 확인합니다. 

	<pre class="prettyprint">
where dnvm
</pre>명령 창에 다음과 비슷한 경로가 표시됩니다.

	![dnvm 위치](./media/app-service-create-aspnet-api-app-using-vscode/00-where-dnvm.png)

4. 이제 DNVM이 있으므로, 응용 프로그램을 실행하기 위해 이를 사용하여 DNX를 다운로드해야 합니다. 명령 창에서 다음 명령을 실행합니다.

	<pre class="prettyprint">
dnvm upgrade
</pre>

5. DNVM을 확인하고 명령 창에서 다음을 입력하여 활성 런타임을 확인합니다.

	<pre class="prettyprint">
dnvm list
</pre>명령 창에 활성 런타임에 대한 세부 정보가 표시됩니다.

	![dnvm 위치](./media/app-service-create-aspnet-api-app-using-vscode/00b-dnvm-list.png)

OS X, Linux 및 Windows에 대한 자세한 설치 지침은 [ASP.NET 5 및 DNX 설치](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)를 참조하세요.

## API 앱 만들기 

이 섹션에서는 새 앱 ASP.NET API 앱을 스캐폴딩하는 방법을 보여줍니다. NPM(Node Package Manager)을 사용하여 [Yeoman](http://yeoman.io/), [Grunt](http://gruntjs.com/) 및 [Bower](http://bower.io/)를 설치하게 됩니다.

1. Visual Studio Code와 Node.js를 설치한 후 관리자 권한으로 명령 프롬프트를 열고 VSCode를 사용한 모든 ASP.NET 프로젝트를 배치할 원하는 위치로 이동합니다.
2. Yeoman과 지원 도구를 설치하려면 명령 창에 다음을 입력합니다.

	<pre class="prettyprint">
npm install -g yo grunt-cli generator-aspnet bower
</pre>

3. 프로젝트 폴더를 만들고 앱을 스캐폴딩하려면 명령 창에 다음을 입력합니다.

	<pre class="prettyprint">
yo aspnet
</pre>

4. 생성자에서 제공한 지침에 따라 스크롤하여 **웹 API 응용 프로그램** 유형을 선택합니다.

	![Yeoman - ASP.NET 5 생성기](./media/app-service-create-aspnet-api-app-using-vscode/01-yo-aspnet.png)

5. 새 ASP.NET API 앱의 이름을 **ContactsList**로 설정합니다. 이 이름은 이 자습서의 뒷부분에서 제공하는 코드에 사용됩니다. <br> Yoman에서 **ContactsList**라는 이름의 새 폴더와 새 앱에 필요한 파일을 만듭니다.
6. **Visual Studio Code**를 엽니다.<br> 명령 창에서 **code .**를 입력하여 VSCode를 열 수 있습니다.
7. **파일** 메뉴에서 **폴더 열기**를 선택한 다음 ASP.NET API 앱이 위치한 폴더를 선택합니다.

	![폴더 선택 대화 상자](./media/app-service-create-aspnet-api-app-using-vscode/02-open-folder.png)

	VSCode가 프로젝트를 로드하고 **탐색** 창에 표시합니다.

	![VSCode 연락처 표시 프로젝트](./media/app-service-create-aspnet-api-app-using-vscode/03-vscode-contacts-project.png)

8. **VSCode**의 **보기** 메뉴에서 **명령 팔레트**를 선택합니다.
9. **명령 팔레트**에서 다음 명령을 입력합니다.

	<pre class="prettyprint">
dnx:dnu restore - (ContactsList)
</pre>입력을 시작하면 목록에 전체 명령줄이 표시됩니다.

	![복원 명령](./media/app-service-create-aspnet-api-app-using-vscode/04-dnu-restore.png)

	복원 명령은 응용 프로그램을 실행하는 데 필요한 NuGet 패키지를 설치합니다. 준비가 되면 명령 창에 **복원 완료**가 표시됩니다.

## API 앱 수정

이제 **Contact** 클래스와 **ContactsController** 클래스를 추가하여 **ContactsList** 앱을 수정합니다.

1. **ContactsList** 프로젝트 이름에 커서를 올리고 새 폴더 아이콘을 사용하여 *Models*라는 이름의 새 폴더를 만듭니다.

	![새 Models 폴더](./media/app-service-create-aspnet-api-app-using-vscode/07-new-folder.png)

2. **Models** 폴더를 마우스 오른쪽 단추로 클릭하고 다음 코드를 사용하여 *Contact.cs*라는 새 클래스 파일을 추가합니다.

	<pre class="prettyprint">
namespace ContactsList.Models
{
    public class Contact
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string EmailAddress { get; set; }
    }
}
</pre>

3. **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고 *ContactsController.cs* 파일을 추가하여 다음과 같이 표시되도록 합니다.

	<pre class="prettyprint">
	using System.Collections.Generic;
	using Microsoft.AspNet.Mvc;
	using ContactsList.Models;

	namespace ContactsList.Controllers
	{
	    [Route("api/[controller]")]
	    public class ContactsController : Controller
	    {
	        // GET: api/Contacts
	        [HttpGet]
	        public IEnumerable&lt;Contact> Get()
	        {
	            return new Contact[]{
	                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
	                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
	                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}
	</pre>

4. **파일** > **모두 저장**을 선택하여 모든 파일을 저장합니다.
5. **명령 팔레트**에서 다음을 입력하여 앱을 로컬로 실행합니다.

	<pre class="prettyprint">
	dnx: kestrel - (ContactsList, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001
	</pre>

	명령 창에 *시작됨*이 표시됩니다. 명령 창에 *시작됨*이 표시되지 않는 경우 VSCode의 왼쪽 아래에서 프로젝트에 오류가 있는지 확인합니다.

5. 브라우저를 열고 다음 URL로 이동합니다.

	**http://localhost:5001/api/Contacts**로 바꿉니다.

	여기서 *Contacts.json*의 내용을 볼 수 있습니다. 파일에서 다음 내용을 볼 수 있습니다.

	![json 콘텐츠 반환](./media/app-service-create-aspnet-api-app-using-vscode/08-contacts-json.png)

## API 앱 메타데이터 수정
ASP.NET API 프로젝트를 API 앱으로 배포할 수 있게 해주는 메타데이터는 프로젝트의 루트에 있는 *apiapp.json* 파일에 포함되어야 합니다.

1. VSCode에서 *wwwroot* 폴더를 마우스 오른쪽 단추로 클릭하고 **새 파일** 옵션을 선택합니다.
2. 새 파일 이름을 *apiapp.json*으로 지정합니다.<br>*apiapp.json*은 *wwwroot* 폴더에 있어야 합니다.
3. 다음 내용을 *apiapp.json* 파일에 추가합니다.

	<pre class="prettyprint">
{
    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
    "id": "ContactsList",
    "namespace": "microsoft.com",
    "gateway": "2015-01-14",
    "version": "1.0.0",
    "title": "ContactsList",
    "summary": "",
    "author": "",
    "endpoints": null
}
</pre>

*apiapp.json* 파일에 동적 Swagger API 정의 JSON에 대한 끝점을 지정할 수 있지만 이 자습서에서는 정적 API 정의 파일을 사용합니다. 동적 Swagger 생성을 사용하는 예는 [API 앱으로 Web API 프로젝트 구성](app-service-dotnet-create-api-app-visual-studio.md)을 참조하세요.

## Swagger 정적 API 정의 추가
정적 Swagger 2.0 API 정의 파일을 제공하려면 웹 루트에 폴더를 만들고 여기에 API 정의 파일을 저장해야 합니다.

1. VSCode에서 *wwwroot* 폴더 내에 *metadata*라는 폴더를 만듭니다.
2. 새 *metadata* 폴더를 마우스 오른쪽 단추로 클릭하고 *apiDefinition.swagger.json*이라는 이름의 새 파일을 추가합니다. 
3. 새 파일에 다음 json 구문을 추가 합니다.

	<pre class="prettyprint">
{
  "swagger": "2.0",
  "info": {
    "version": "v1",
    "title": "ContactsList"
  },
  "host": "이 부분을 호스트 URL로 교체해야 합니다",
  "schemes": [
    "https"
  ],
  "paths": {
    "/api/Contacts": {
      "get": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Get",
        "consumes": [],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "type": "array",
              "items": {
                "$ref": "#/definitions/Contact"
              }
            }
          }
        },
        "deprecated": false
      },
      "post": {
        "tags": [
          "Contacts"
        ],
        "operationId": "Contacts_Post",
        "consumes": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml",
          "application/x-www-form-urlencoded"
        ],
        "produces": [
          "application/json",
          "text/json",
          "application/xml",
          "text/xml"
        ],
        "parameters": [
          {
            "name": "contact",
            "in": "body",
            "required": true,
            "schema": {
              "$ref": "#/definitions/Contact"
            }
          }
        ],
        "responses": {
          "200": {
            "description": "OK",
            "schema": {
              "$ref": "#/definitions/Object"
            }
          }
        },
        "deprecated": false
      }
    }
  },
  "definitions": {
    "Contact": {
      "type": "object",
      "properties": {
        "Id": {
          "format": "int32",
          "type": "integer"
        },
        "Name": {
          "type": "string"
        },
        "EmailAddress": {
          "type": "string"
        }
      }
    },
    "Object": {
      "type": "object",
      "properties": {}
    }
  }
}
</pre>

이 자습서의 뒷부분에서, 위 코드의 호스트 URL 자리 표시자 문자열을 곧 만들 Azure 호스트 URL로 바꾸게 됩니다.

## Azure Preview 포털에서 API 앱 만들기

> [AZURE.NOTE]이 자습서를 완료하려면 Microsoft Azure 계정이 필요합니다. 계정이 없는 경우 [가입하여 무료 평가판을 사용](/pricing/free-trial/)하거나 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/)할 수 있습니다. 또한 무료로 [앱 서비스 앱 샘플](http://tryappservice.azure.com)을 사용해 볼 수 있습니다.

1. [Azure Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 포털의 왼쪽 위에서 **새로 만들기**를 클릭합니다.

3. **웹 + 모바일 > API 앱**을 클릭합니다.

	![Azure 새 API 앱](./media/app-service-create-aspnet-api-app-using-vscode/09-azure-newapiapp.png)

4. **Name**의 값을 입력합니다. 예를 들어 ContactsList와 같은 이름을 사용합니다.

5. 앱 서비스 계획을 선택하거나 새로 만듭니다. 새 계획을 만드는 경우 가격 책정 계층, 위치 및 기타 옵션을 선택합니다.

	![Azure 새 API 앱 블레이드](./media/app-service-create-aspnet-api-app-using-vscode/10-azure-newappblade.png)

6. **만들기**를 클릭합니다.

	![API 앱 블레이드](./media/app-service-create-aspnet-api-app-using-vscode/11-azure-apiappblade.png)

	앱을 만들 때 **시작 보드에 고정** 확인란을 선택된 상태로 변경하지 않은 경우 **홈** 또는 **찾아보기**를 클릭하여 앱의 위치를 쉽게 찾을 수 있습니다. 이 확인란의 선택을 취소한 경우, 왼쪽의 **알림**을 클릭하여 API 앱 만들기 상태를 보고, 알림을 클릭하여 새 API 앱의 블레이드로 이동합니다.

7. **설정 > 응용 프로그램 설정**을 클릭합니다.

8. 액세스 수준을 **공용(익명)**으로 설정합니다.

9. **Save**를 클릭합니다.

	![Azure 앱 설정](./media/app-service-create-aspnet-api-app-using-vscode/12-azure-appsettings.png)

## 새 API 앱에 Git 게시 사용

Git는 Azure 웹 사이트를 배포하는 데 사용할 수 있는 분산된 버전 제어 시스템입니다. API 앱에 대해 작성한 코드를 로컬 Git 리포지토리에 저장하고, 원격 리포지토리로 푸시하여 Azure에 코드를 배포합니다. API 앱은 웹앱을 기반으로 하기 때문에 이 배포 방법은 API 앱에서 사용할 수 있는 앱 서비스 웹앱의 기능입니다. Azure 앱 서비스의 API 앱은 웹 서비스를 호스트하는 추가 기능이 있는 웹앱입니다.

포털에서는 **API 앱** 블레이드에서 API 앱 관련 기능을 관리하고, **API 앱 호스트** 블레이드에서 웹앱과 공유되는 기능을 관리할 수 있습니다. 따라서 이 섹션에서는 **API 앱 호스트** 블레이드로 이동하여 Git 배포 기능을 구성합니다.

1. API 앱 블레이드에서 **API 앱 호스트**를 클릭합니다.

	![Azure API 앱 호스트](./media/app-service-create-aspnet-api-app-using-vscode/13-azure-apiapphost.png)

2. **API 앱** 블레이드의 **배포** 섹션을 찾아 **연속 배포 설정**을 클릭합니다. 블레이드의 이 부분을 보려면 아래로 스크롤해야 할 수도 있습니다.

	![Azure API 앱 호스트](./media/app-service-create-aspnet-api-app-using-vscode/14-azure-deployment.png)

3. **원본 선택 > 로컬 Git 리포지토리**를 클릭합니다.

5. **확인**을 클릭합니다.

	![Azure 로컬 Git 리포지토리](./media/app-service-create-aspnet-api-app-using-vscode/15-azure-localrepository.png)

6. 이전에 API 앱 또는 다른 앱 서비스 앱을 게시하기 위해 배포 자격 증명을 설정하지 않은 경우 지금 설정합니다.

	* **배포 자격 증명 설정**을 클릭합니다.

	* 사용자 이름 및 암호를 만듭니다. 나중에 Git를 설정할 때 이 암호가 필요합니다.

	* **Save**를 클릭합니다.

	![Azure 배포 자격 증명](./media/app-service-create-aspnet-api-app-using-vscode/16-azure-credentials.png)

7. **API 앱 호스트** 블레이드에서 **설정 > 속성**을 클릭합니다. 배포할 원격 Git 리포지토리의 URL이 "GIT URL" 아래에 표시됩니다.

8. 이 자습서의 뒷부분에서 사용하기 위해 **GIT URL**을 복사합니다.

	![Azure Git URL](./media/app-service-create-aspnet-api-app-using-vscode/17-azure-giturl.png)

9. 또한 **API 앱** 블레이드에서 *apiDefinition.swagger.json* 파일의 “host” 값을 업데이트하는 데 사용할 **URL**을 복사합니다.

	![Azure URL](./media/app-service-create-aspnet-api-app-using-vscode/18-azure-url.png)

10. VSCode에서 *apiDefinition.swagger.json* 파일을 열고 “이 부분을 호스트 URL로 교체해야 합니다” 호스트 값을 위 단계에서 복사한 **URL**로 바꿉니다.
11. 또한 호스트 값의 시작 부분에서 "https://" 문자를 제거합니다.
12. 변경 내용을 *apiDefinition.swagger.json* 파일에 저장합니다.

## Azure 앱 서비스에 API 앱 게시

이 섹션에서는 Azure 앱 서비스에서 실행되는 API 앱에 샘플 응용 프로그램을 배포하기 위해 로컬 Git 리포지토리를 만들고 해당 리포지토리에서 Azure에 푸시합니다.

1. VSCode의 왼쪽 탐색 모음에서 Git 옵션을 선택합니다.
2. Git를 아직 설치하지 않은 경우 [Chocolatey](https://chocolatey.org/packages/git) 또는 [git scm.com](http://git-scm.com/downloads) 링크 중 하나를 선택하여 설치합니다. Git를 처음 사용하는 경우 **git-scm.com**을 선택하고 Windows 명령 프롬프트에서 GitBash와 함께 Git를 사용하는 옵션을 선택합니다. 
3. Git가 설치되면 VSCode를 다시 시작하고 왼쪽 막대에서 Git 옵션을 선택합니다.
4. VSCode 코드에서 **git 리포지토리 초기화**를 선택하여 작업 공간이 git 소스 제어 하에 있도록 합니다. 

	![Git 초기화](./media/app-service-create-aspnet-api-app-using-vscode/19-initgit.png)

5. 
6. 커밋 메시지를 추가하고 **모두 커밋** 확인란을 클릭합니다.

	![Git 모두 커밋](./media/app-service-create-aspnet-api-app-using-vscode/20-git-commit.png)

6. **GitBash**를 찾아 엽니다. 대신 Windows 명령 프롬프트를 사용할 수도 있습니다.
7. **GitBash**에서 폴더를 VSCode 프로젝트 폴더로 변경합니다. 예:

	<pre class="prettyprint">
cd c:\VSCodeProjects\ContactsList
</pre>

7. 앞에서 복사한 Git URL(“.git”으로 끝남)을 사용하여 이전에 만든 웹앱(API 앱 호스트)에 업데이트를 푸시하기 위한 원격 참조를 만듭니다.

	<pre class="prettyprint">
git remote add azure [URL for remote repository]
</pre>

8. 다음 명령을 입력하여 변경 내용을 Azure에 푸시합니다.

	<pre class="prettyprint">
git push azure master
</pre>이전에 만든 암호를 입력하라는 메시지가 나타납니다. **참고: 암호는 표시되지 않습니다.**

	위 명령의 출력은 배포에 성공했다는 메시지로 종료됩니다.

	<pre class="prettyprint">
remote: Deployment successful.
To https://user@testsite.scm.azurewebsites.net/testsite.git
[new branch]      master -> master
</pre>

> [AZURE.NOTE]앱을 변경할 경우 VSCode에서 **모두 커밋**을 선택한 다음 **GitBash**에서 **git push azure master** 명령을 입력하여 다시 게시하면 됩니다.

## Azure Preview 포털에서 API 정의 보기
API 앱에 API를 배포했으므로 이제 Azure Preview 포털에서 API 정의를 볼 수 있습니다. 먼저 Azure에서 API 앱의 API 정의가 변경된 것을 인식할 수 있도록 게이트웨이를 다시 시작합니다. 게이트웨이는 리소스 그룹의 API 앱에 대한 API 관리 및 권한 부여를 처리하는 웹앱입니다.

1. Azure Preview 포털에서 이전에 만든 API 앱에 대한 **API 앱** 블레이드로 이동하여 **게이트웨이** 링크를 클릭합니다.
2. **게이트웨이** 블레이드에서 **다시 시작**을 클릭합니다. 이제 이 블레이드를 닫을 수 있습니다.
3. **API 앱** 블레이드에서 **다시 시작**을 클릭합니다. 
4. **API 앱** 블레이드에서 **API 정의**를 클릭합니다.<br> API 정의 블레이드에 두 가지 메서드가 표시됩니다. GET 및 POST 메서드가 바로 표시 되지 않으면, Azure가 앱을 다시 고칠 때까지 몇 초 정도 기다립니다. **API 앱** 블레이드에서 **API 정의**를 클릭합니다.

## Azure에서 앱 실행
Azure Preview 포털에서 API 앱에 대한 **API 앱 호스트** 블레이드로 이동하여 **찾아보기**를 클릭합니다. 그런 다음 **api/Contacts**를 URL의 끝에 추가하면 연락처 세부 정보를 볼 수 있습니다.


## 결론
이 자습서에서는 Visual Studio Code에서 API 앱을 만드는 방법을 알아보았습니다. Visual Studio Code에 대한 자세한 내용은 [Visual Studio Code](https://code.visualstudio.com/Docs/)를 참조하세요. API 앱에 대한 자세한 내용은 [API 앱 정의](app-service-api-apps-why-best-platform.md)를 참조하세요.
 

<!---HONumber=August15_HO8-->