---
title: "ASP.NET 및 SQL DB를 사용하여 Azure에서 REST API 만들기 | Microsoft Docs"
description: "Visual Studio를 사용하여 Azure 웹 앱에 ASP.NET Web API를 사용하는 앱을 배포하는 방법에 대해 설명하는 자습서입니다."
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
writer: Rick-Anderson
manager: erikre
editor: 
ms.assetid: f4916fc0-ea08-41f7-846b-73e41bc88149
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/29/2016
ms.author: riande
translationtype: Human Translation
ms.sourcegitcommit: 3f6bb2604d6f20e299ae5583a0ad215f02b9b803
ms.openlocfilehash: 016d44cb57d5ad76dc0b115be6fbac6835d84396
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-rest-service-using-aspnet-web-api-and-sql-database-in-azure-app-service"></a>Azure 앱 서비스에서 ASP.NET Web API 및 SQL 데이터베이스를 사용하여 REST 서비스 만들기
이 자습서에서는 Visual Studio 2013 또는 Visual Studio 2013 Community Edition의 웹 게시 마법사를 사용하여 ASP.NET 웹앱을 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 에 배포하는 방법을 보여 줍니다. 

Azure 계정은 무료로 개설할 수 있으며, Visual Studio 2013이 아직 없는 경우 SDK에서 Web Express용 Visual Studio 2013을 자동으로 설치합니다. 따라서 Azure용 개발을 무료로 시작할 수 있습니다.

이 자습서에서는 이전에 Azure를 사용한 경험이 없다고 가정합니다. 이 자습서를 완료하면 클라우드에서 간단한 웹 앱을 실행할 수 있습니다.

다음 내용을 배웁니다.

* Azure SDK를 설치하여 사용자 컴퓨터에서 Azure를 개발할 수 있도록 하는 방법
* Visual Studio ASP.NET MVC 5 프로젝트를 만들고 Azure 웹 앱에 게시하는 방법
* ASP.NET Web API를 사용하여 RESTful API 호출을 사용하도록 설정하는 방법
* SQL 데이터베이스를 사용하여 Azure에 데이터를 저장하는 방법
* 응용 프로그램 업데이트를 Azure에 게시하는 방법

ASP.NET MVC 5에서 빌드되고 데이터베이스 액세스에 ADO.NET Entity Framework를 사용하는 간단한 연락처 목록 웹 응용 프로그램을 빌드합니다. 다음 그림에서는 완료된 응용 프로그램을 보여 줍니다.

![웹 사이트의 스크린샷][intro001]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### <a name="create-the-project"></a>프로젝트 만들기
1. Visual Studio 2013을 시작합니다.
2. **파일** 메뉴에서 **새 프로젝트**를 클릭합니다.
3. **새 프로젝트** 대화 상자에서 **Visual C#**을 확장하고 **웹**을 선택한 다음 **ASP.NET 웹 응용 프로그램**을 선택합니다. 응용 프로그램 이름을 **ContactManager** 로 지정하고 **확인**를 클릭합니다.
   
    ![새 프로젝트 대화 상자](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.png)
4. **새 ASP.NET 프로젝트** 대화 상자에서 **MVC** 템플릿을 선택하고 **Web API**를 선택한 후 **인증 변경**을 클릭합니다.
5. **인증 변경** 대화 상자에서 **인증 없음**, **확인**을 차례로 클릭합니다.
   
    ![인증 없음](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)
   
    여기서 만드는 샘플 응용 프로그램에는 사용자 로그인을 필요로 하는 기능이 없습니다. 인증 및 권한 부여 기능을 구현하는 방법에 대한 자세한 내용은 이 자습서 끝에 있는 [다음 단계](#nextsteps) 섹션을 참조하세요. 
6. **새 ASP.NET 프로젝트** 대화 상자에서 **클라우드에서 호스트**를 선택했는지 확인하고 **확인**을 클릭합니다.

이전에 Azure에 로그인한 적이 없는 경우 로그인하라는 메시지가 나타납니다.

1. 구성 마법사에서 *ContactManager* 를 기준으로 고유한 이름을 제안합니다(아래 이미지 참조). 근처에 있는 지역을 선택합니다. [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") 을 사용하여 대기 시간이 가장 짧은 데이터 센터를 찾을 수 있습니다. 
2. 기존에 만든 데이터베이스 서버가 없으면 **새 서버 만들기**를 선택하고 데이터베이스 사용자 이름과 암호를 입력합니다.
   
    ![Azure 웹 사이트 구성](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

데이터베이스 서버가 있는 경우 해당 서버를 사용하여 새 데이터베이스를 만듭니다. 데이터베이스 서버는 중요한 리소스이며, 일반적으로 데이터베이스별로 데이터베이스 서버를 만들기보다는 테스트 및 개발을 위해 같은 서버에서 여러 데이터베이스 만들려고 합니다. 웹 사이트 및 데이터베이스가 동일한 지역에 있어야 합니다.

![Azure 웹 사이트 구성](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### <a name="set-the-page-header-and-footer"></a>페이지 머리글 및 바닥글 설정
1. **솔루션 탐색기**에서 *Views\Shared* 폴더를 확장하고 *_Layout.cshtml* 파일을 엽니다.
   
    ![솔루션 탐색기의 _Layout.cshtml][newapp004]
2. *Views\Shared_Layout.cshtml* 파일 내용을 다음 코드로 바꿉니다.

        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="utf-8" />
            <title>@ViewBag.Title - Contact Manager</title>
            <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
            <meta name="viewport" content="width=device-width" />
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
        </head>
        <body>
            <header>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
                    </div>
                </div>
            </header>
            <div id="body">
                @RenderSection("featured", required: false)
                <section class="content-wrapper main-content clear-fix">
                    @RenderBody()
                </section>
            </div>
            <footer>
                <div class="content-wrapper">
                    <div class="float-left">
                        <p>&copy; @DateTime.Now.Year - Contact Manager</p>
                    </div>
                </div>
            </footer>
            @Scripts.Render("~/bundles/jquery")
            @RenderSection("scripts", required: false)
        </body>
        </html>

위의 변경 내용은 앱 이름을 "My ASP.NET App"에서 "Contact Manager"로 변경하고 **홈**, **정보** 및 **연락처**에 대한 링크를 제거합니다.

### <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
1. Ctrl+F5를 눌러 응용 프로그램을 실행합니다.
   응용 프로그램 홈페이지가 기본 브라우저에 나타납니다.
    ![할 일 모음 홈 페이지](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.png)

Azure에 배포할 응용 프로그램을 만들기 위해 지금 수행해야 하는 작업은 이것뿐입니다. 나중에 데이터베이스 기능을 추가하겠습니다.

## <a name="deploy-the-application-to-azure"></a>Azure에 응용 프로그램 배포
1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **게시**를 선택합니다.
   
    ![프로젝트 상황에 맞는 메뉴의 게시][PublishVSSolution]
   
    **웹 게시** 마법사가 열립니다.
2. **게시**를 클릭합니다.

![설정 탭](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

Visual Studio에서 Azure 서버로 파일을 복사하는 프로세스를 시작합니다. **출력** 창에 수행된 배포 작업이 표시되고 성공적인 배포 완료가 보고됩니다.

1. 배포된 사이트의 URL이 기본 브라우저에서 자동으로 열립니다.
   
   만든 응용 프로그램이 이제 클라우드에서 실행되고 있습니다.
   
   ![Azure에서 실행하는 할 일 모음 홈 페이지][rxz2]

## <a name="add-a-database-to-the-application"></a>응용 프로그램에 데이터베이스 추가
이제 MVC 응용 프로그램을 업데이트하여 연락처를 표시 및 업데이트하고 데이터베이스에 데이터를 저장하는 기능을 추가하겠습니다. 응용 프로그램은 Entity Framework를 사용하여 데이터베이스를 만들며 데이터베이스에서 데이터를 읽고 업데이트합니다.

### <a name="add-data-model-classes-for-the-contacts"></a>연락처에 대한 데이터 모델 클래스 추가
먼저 코드로 간단한 데이터 모델을 만듭니다.

1. **솔루션 탐색기**에서 모델 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.
   
    ![모델 폴더 상황에 맞는 메뉴의 클래스 추가][adddb001]
2. **새 항목 추가** 대화 상자에서 새 클래스 파일의 이름을 *Contact.cs*로 지정하고 **추가**를 클릭합니다.
   
    ![새 항목 추가 대화 상자][adddb002]
3. Contacts.cs 파일 내용을 다음 코드로 바꿉니다.
   
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
               {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                public string Email { get; set; }
                public string Twitter { get; set; }
                public string Self
                {
                    get { return string.Format(CultureInfo.CurrentCulture,
                         "api/contacts/{0}", this.ContactId); }
                    set { }
                }
            }
        }

**Contact** 클래스는 각 연락처에 대해 저장할 데이터와 데이터베이스에 필요한 기본 키 ContactID를 정의합니다. 이 자습서의 후반부에 있는 [다음 단계](#nextsteps) 섹션에서 데이터 모델 관련 정보를 추가로 확인할 수 있습니다.

### <a name="create-web-pages-that-enable-app-users-to-work-with-the-contacts"></a>앱 사용자가 연락처 작업을 수행할 수 있는 웹 페이지 만들기
ASP.NET MVC 스캐폴딩 기능은 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 코드를 자동으로 생성할 수 있습니다.

## <a name="add-a-controller-and-a-view-for-the-data"></a>데이터에 대한 컨트롤러 및 뷰 추가
1. **솔루션 탐색기**에서 컨트롤러 폴더를 확장합니다.
2. 프로젝트를 빌드합니다 **(Ctrl+Shift+B)**. 스캐폴딩 메커니즘을 사용하기 전에 프로젝트를 빌드해야 합니다. 
3. 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러**를 클릭합니다.
   
    ![컨트롤러 폴더 상황에 맞는 메뉴의 컨트롤러 추가][addcode001]
4. **스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러(뷰 포함), Entity Framework 사용**을 선택하고 **추가**를 클릭합니다.
   
   ![컨트롤러 추가](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.png)
5. 컨트롤러 이름을 **HomeController**로 설정합니다. 모델 클래스로 **Contact** 를 선택합니다. **새 데이터 컨텍스트** 단추를 클릭하고 **새 데이터 컨텍스트 형식**으로 기본값인 "ContactManager.Models.ContactManagerContext"를 수락합니다. **추가**를 클릭합니다.

    "HomeController라는 파일이 이미 있습니다. 바꾸시겠습니까?"와 같은 메시지가 나타납니다. **예**를 클릭합니다. 새 프로젝트로 만들었던 Home Controller를 덮어쓰겠습니다. 연락처 목록에 새 Home Controller를 사용합니다.

    Visual Studio에서 **Contact** 개체에 대한 CRUD 데이터베이스 작업의 컨트롤러 메서드와 뷰를 만듭니다.

## <a name="enable-migrations-create-the-database-add-sample-data-and-a-data-initializer"></a>마이그레이션 사용, 데이터베이스 만들기, 샘플 데이터 및 데이터 이니셜라이저 추가
다음 작업은 만든 데이터 모델에 따라 데이터베이스를 만들기 위해 [Code First 마이그레이션](http://curah.microsoft.com/55220) (영문) 기능을 사용하도록 설정하는 것입니다.

1. **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다.
   
    ![도구 메뉴의 패키지 관리자 콘솔][addcode008]
2. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
   
        enable-migrations 
   
    **enable-migrations** 명령은 *Migrations* 폴더를 만들고 해당 폴더에 *Configuration.cs* 파일을 넣습니다. 이 파일을 편집하여 마이그레이션을 구성할 수 있습니다. 
3. **패키지 관리자 콘솔** 창에서 다음 명령을 입력합니다.
   
        add-migration Initial
   
    **add-migration Initial** 명령은 데이터베이스를 만드는 **&lt;date_stamp&gt;Initial**이라는 클래스를 생성합니다. 첫 번째 매개 변수( *Initial* )는 임의이며 파일 이름을 만드는 데 사용됩니다. **솔루션 탐색기**에서 새 클래스 파일을 볼 수 있습니다.
   
    **Initial** 클래스의 **Up** 메서드는 Contacts 테이블을 만들고 이전 상태로 돌아가려는 경우 사용되는 **Down** 메서드는 테이블을 삭제합니다.
4. *Migrations\Configuration.cs* 파일을 엽니다. 
5. 다음 네임스페이스를 추가합니다. 
   
         using ContactManager.Models;
6. *Seed* 메서드를 다음 코드로 바꿉니다.
   
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }
   
    이 코드는 연락처 정보가 있는 데이터베이스를 초기화합니다. 데이터베이스 시드에 대한 자세한 내용은 [EF(Entity Framework) DB 디버그](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)(영문)를 참조하십시오.
7. **패키지 관리자 콘솔** 에서 다음 명령을 입력합니다.
   
        update-database
   
    ![패키지 관리자 콘솔 명령][addcode009]
   
    **update-database** 는 데이터베이스를 만드는 첫 번째 마이그레이션을 실행합니다. 기본적으로 데이터베이스는 SQL Server Express LocalDB 데이터베이스로 생성됩니다.
8. Ctrl+F5를 눌러 응용 프로그램을 실행합니다. 

응용 프로그램에서 시드 데이터를 표시하고 편집, 세부 정보 및 삭제 링크를 제공합니다.

![MVC 데이터 뷰][rxz3]

## <a name="edit-the-view"></a>뷰 편집
1. *Views\Home\Index.cshtml* 파일을 엽니다. 다음 단계에서는 생성된 변경 내용을 [jQuery](http://jquery.com/) 및 [Knockout.js](http://knockoutjs.com/)를 사용하는 코드로 바꿀 것입니다. 이 새 코드는 웹 API 및 JSON을 사용하여 연락처 목록을 가져온 후 knockout.js를 사용하여 연락처 데이터를 UI에 바인딩합니다. 자세한 내용은 이 자습서의 후반부에서 [다음 단계](#nextsteps) 섹션을 참조하세요. 
2. 파일 내용을 다음 코드로 바꿉니다.
   
        @model IEnumerable<ContactManager.Models.Contact>
        @{
            ViewBag.Title = "Home";
        }
        @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                function ContactsViewModel() {
                    var self = this;
                    self.contacts = ko.observableArray([]);
                    self.addContact = function () {
                        $.post("api/contacts",
                            $("#addContact").serialize(),
                            function (value) {
                                self.contacts.push(value);
                            },
                            "json");
                    }
                    self.removeContact = function (contact) {
                        $.ajax({
                            type: "DELETE",
                            url: contact.Self,
                            success: function () {
                                self.contacts.remove(contact);
                            }
                        });
                    }
   
                    $.getJSON("api/contacts", function (data) {
                        self.contacts(data);
                    });
                }
                ko.applyBindings(new ContactsViewModel());    
        </script>
        }
        <ul id="contacts" data-bind="foreach: contacts">
            <li class="ui-widget-content ui-corner-all">
                <h1 data-bind="text: Name" class="ui-widget-header"></h1>
                <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
                <div>
                    <span data-bind="text: $data.City || 'City?'"></span>,
                    <span data-bind="text: $data.State || 'State?'"></span>
                    <span data-bind="text: $data.Zip || 'Zip?'"></span>
                </div>
                <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
                <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
                <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
                <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
                <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
            </li>
        </ul>
        <form id="addContact" data-bind="submit: addContact">
            <fieldset>
                <legend>Add New Contact</legend>
                <ol>
                    <li>
                        <label for="Name">Name</label>
                        <input type="text" name="Name" />
                    </li>
                    <li>
                        <label for="Address">Address</label>
                        <input type="text" name="Address" >
                    </li>
                    <li>
                        <label for="City">City</label>
                        <input type="text" name="City" />
                    </li>
                    <li>
                        <label for="State">State</label>
                        <input type="text" name="State" />
                    </li>
                    <li>
                        <label for="Zip">Zip</label>
                        <input type="text" name="Zip" />
                    </li>
                    <li>
                        <label for="Email">E-mail</label>
                        <input type="text" name="Email" />
                    </li>
                    <li>
                        <label for="Twitter">Twitter</label>
                        <input type="text" name="Twitter" />
                    </li>
                </ol>
                <input type="submit" value="Add" />
            </fieldset>
        </form>
3. Content 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **새 항목...**을 클릭합니다.
   
    ![Content 폴더의 상황에 맞는 메뉴에서 스타일시트 추가][addcode005]
4. **새 항목 추가** 대화 상자에서 오른쪽 위에 있는 검색 상자에 **스타일**을 입력하고 **스타일 시트**를 선택합니다.
    ![새 항목 추가 대화 상자][rxStyle]
5. 파일 이름을 *Contacts.css* 로 지정하고 **추가**를 클릭합니다. 파일 내용을 다음 코드로 바꿉니다.
   
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }
   
    Contact Manager 앱에 사용되는 레이아웃, 색 및 스타일에 이 스타일시트를 사용하겠습니다.
6. *App_Start\BundleConfig.cs* 파일을 엽니다.
7. 다음 코드를 추가하여 [Knockout](http://knockoutjs.com/index.html "KO") 플러그인을 등록합니다.
   
        bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
                    "~/Scripts/knockout-{version}.js"));
    knockout을 사용하는 이 샘플은 차단 템플릿을 처리하는 동적 JavaScript 코드를 간소화합니다.
8. contents/css 항목을 수정하여 *contacts.css* 스타일시트를 등록합니다. 다음 줄을
   
                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
   아래와 같이 변경합니다.
   
        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));
9. 패키지 관리자 콘솔에서 다음 명령을 실행하여 Knockout을 설치합니다.
   
        Install-Package knockoutjs

## <a name="add-a-controller-for-the-web-api-restful-interface"></a>Web API RESTful 인터페이스용 컨트롤러 추가
1. **솔루션 탐색기**에서 컨트롤러를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **컨트롤러...**를 클릭합니다. 
2. **스캐폴드 추가** 대화 상자에서 **Web API 2 컨트롤러(작업 포함), Entity Framework 사용**을 입력하고 **추가**를 클릭합니다.
   
    ![API 컨트롤러 추가](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.png)
3. **컨트롤러 추가** 대화 상자에서 컨트롤러 이름으로 "ContactsController"를 입력합니다. **모델 클래스**에 대해 "Contact(ContactManager.Models)"를 선택합니다.  **데이터 컨텍스트 클래스**에 대한 기본값은 그대로 유지합니다. 
4. **추가**를 클릭합니다.

### <a name="run-the-application-locally"></a>로컬에서 응용 프로그램 실행
1. Ctrl+F5를 눌러 응용 프로그램을 실행합니다.
   
    ![인덱스 페이지][intro001]
2. 연락처를 입력하고 **추가**를 클릭합니다. 앱이 홈 페이지로 돌아가고 입력한 연락처가 표시됩니다.
   
    ![할 일 모음 항목이 있는 인덱스 페이지][addwebapi004]
3. 브라우저에서 URL 끝에 **/api/contacts** 를 추가합니다.
   
    이에 따라 표시되는 URL은 http://localhost:1234/api/contacts와 유사합니다. 추가한 RESTful Web API에서 저장된 연락처가 반환됩니다. Firefox 및 Chrome은 XML 형식으로 데이터를 표시합니다.
   
    ![할 일 모음 항목이 있는 인덱스 페이지][rxFFchrome]

    IE에는 연락처를 열거나 저장할지를 묻는 메시지가 표시됩니다.

    ![Web API 저장 대화 상자][addwebapi006]


    반환된 연락처는 메모장 또는 브라우저에서 열 수 있습니다.

    이 출력은 모바일 웹 페이지나 모바일 응용 프로그램 같은 다른 응용 프로그램에서 사용될 수 있습니다.

    ![Web API 저장 대화 상자][addwebapi007]

    **보안 경고**: 이 단계에서 응용 프로그램은 CSRF 공격에 취약하고 보안되지 않는 상태입니다. 이 취약성은 자습서의 뒷부분에서 제거하겠습니다. 자세한 내용은 [CSRF(교차 사이트 요청 위조) 공격 예방][prevent-csrf-attacks]을 참조하세요.
## <a name="add-xsrf-protection"></a>XSRF 보호 추가
XSRF 또는 CSRF라고도 하는 교차 사이트 요청 위조는 웹 호스팅 응용 프로그램을 공격하며, 이 공격을 통해 악성 웹 사이트는 해당 브라우저가 신뢰하는 클라이언트 브라우저와 웹 사이트 간의 상호 작용에 영향을 미칠 수 있습니다. 이러한 공격은 웹 브라우저가 인증 토큰을 각 요청과 함께 웹 사이트에 자동으로 보내기 때문에 가능합니다. 정식 예로는 ASP.NET의 폼 인증 티켓과 같은 인증 쿠키가 있습니다. 하지만 Windows 인증, 기본 인증 등 영구적 인증 메커니즘을 사용하는 웹 사이트가 이러한 공격의 대상이 될 수 있습니다.

XSRF 공격은 피싱 공격과는 구분됩니다. 피싱 공격에는 피해자의 상호 작용이 필요합니다. 피싱 공격에서 악성 웹 사이트는 대상 웹 사이트를 가장하고 피해자는 공격자에게 중요 정보를 제공하는 실수를 저지르게 됩니다. XSRF 공격에서는 종종 피해자의 상호 작용이 필요하지 않습니다. 대신, 공격자는 대상 웹 사이트에 모든 관련 쿠키를 자동으로 보내는 브라우저를 사용합니다.

자세한 내용은 OWASP([Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page)) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_\(CSRF\))를 참조하세요.

1. **솔루션 탐색기**에서 **ContactManager** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**를 클릭한 후 **클래스**를 클릭합니다.
2. 파일 이름을 *ValidateHttpAntiForgeryTokenAttribute.cs* 로 지정하고 다음 코드를 추가합니다.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
                    IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }
3. *[ValidateHttpAntiForgeryToken]* 특성에 대한 액세스 권한을 받을 수 있도록 다음 **using** 문을 연락처 컨트롤러에 추가합니다.
   
        using ContactManager.Filters;
4. XSRF 위협으로부터 보호할 수 있도록 **ContactsController**의 Post 메서드에 **[ValidateHttpAntiForgeryToken]** 특성을 추가합니다. "PutContact", "PostContact" 및 **DeleteContact** 작업 메서드에 이 특성을 추가하겠습니다.
   
        [ValidateHttpAntiForgeryToken]
            public IHttpActionResult PutContact(int id, Contact contact)
            {
5. *Views\Home\Index.cshtml* 파일의 *스크립트* 섹션을 업데이트하여 XSRF 토큰을 가져오는 코드를 포함합니다.
   
         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }
   
               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {
   
                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });
   
                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
   
                     });
                  }
   
                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>
         }

## <a name="publish-the-application-update-to-azure-and-sql-database"></a>Azure 및 SQL 데이터베이스에 응용 프로그램 업데이트 게시
응용 프로그램을 게시하려면 이전에 따랐던 절차를 반복합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
   
    ![게시][rxP]
2. **설정** 탭을 클릭합니다.
3. **ContactsManagerContext(ContactsManagerContext)**에서 **v** 아이콘을 클릭하여 *원격 연결 문자열*을 연락처 데이터베이스에 대한 연결 문자열로 변경합니다. **ContactDB**를 클릭합니다.
   
    ![설정](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)
4. **Execute Code First Migrations (runs on application start)**확인란을 선택합니다.
5. **다음**을 클릭한 후 **미리 보기**를 클릭합니다. Visual Studio에 추가 또는 업데이트될 파일 목록이 표시됩니다.
6. **게시**를 클릭합니다.
   배포가 완료된 후 브라우저에 응용 프로그램의 홈 페이지가 열립니다.
   
    ![연락처가 없는 인덱스 페이지][intro001]
   
    Visual Studio 게시 프로세스는 배포된 *Web.config* 파일의 연결 문자열을 자동으로 구성하여 SQL 데이터베이스를 가리켰습니다. 또한 배포 후 응용 프로그램이 처음 데이터베이스에 액세스할 때 데이터베이스를 최신 버전으로 자동 업그레이드하도록 Code First 마이그레이션을 구성했습니다.
   
    이러한 구성으로 인해 Code First는 이전에 만든 **Initial** 클래스의 코드를 실행하여 데이터베이스를 만들었습니다. 이는 배포 후 응용 프로그램이 데이터베이스에 처음 액세스하려 할 때 수행되었습니다.
7. 로컬에서 앱을 실행할 때 입력한 연락처를 입력하여 데이터베이스 배포가 성공했는지 확인합니다.

입력한 항목이 저장되어 Contact Manager 페이지에 나타나면 해당 항목은 데이터베이스에 저장된 것입니다.

![연락처가 있는 인덱스 페이지][addwebapi004]

이제 응용 프로그램이 클라우드에서 실행되고 데이터를 저장하는 데 SQL 데이터베이스가 사용됩니다. Azure에서 응용 프로그램 테스트를 마치면 해당 응용 프로그램을 삭제해야 합니다. 응용 프로그램이 공개될 뿐 아니라 액세스를 제한할 메커니즘이 없기 때문입니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://azure.microsoft.com/try/app-service/)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
실제 응용 프로그램에는 인증 및 권한 부여가 필요할 수 있으므로 멤버 자격 데이터베이스를 사용할 수 있습니다. [OAuth, 멤버 자격 및 SQL 데이터베이스를 사용하여 안전한 ASP.NET MVC 응용 프로그램 배포](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) (영문) 자습서는 현재 자습서를 기반으로 작성되었으며, 멤버 자격 데이터베이스를 사용하여 웹 응용 프로그램을 배포하는 방법을 설명합니다.

Azure 응용 프로그램에 데이터를 저장하는 또 다른 방법은 Azure 저장소를 사용하는 것입니다. Azure 저장소는 비관계형 데이터 저장소를 Blob 및 테이블 형식으로 제공합니다. Web API, ASP.NET MVC 및 Window Azure에 대한 자세한 내용은 다음 링크를 참조하십시오.

* [MVC를 사용하여 Entity Framework 시작][EFCodeFirstMVCTutorial]
* [ASP.NET MVC 5 소개(영문)](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [최초 ASP.NET 앱 API](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [WAWS 디버그](web-sites-dotnet-troubleshoot-visual-studio.md)

이 자습서 및 샘플 응용 프로그램은 [Rick Anderson](http://blogs.msdn.com/b/rickandy/)(Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))에 의해 작성되었으며, Tom Dykstra 및 Barry Dorrans(Twitter [@blowdart](https://twitter.com/blowdart))의 도움을 받았습니다. 

자습서 자체뿐 아니라 설명된 제품과 관련해서 좋아한 사항이나 바라는 개선 사항에 대한 의견을 남겨주십시오. 사용자 의견은 개선 사항의 우선 순위를 지정하는 데 도움이 됩니다. 특히 멤버 자격 데이터베이스를 구성하고 배포하는 프로세스 자동화에 대한 사용자의 관심도가 어느 정도인지 파악하는 데 유용합니다. 

## <a name="whats-changed"></a>변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]:#mbrDB
[Create a Data Deployment Script]:#ppd
[Update the Membership Database]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks


