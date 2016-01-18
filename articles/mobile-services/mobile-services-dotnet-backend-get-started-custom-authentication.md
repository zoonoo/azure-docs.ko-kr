<properties
	pageTitle="사용자 지정 인증 시작 | Microsoft Azure"
	description="사용자 이름 및 암호를 사용하여 사용자를 인증하는 방법에 대해 알아봅니다."
	documentationCenter="Mobile"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="mahender"/>

# 사용자 지정 인증 시작

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


## 개요
이 항목에서는 고유 모바일 서비스 인증 토큰을 발행하여 Azure 모바일 서비스 .NET 백 엔드에서 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱에 대해 사용자 지정 사용자 이름 및 암호를 사용해서 빠른 시작 프로젝트에 인증을 추가합니다.

>[AZURE.NOTE]이 자습서에서는 사용자 지정 자격 증명을 사용해서 모바일 서비스를 인증하는 고급 방법을 보여 줍니다. 많은 앱에서는 기본 제공되는 소셜 ID 공급자를 대신 사용하여 Facebook, Twitter, Google, Microsoft 계정 및 Azure Active Directory를 통해 로그인할 수 있도록 허용하는 것이 가장 적합할 것입니다. 모바일 서비스에서 인증 관련 작업을 처음 수행할 경우에는 [앱에 인증 추가] 자습서를 참조하세요.

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

>[AZURE.IMPORTANT]이 자습서에서는 모바일 서비스에 대한 인증 토큰을 발행하는 방법을 보여 줍니다. 이 자습서는 보안 지침으로 간주되지 않습니다. 앱 개발 시에는 암호 저장소가 보안에 미치는 영향을 인지하고 무차별 암호 대입 공격(brute-force attack)을 관리하기 위한 전략을 마련해야 합니다.

## 계정 테이블 설정

사용자 지정 인증을 사용 중이고 다른 ID 공급자를 사용하지 않기 때문에 사용자 로그인 정보를 저장할 필요가 있습니다. 이 섹션에서는 계정에 대한 테이블을 만들고 기본 보안 메커니즘을 설정합니다. 계정 테이블에는 사용자 이름과 솔트된/해시된 암호가 포함되며, 필요한 경우 추가 사용자 정보도 포함할 수 있습니다.

1. 백 엔드 프로젝트의 **DataObjects** 폴더에서 `Account`(이)라는 새 엔터티를 추가합니다.

2. 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service;

3. 클래스 정의를 다음 코드로 바꿉니다.

	    public class Account : EntityData
	    {
	        public string Username { get; set; }
	        public byte[] Salt { get; set; }
	        public byte[] SaltedAndHashedPassword { get; set; }
	    }

    이것은 사용자 이름, 해당 사용자의 솔트 및 안전하게 저장된 암호를 포함하는 새 계정 테이블의 행을 나타냅니다.

2. **Models** 폴더 아래에는 모바일 서비스 이름에 따라 지정된 **DbContext** 클래스가 있습니다. 컨텍스트를 열고 다음을 포함하여 데이터 모델에 계정 테이블을 추가합니다.

        public DbSet<Account> Accounts { get; set; }

	>[AZURE.NOTE]이 자습서 사용에서 코드 조각은 컨텍스트 이름으로 `todoContext`을(를) 사용합니다. 프로젝트의 컨텍스트에 대한 코드 조각을 업데이트해야 합니다. &nbsp; 다음으로,이 데이터로 작업하기 위한 보안 기능을 설정합니다.

5. `CustomLoginProviderUtils`(이)라는 클래스를 만들고 여기에 다음 `using` 문을 추가합니다.

		using System.Security.Cryptography;

6. 새 클래스에 다음 코드 메서드를 추가합니다.


        public static byte[] hash(string plaintext, byte[] salt)
        {
            SHA512Cng hashFunc = new SHA512Cng();
            byte[] plainBytes = System.Text.Encoding.ASCII.GetBytes(plaintext);
            byte[] toHash = new byte[plainBytes.Length + salt.Length];
            plainBytes.CopyTo(toHash,0);
            salt.CopyTo(toHash, plainBytes.Length);
            return hashFunc.ComputeHash(toHash);
        }

        public static byte[] generateSalt()
        {
            RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider();
            byte[] salt = new byte[256];
            rng.GetBytes(salt);
            return salt;
        }

        public static bool slowEquals(byte[] a, byte[] b)
        {
            int diff = a.Length ^ b.Length;
            for (int i = 0; i < a.Length && i < b.Length; i++)
            {
                diff |= a[i] ^ b[i];
            }
            return diff == 0;
        }

	이를 통해 새로운 긴 솔트를 생성하고, 솔트된 암호를 해시하는 기능을 추가하고, 두 해시를 비교하는 안전한 방법을 제공할 수 있습니다.

## 등록 끝점 만들기

이제는 사용자 계정 만들기를 시작하는 데 필요한 모든 것이 준비되었습니다. 이 섹션에서는 새로운 등록 요청을 처리하기 위한 등록 끝점을 설정합니다. 여기에서는 새로운 사용자 이름 및 암호 정책을 강제 적용하고 사용자 이름이 사용된 이름이 아닌지 확인해야 합니다. 그런 후 사용자 정보를 데이터베이스에 안전하게 저장합니다.

1. 들어오는 등록 시도를 나타내는 다음과 같은 새 클래스를 만듭니다.

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    등록하는 동안 다른 정보를 수집 및 저장해야 하는 경우 여기서 수행해야 합니다.

2. 모바일 서비스 백 엔드 프로젝트에서 **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 **추가** 및 **컨트롤러**를 클릭하고 `CustomRegistrationController`(이)라는 새 **Microsoft Azure 모바일 서비스 사용자 지정 컨트롤러**를 만들어 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Text.RegularExpressions;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

	위의 코드에서 프로젝트의 네임스페이스를 가진 자리 표시자를 바꿉니다.

4. 클래스 정의를 다음 코드로 바꿉니다.

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomRegistrationController : ApiController
	    {
	        public ApiServices Services { get; set; }

	        // POST api/CustomRegistration
	        public HttpResponseMessage Post(RegistrationRequest registrationRequest)
	        {
	            if (!Regex.IsMatch(registrationRequest.username, "^[a-zA-Z0-9]{4,}$"))
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid username (at least 4 chars, alphanumeric only)");
	            }
	            else if (registrationRequest.password.Length < 8)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Invalid password (at least 8 chars required)");
	            }

	            todoContext context = new todoContext();
	            Account account = context.Accounts.Where(a => a.Username == registrationRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                return this.Request.CreateResponse(HttpStatusCode.BadRequest, "That username already exists.");
	            }
	            else
	            {
	                byte[] salt = CustomLoginProviderUtils.generateSalt();
	                Account newAccount = new Account
	                {
	                    Id = Guid.NewGuid().ToString(),
	                    Username = registrationRequest.username,
	                    Salt = salt,
	                    SaltedAndHashedPassword = CustomLoginProviderUtils.hash(registrationRequest.password, salt)
	                };
	                context.Accounts.Add(newAccount);
	                context.SaveChanges();
	                return this.Request.CreateResponse(HttpStatusCode.Created);
	            }
	        }
	    }

    *todoContext* 변수를 프로젝트의 **DbContext** 이름으로 바꾸어야 합니다. 이 컨트롤러는 다음 특성을 사용하여 모든 트래픽을 이 끝점으로 허용합니다.

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]이 등록 끝점은 HTTP를 통해 모든 클라이언트에서 액세스할 수 있습니다. 이 서비스를 프로덕션 환경에 게시하기 전에 SMS 또는 전자 메일 기반 확인과 같은 일종의 스키마를 구현하여 등록의 유효성을 검사해야 합니다. 이렇게 하면 악의적인 사용자가 사기성 등록을 만들지 못합니다.

## LoginProvider 만들기

모바일 서비스 인증 파이프라인에 있는 기본 생성 항목 중 하나는 **LoginProvider**입니다. 이 섹션에서는 사용자 고유의 `CustomLoginProvider`을(를) 만듭니다. 이 항목은 기본 제공되는 공급자와 같이 파이프라인에 연결되지 않지만 몇 가지 편리한 기능을 제공합니다. Visual Studio 2013을 사용하는 경우 `LoginProvider` 클래스에 참조를 추가하는 `WindowsAzure.MobileServices.Backend.Security` nuget 패키지를 설치해야 할 수 있습니다.

1. **LoginProvider**에서 파생된 새 클래스 `CustomLoginProvider`을(를) 만들고 다음 `using` 문을 추가합니다.

	    using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Newtonsoft.Json.Linq;
		using Owin;
		using System.Security.Claims;

3. **CustomLoginProvider** 클래스 정의를 다음 코드로 바꿉니다.

        public class CustomLoginProvider : LoginProvider
        {
            public const string ProviderName = "custom";

            public override string Name
            {
                get { return ProviderName; }
            }

            public CustomLoginProvider(IServiceTokenHandler tokenHandler)
                : base(tokenHandler)
            {
                this.TokenLifetime = new TimeSpan(30, 0, 0, 0);
            }

        }

       프로젝트를 빌드하려고 하지만 이제 실패합니다. `LoginProvider`에는 나중에 수행하게 될 구현이 필요한 3개의 추상 메서드가 있스니다.

2. 동일한 코드 파일에서 `CustomLoginProviderCredentials`(이)라는 새 클래스를 만듭니다.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

	이 클래스는 사용자에 대한 정보를 나타내며 [GetIdentitiesAsync](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.serviceuser.getidentitiesasync.aspx)를 통해 백 엔드에서 사용자에게 제공됩니다. 사용자 지정 크레임을 추가하는 경우 이 개체에 캡처되었는지 확인합니다.

3. 추상 메서드 `ConfigureMiddleware`에 대한 다음 구현을 **CustomLoginProvider**에 추가합니다.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

	**CustomLoginProvider**가 인증 파이프라인과 통합되지 않기 때문에 이 메서드는 구현되지 않습니다.

4. 추상 메서드 `ParseCredentials`에 대한 다음 구현을 **CustomLoginProvider**에 추가합니다.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }

	이 메서드는 백 엔드가 들어오는 인증 토큰으로부터 사용자 정보를 역직렬화하도록 허용합니다.

5. 추상 메서드 `CreateCredentials`에 대한 다음 구현을 **CustomLoginProvider**에 추가합니다.

        public override ProviderCredentials CreateCredentials(ClaimsIdentity claimsIdentity)
        {
            if (claimsIdentity == null)
            {
                throw new ArgumentNullException("claimsIdentity");
            }

            string username = claimsIdentity.FindFirst(ClaimTypes.NameIdentifier).Value;
            CustomLoginProviderCredentials credentials = new CustomLoginProviderCredentials
            {
                UserId = this.TokenHandler.CreateUserId(this.Name, username)
            };

            return credentials;
        }

	이 메서드는 [ClaimsIdentity]를 [ProviderCredentials] 개체로 변환합니다. 이 개체는 인증 토큰 발행 단계에서 사용됩니다. 여기에서 추가 클레임을 다시 캡처해야 합니다.

6. App\_Start 폴더에서 WebApiConfig.cs 프로젝트 파일을 열면 **ConfigOptions** 뒤에 다음 줄의 코드가 작성됩니다.

		options.LoginProviders.Add(typeof(CustomLoginProvider));



## 로그인 끝점 만들기

그런 다음에는 사용자가 로그인할 수 있는 끝점을 만듭니다. 수신된 사용자 이름과 암호는 먼저 사용자의 솔트를 적용하고 암호를 해싱한 다음 들어온 값이 데이터베이스의 값과 일치하는지 확인하는 과정을 거쳐서 데이터베이스에 대해 확인됩니다. 값이 일치하면 [ClaimsIdentity]를 만들고 이를 **CustomLoginProvider**에 전달할 수 있습니다. 클라이언트 앱이 모바일 서비스에 액세스할 수 있도록 사용자 ID 및 인증 토큰을 수신합니다.

1. 모바일 서비스 백 엔드 프로젝트에서 다음 새 `LoginRequest` 클래스를 만듭니다.

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

	이 클래스는 들어오는 로그인 시도를 나타냅니다.

2. 다음 새 `CustomLoginResult` 클래스 만들기:

	    public class CustomLoginResult
	    {
	        public string UserId { get; set; }
	        public string MobileServiceAuthenticationToken { get; set; }

	    }

	이 클래스는 사용자 ID 및 인증 토큰을 사용한 성공적인 로그인을 나타냅니다. 이 클래스에는 클라이언트의 MobileServiceUser 클래스와 동일한 모양이 있으며, 이는 강력한 형식의 클라이언트에서 로그인 응답을 더 쉽게 처리할 수 있도록 합니다.

2. **컨트롤러**를 마우스 오른쪽 단추로 클릭하고 **추가** 및 **컨트롤러**를 클릭하고 `CustomLoginController`(이)라는 새 **Microsoft Azure 모바일 서비스 사용자 지정 컨트롤러**를 만들어 다음 `using` 문을 추가합니다.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using System.Security.Claims;
		using <my_project_namespace>.DataObjects;
		using <my_project_namespace>.Models;

3. **CustomLoginController** 클래스 정의를 다음 코드로 바꿉니다.

	    [AuthorizeLevel(AuthorizationLevel.Anonymous)]
	    public class CustomLoginController : ApiController
	    {
	        public ApiServices Services { get; set; }
	        public IServiceTokenHandler handler { get; set; }

	        // POST api/CustomLogin
	        public HttpResponseMessage Post(LoginRequest loginRequest)
	        {
	            todoContext context = new todoContext();
	            Account account = context.Accounts
	                .Where(a => a.Username == loginRequest.username).SingleOrDefault();
	            if (account != null)
	            {
	                byte[] incoming = CustomLoginProviderUtils
	                    .hash(loginRequest.password, account.Salt);

	                if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
	                {
	                    ClaimsIdentity claimsIdentity = new ClaimsIdentity();
	                    claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
	                    LoginResult loginResult = new CustomLoginProvider(handler)
	                        .CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
	                    var customLoginResult = new CustomLoginResult()
	                    {
	                        UserId = loginResult.User.UserId,
	                        MobileServiceAuthenticationToken = loginResult.AuthenticationToken
	                    };
	                    return this.Request.CreateResponse(HttpStatusCode.OK, customLoginResult);
	                }
	            }
	            return this.Request.CreateResponse(HttpStatusCode.Unauthorized,
	                "Invalid username or password");
	        }
	    }

       *todoContext* 변수를 프로젝트의 **DbContext** 이름으로 바꾸어야 합니다. 이 컨트롤러는 다음 특성을 사용하여 모든 트래픽을 이 끝점으로 허용합니다.

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[AZURE.IMPORTANT]프로덕션 사용을 위한 `CustomLoginController`에는 무차별 암호 대입 공격(brute-force) 검색 전략이 포함되어야 합니다. 그렇지 않으면 로그인 솔루션이 공격에 취약해집니다.

## 인증을 요구하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## 테스트 클라이언트를 사용하여 로그인 흐름 테스트

클라이언트 앱에서는 사용자 이름과 암호를 가져와서 이를 등록 및 로그인 끝점에 대한 JSON 페이로드로 보내는 사용자 지정 로그인 화면을 개발해야 합니다. 이 자습서를 완료할 때는 대신 모바일 서비스 .NET 백 엔드에 대해 기본 제공되는 테스트 클라이언트만 사용합니다.

1. Visual Studio에서 모바일 서비스 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **디버그** 및 **새 인스턴스 시작**을 클릭합니다.

	그러면 모바일 서비스 백 엔드 프로젝트의 새 디버깅 인스턴스가 시작됩니다. 서비스가 성공적으로 시작되면 **이제 코드가 실행되고 있습니다**라는 시작 페이지가 시작됩니다.

2. 서비스 시작 페이지에서 **체험**을 클릭한 다음 인증 대화 상자에 사용자 이름이 비어 있는 web.config 파일에 **MS\_ApplicationKey** 앱 설정에 대해 설정한 암호를 입력합니다.

3. 도움말 페이지에서 **CustomRegistration** 끝점을 클릭한 다음 **사용해보기**를 클릭합니다.

    ![][2]

4. 본문에서 샘플 문자열을 이전에 지정한 조건을 충족하는 사용자 이름 및 암호로 바꾼 다음 **보내기**를 클릭합니다.

    ![][3]

	응답은 **201/Created**이 됩니다.

5. 브라우저의 뒤로 단추를 클릭하고 이전 단계에서 등록한 것과 같은 사용자 이름 및 암호를 사용하여 **CustomLogin** 끝점에 대해 2 및 3단계를 반복합니다.

    ![][4]

	*사용자 ID* 및 *authenticationToken* 둘 다 있는 **사용자** JSON 개체를 포함하는 본문이 있는 응답 메시지를 받게 되며, 이 토큰은 사용자 지정 인증으로 생성되는 모바일 서비스 인증 토큰입니다. 이 토큰은 TodoItem 끝점에 대한 클라이언트 앱 액세스 권한을 부여하기에 충분합니다.

	*authenticationToken* 값의 복사본을 만듭니다. 이는 제한된 TodoItem 끝점에 액세스하는 데 사용됩니다.

6. 브라우저의 뒤로 단추를 클릭한 다음 API 설명 페이지에서 **GetTables**를 클릭하고 **사용해보기**를 클릭합니다.

7. GET 요청 대화상자에서 **헤더** 옆의 더하기 기호를 클릭하고 왼쪽 상자에 `X-ZUMO-AUTH` 값을 입력하고 복사한 *authenticationToken* 값을 오른쪽 상자에 붙여넣은 다음 **보내기**를 클릭합니다.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-endpoint.png)

	모바일 서비스는 끝점에 대한 액세스 권한을 부여하며 해당 테이블에서 TodoItems 목록과 함께 **200/OK** 상태를 반환합니다.

 	![](./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-access-success.png)

>[AZURE.IMPORTANT]테스트를 위해 Azure에 이 모바일 서비스 프로젝트를 게시하도록 선택하는 경우, 로그인 및 인증 공급자가 공격에 취약해집니다. 이를 적절하게 확정하거나 보호되는 테스트 데이터가 사용자에게 중요하지 않아야 합니다. 프로덕션 서비스를 보호하려면 사용자 지정 인증 체계를 사용하려기 전에 주의를 기울여야 합니다.

## 클라이언트에서 사용자 지정 인증을 사용하여 로그인

이 섹션에서는 모바일 서비스에 액세스하는 데 사용되는 인증 토큰을 가져오기 위해 클라이언트에서 사용자 지정 인증 끝점애 액세스하는 데 필요한 단계를 설명합니다. 필요한 특정 클라이언트 코드는 클라이언트에 따라 다르기 때문에 여기에 제공된 지침은 플랫폼에 독립적입니다.

>[AZURE.NOTE]모바일 서비스 클라이언트 라이브러리는 HTTPS를 통해 서비스와 통신합니다. 이 솔루션은 암호를 일반 텍스트 형식으로 보내는 데 필요하기 때문에 직접 REST 요청을 사용하는 이러한 끝점을 호출할 때 HTTPS를 사용하는지 확인해야 합니다.

1. 사용자 이름 및 암호를 입력할 수 있도록 클라이언트 앱에서 필요한 UI 요소를 만듭니다.

2. 클라이언트 라이브러리의 **MobileServiceClient**에서 적합한 **invokeApi** 메서드를 사용하여 메시지 본문에서 런타임 제공 사용자 이름 및 암호를 전달하는 **CustomRegistration** 끝점을 호출합니다.

	계정 테이블에서 사용자 로그인 정보를 유지할 뿐 아니라 지정된 사용자의 계정을 만들기 위해 **CustomRegistration** 끝점을 한 번 호출해야 합니다. 다양한 지원 클라이언트 플랫폼에서 사용자 지정 API를 호출하는 방법의 예시는 [Azure 모바일 서비스의 사용자 지정 API - 클라이언트 SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx) 문서를 참조하세요.

	> [AZURE.IMPORTANT]이 사용자 프로비전 단계는 한 번만 발생하기 때문에 일부 대역 외 방식에서 사용자 계정 만들기를 고려해야 합니다. 공용 등록 끝점의 경우, SMS 기반 또는 이메일 기반 확인 프로세스 구현을 고려하거나 사기성 계정을 생성하지 않도록 다른 보호책을 고려해야 합니다. Twilio를 사용하여 모바일 서비스에서 SMS 메시지를 보낼 수 있습니다. 모바일 서비스에서 이메일을 보내려면 SendGrid를 사용할 수도 있습니다. SendGrid 사용에 대한 자세한 내용은 [SendGrid로 모바일 서비스에서 전자 메일 보내기](store-sendgrid-mobile-services-send-email-scripts.md)를 참조하세요.

3. 이번에는 적합한 **invokeApi** 메서드를 다시 사용하여 메시지 본문에서 런타임 제공 사용자 이름 및 암호를 전달하는 **CustomLogin** 끝점을 호출합니다.

	이번에는 성공적인 로그인 후에 응답 개체에서 반환되는 *userId* 및 *authenticationToken* 값을 캡처해야 합니다.

4. [기존 앱에 인증 추가](mobile-services-dotnet-backend-ios-get-started-users.md) 항목에 표시된 대로 반환되는 *userId* 및 *authenticationToken* 값을 사용하여 새 **MobileServiceUser** 개체를 만들고 **MobileServiceClient** 인스턴스에 대해 현재 사용자로 설정합니다. CustomLogin 결과는 **MobileServiceUser** 개체와 같은 형태이므로 결과를 직접적으로 캐스트할 수 있어야 합니다.

이제 자습서를 마쳤습니다.


<!-- Anchors. -->


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png


<!-- URLs. -->
[앱에 인증 추가]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[모바일 서비스 시작]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

[ClaimsIdentity]: https://msdn.microsoft.com/library/system.security.claims.claimsidentity(v=vs.110).aspx
[ProviderCredentials]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.security.providercredentials.aspx

<!---HONumber=AcomDC_0107_2016-->