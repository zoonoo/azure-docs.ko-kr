<properties
	pageTitle="엔터프라이즈 SaaS에 모바일 앱 연결 | 모바일 개발자 센터"
	description="SharePoint Online과 같은 엔터프라이즈 리소스를 호출하는 방법을 알아봅니다."
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="06/19/2015"
	ms.author="mahender"/>

# SaaS API에 모바일 앱 연결

이 자습서에서는 모바일 앱을 엔터프라이즈 SaaS(Software-as-a-Service) 솔루션에 연결합니다. 새 TodoItem이 추가될 때마다 SharePoint Online에서 Word 문서를 만들도록 [Active Directory 인증 라이브러리 Single Sign-On을 사용하여 앱 인증]에 따라 앱을 업데이트합니다.

이 자습서를 사용하려면 다음이 필요합니다.

* Windows 8.1에서 실행 중인 Visual Studio 2013
* 활성 [SharePoint Online] 구독
* [Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증] 자습서 완료. SharePoint 구독으로 제공된 테넌트를 사용해야 합니다.

## <a name="configure-permissions"></a>SharePoint에 대한 위임된 액세스를 위한 응용 프로그램 구성
기본적으로 AAD로부터 받은 토큰은 권한이 제한되어 있습니다. 타사 리소스 또는 SharePoint Online과 같은 SaaS 응용 프로그램에 액세스하려면 이를 명시적으로 허용해야 합니다.

1. **Azure 관리 포털**의 [Active Directory] 섹션에서 테넌트를 선택합니다. 앱 서비스용으로 만든 웹 응용 프로그램으로 이동합니다.

2. **구성** 탭에서 페이지를 다른 응용 프로그램에 대한 권한 섹션으로 스크롤합니다. **Office 365 SharePoint Online**을 선택하고 **사용자 파일 편집 또는 삭제** 위임된 권한을 부여합니다. 그런 다음 **Save**를 클릭합니다.

    ![][1]

이제 앱 서비스에 대해 SharePoint 액세스 토큰을 발급하도록 AAD를 구성했습니다.

## <a name="store-credentials"></a>모바일 앱에 SharePoint 정보 추가

SharePoint를 호출하려면 모바일 앱이 연결할 끝점을 지정해야 합니다. 또한 앱 서비스의 ID를 입증할 수 있어야 합니다. 이러한 작업은 클라이언트 ID 및 클라이언트 암호 쌍을 사용해서 수행됩니다. 앱 서비스에 대한 클라이언트 ID는 AAD 로그인 설정 중에 이미 가져와서 저장했습니다. 이러한 ID는 민감한 자격 증명이므로 코드에 일반 텍스트로 저장해서는 안 됩니다. 대신 모바일 앱 코드 사이트에 대한 응용 프로그램 설정으로 이러한 값을 설정합니다.

1. 테넌트에 대한 AAD 응용 프로그램 탭으로 돌아가 앱 서비스에 대한 웹 응용 프로그램을 선택합니다.

2. 구성 아래에서 키로 스크롤합니다. 클라이언트 암호는 새 키를 생성하여 얻을 수 있습니다. 키를 만들고 페이지를 벗어나면 포털에서 키를 제거할 수 있는 방법이 없습니다. 키를 만든 다음에는 이 값을 복사해서 안전한 위치에 저장해야 합니다. 키 사용 기간을 선택한 후 저장을 클릭하고 결과 값을 복사합니다.

3. 관리 포털의 모바일 앱 코드 섹션에서 구성 탭으로 이동한 다음 아래의 앱 설정으로 스크롤합니다. 여기에서는 필요한 자격 증명을 참조할 수 있도록 키-값 쌍을 제공할 수 있습니다.

* SP_Authority를 AAD 테넌트에 대한 기관 끝점으로 설정합니다. 이 항목은 클라이언트 앱에 사용되는 기관 값과 동일해야 합니다. `https://login.windows.net/contoso.onmicrosoft.com` 형식이 됩니다.

* SP_ClientSecret를 이전에 얻은 클라이언트 암호 값으로 설정합니다.

* SP_SharePointURL을 SharePoint 사이트에 대한 URL로 설정합니다. 형식은 `https://contoso-my.sharepoint.com`이어야 합니다.

이러한 값은 ApiServices.Settings를 사용하여 코드에서 다시 가져올 수 있습니다.

## <a name="obtain-token"></a>액세스 토큰 가져오기 및 SharePoint API 호출

SharePoint에 액세스하려면 대상으로서 SharePoint에 대한 특별 액세스 토큰이 필요합니다. 이 토큰을 가져오려면 앱 서비스의 ID 및 사용자에 대해 발급된 토큰을 사용해서 AAD로 콜백해야 합니다.

1. Visual Studio에서 모바일 앱 코드 프로젝트를 엽니다.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. NuGet 패키지 관리자에서 **온라인**을 클릭합니다. **Microsoft.Azure.Mobile.Server.AppService**를 검색 단어로 입력합니다. **설치**를 클릭하여 [모바일 앱 .NET 백 엔드 앱 서비스 확장] 패키지를 설치합니다. 이 패키지는 현재 로그인한 사용자에 대한 정보를 사용하기 위한 확장 메서드를 제공합니다.

2. 모바일 앱 코드 프로젝트에서 SharePointUploadContext라는 새로운 클래스를 만듭니다. `using Microsoft.Azure.Mobile.Server.AppService;` 문을 파일에 추가합니다. 그런 다음 클래스에 다음 코드를 추가합니다.

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web";
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. 이제 사용자의 문서 라이브러리에 파일을 추가하기 위한 메서드를 만듭니다.

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Word 문서 만들기 및 업로드

Word 문서를 만들려면 OpenXML NuGet 패키지를 사용합니다. NuGet 관리자를 열고 DocumentFormat.OpenXml을 검색하여 이 패키지를 설치합니다.

1. TodoItemController에 다음 코드를 추가합니다. 그러면 TodoItem 기반의 Word 문서가 생성됩니다. 문서 텍스트는 항목의 이름입니다.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. PostTodoItem을 다음으로 바꿉니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>응용 프로그램 테스트

1. 변경 내용을 백 엔드에 게시한 후 클라이언트 응용 프로그램을 실행합니다. 메시지가 표시되면 로그인하고 새 TodoItem을 삽입합니다.

2. SharePoint 사이트로 이동하고 동일한 사용자를 사용해서 로그인합니다.

3. OneDrive 탭을 선택합니다. 문서 폴더에서 GUID 제목이 있는 Word 문서를 찾을 수 있습니다. 문서를 열면 TodoItem에 대한 텍스트가 표시됩니다.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[Active Directory]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/ko-kr/sharepoint/
[Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[Active Directory 인증 라이브러리 Single Sign-On을 사용하여 앱 인증]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[모바일 앱 .NET 백 엔드 앱 서비스 확장]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/
 

<!---HONumber=July15_HO4-->