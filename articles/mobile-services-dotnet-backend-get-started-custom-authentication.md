<properties linkid="mobile-services-dotnet-backend-get-started-custom-authentication" urlDisplayName="Get started with custom authentication" pageTitle="Get started with custom authentication | Mobile Dev Center" metaKeywords="" description="Learn how to authenticate users with a username and password." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with custom authentication" authors="mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# 사용자 지정 인증 시작

이 항목에서는 고유 모바일 서비스 인증 토큰을 발행하여 Azure 모바일 서비스 .NET 백 엔드에서 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱에 대해 사용자 지정 사용자 이름 및 암호를 사용해서 빠른 시작 프로젝트에 인증을 추가합니다.

> [WACOM.NOTE] 이 자습서에서는 사용자 지정 자격 증명을 사용해서 모바일 서비스를 인증하는 고급 방법을 보여 줍니다. 많은 앱에서는 기본 제공되는 소셜 ID 공급자를 대신 사용하여 Facebook, Twitter, Google, Microsoft 계정 및 Azure Active Directory를 통해 로그인할 수 있도록 허용하는 것이 가장 적합할 것입니다. 모바일 서비스에서 인증 관련 작업을 처음 수행할 경우에는 [사용자 시작][사용자 시작] 자습서를 참조하세요.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [계정 테이블 설정][계정 테이블 설정]
2. [등록 끝점 만들기][등록 끝점 만들기]
3. [LoginProvider 만들기][LoginProvider 만들기]
4. [로그인 끝점 만들기][로그인 끝점 만들기]
5. [인증을 요구하도록 모바일 서비스 구성][인증을 요구하도록 모바일 서비스 구성]
6. [테스트 클라이언트를 사용하여 로그인 흐름 테스트][테스트 클라이언트를 사용하여 로그인 흐름 테스트]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

>[WACOM.NOTE] 이 자습서에서는 모바일 서비스에 대한 인증 토큰을 발행하는 방법을 보여 줍니다. 이 자습서는 보안 지침으로 간주되지 않습니다. 앱 개발 시에는 암호 저장소가 보안에 미치는 영향을 인지하고 무차별 암호 대입 공격(brute-force attack)을 관리하기 위한 전략을 마련해야 합니다.

## <a name="table-setup"></a> 계정 테이블 설정

사용자 지정 인증을 사용 중이고 다른 ID 공급자를 사용하지 않기 때문에 사용자 로그인 정보를 저장할 필요가 있습니다. 이 섹션에서는 계정에 대한 테이블을 만들고 기본 보안 메커니즘을 설정합니다. 계정 테이블에는 사용자 이름과 솔트된/해시된 암호가 포함되며, 필요한 경우 추가 사용자 정보도 포함할 수 있습니다.

1. 백 엔드 프로젝트의 `DataObjects` 폴더에서 `Account`라는 새 엔터티를 만듭니다.

            public class Account : EntityData
            {
                public string Username { get; set; }
                public byte[] Salt { get; set; }
                public byte[] SaltedAndHashedPassword { get; set; }
            }
    
    이 엔터티는 새 테이블의 행을 나타내고, 사용자 이름, 해당 사용자의 솔트 및 보안 방식으로 저장된 암호를 포함합니다.

2. `Models` 폴더 아래에는 모바일 서비스 이름에 따라 지정된 `DbContext` 클래스가 있습니다. 이 자습서의 나머지 부분에서는 `todoContext`가 예제로 사용되므로, 그에 따라 코드 조각을 업데이트해야 합니다. 컨텍스트를 열고 다음을 포함하여 데이터 모델에 계정 테이블을 추가합니다.

        public DbSet<Account> Accounts { get; set; }

3.  그런 다음 이 데이터를 사용하기 위한 보안 기능을 설정합니다. 새로운 긴 솔트를 생성하기 위한 방법, 솔트된 암호를 해시하는 기능, 두 해시를 비교하는 보안 방식이 필요합니다. `CustomLoginProviderUtils`라는 클래스를 만들고 여기에 다음 메서드를 추가합니다.


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


## <a name="register-endpoint"></a> 등록 끝점 만들기

이제는 사용자 계정 만들기를 시작하는 데 필요한 모든 것이 준비되었습니다. 이 섹션에서는 새로운 등록 요청을 처리하기 위한 등록 끝점을 설정합니다. 여기에서는 새로운 사용자 이름 및 암호 정책을 강제 적용하고 사용자 이름이 사용된 이름이 아닌지 확인해야 합니다. 그런 후 사용자 정보를 데이터베이스에 안전하게 저장합니다.

1. 들어오는 등록 시도를 나타내는 개체를 만듭니다.

        public class RegistrationRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

    등록 시 다른 정보를 수집하길 원하면 여기에 포함할 수 있습니다.

2. 모바일 서비스 백 엔드 프로젝트에서 CustomRegistrationController라는 새 사용자 지정 컨트롤러를 추가하고 다음과 같이 붙여 넣습니다.

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
                    return this.Request.CreateResponse(HttpStatusCode.BadRequest, "Username already exists");
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

    컨트롤러에 다음과 같이 데코레이트하여 이 끝점에 대한 모든 트래픽을 허용해야 합니다.

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]


## <a name="login-provider"></a> LoginProvider 만들기

모바일 서비스 인증 파이프라인에 있는 기본 생성 항목 중 하나는 `LoginProvider`입니다. 이 섹션에서는 사용자 고유의 `CustomLoginProvider`를 만듭니다. 이 항목은 기본 제공되는 공급자와 같이 파이프라인에 연결되지 않지만 몇 가지 편리한 기능을 제공합니다.

1. `LoginProvider`에서 파생된 새 클래스 `CustomLoginProvider`를 만듭니다.

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

       `LoginProvider`에는 나중에 구현하게 될 세 가지 추상 메서드가 포함됩니다.

2. `CustomLoginProviderCredentials`라는 새 클래스를 만듭니다. 이 클래스는 사용자에 대한 정보를 나타내며 `ServiceUser.getIdentitiesAsync()`를 통해 백 엔드에서 사용자에게 제공됩니다. 사용자 지정 크레임을 추가하는 경우 이 개체에 캡처되었는지 확인합니다.

        public class CustomLoginProviderCredentials : ProviderCredentials
        {
            public CustomLoginProviderCredentials()
                : base(CustomLoginProvider.ProviderName)
            {
            }
        }

3. 추상 메서드 `ConfigureMiddleware`에 대한 다음 구현을 `CustomLoginProvider`에 추가합니다. `CustomLoginProvider`가 인증 파이프라인과 통합되지 않기 때문에 이 메서드는 여기에서 수행되는 작업이 없습니다.

        public override void ConfigureMiddleware(IAppBuilder appBuilder, ServiceSettingsDictionary settings)
        {
            // Not Applicable - used for federated identity flows
            return;
        }

4. 추상 메서드 `ParseCredentials`에 대한 다음 구현을 `CustomLoginProvider`에 추가합니다. 이 메서드는 백 엔드가 들어오는 인증 토큰으로부터 사용자 정보를 역직렬화하도록 허용합니다.

        public override ProviderCredentials ParseCredentials(JObject serialized)
        {
            if (serialized == null)
            {
                throw new ArgumentNullException("serialized");
            }

            return serialized.ToObject<CustomLoginProviderCredentials>();
        }


5. 추상 메서드 `CreateCredentials`에 대한 다음 구현을 `CustomLoginProvider`에 추가합니다. 이 메서드는 `ClaimsIdentity`를 `ProviderCredentials` 개체로 변환합니다. 이 개체는 인증 토큰 발행 단계에서 사용됩니다. 여기에서 추가 클레임을 다시 캡처해야 합니다.

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

## <a name="login-endpoint"></a> 로그인 끝점 만들기

그런 다음에는 사용자가 로그인할 수 있는 끝점을 만듭니다. 수신된 사용자 이름과 암호는 먼저 사용자의 솔트를 적용하고 암호를 해싱한 다음 들어온 값이 데이터베이스의 값과 일치하는지 확인하는 과정을 거쳐서 데이터베이스에 대해 확인됩니다. 값이 일치하면 `ClaimsIdentity`를 만들고 이를 `CustomLoginProvider`에 전달할 수 있습니다. 그런 다음 클라이언트 앱이 모바일 서비스에 액세스할 수 있도록 사용자 ID 및 인증 토큰을 수신합니다.

1. 모바일 서비스 백 엔드 프로젝트에서 들어오는 로그인 시도를 나타내기 위한 개체를 만듭니다.

        public class LoginRequest
        {
            public String username { get; set; }
            public String password { get; set; }
        }

2. `CustomLoginController`라는 새로운 사용자 지정 컨트롤러를 추가하고 다음과 같이 붙여 넣습니다.

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]
        public class CustomLoginController : ApiController
        {
            public ApiServices Services { get; set; }
            public IServiceTokenHandler handler { get; set; }

            // POST api/CustomLogin
            public HttpResponseMessage Post(LoginRequest loginRequest)
            {
                todoContext context = new todoContext();
                Account account = context.Accounts.Where(a => a.Username == loginRequest.username).SingleOrDefault();
                if (account != null)
                {
                    byte[] incoming = CustomLoginProviderUtils.hash(loginRequest.password, account.Salt);

                    if (CustomLoginProviderUtils.slowEquals(incoming, account.SaltedAndHashedPassword))
                    {
                        ClaimsIdentity claimsIdentity = new ClaimsIdentity();
                        claimsIdentity.AddClaim(new Claim(ClaimTypes.NameIdentifier, loginRequest.username));
                        LoginResult loginResult = new CustomLoginProvider(handler).CreateLoginResult(claimsIdentity, Services.Settings.MasterKey);
                        return this.Request.CreateResponse(HttpStatusCode.OK, loginResult);
                    }
                }
                return this.Request.CreateResponse(HttpStatusCode.Unauthorized, "Invalid username or password");
            }
        }

    컨트롤러에 다음과 같이 데코레이트하여 이 끝점에 대한 모든 트래픽을 허용해야 합니다.

        [AuthorizeLevel(AuthorizationLevel.Anonymous)]

>[WACOM.NOTE] 프로덕션 사용을 위한 `CustomLoginController`에는 무차별 암호 대입 공격(brute-force) 검색 전략이 포함되어야 합니다. 그렇지 않으면 로그인 솔루션이 공격에 취약해집니다.

## <a name="require-authentication"></a> 인증을 요구하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]


## <a name="test-login"></a> 테스트 클라이언트를 사용하여 로그인 흐름 테스트

클라이언트 응용 프로그램에서는 사용자 이름과 암호를 가져와서 이를 등록 및 로그인 끝점에 대한 JSON 페이로드로 보내는 사용자 지정 로그인 화면을 개발해야 합니다. 이 자습서를 완료할 때는 대신 모바일 서비스 .NET 백 엔드에 대해 기본 제공되는 테스트 클라이언트만 사용합니다.

>[WACOM.NOTE] 모바일 서비스 SDK는 HTTPS를 통해 서비스와 통신합니다. 직접 REST 호출을 통해 이 끝점에 액세스하려는 경우, 암호가 일반 텍스트로 전송되므로 모바일 서비스를 호출할 때 HTTPS를 사용하는지 확인해야 합니다.

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그-\>새 인스턴스 시작**을 선택하여 모바일 서비스 백 엔드 프로젝트에 대한 새로운 디버깅 인스턴스를 시작합니다.

    ![][0]

2. **try it out(사용해 보기)**를 클릭합니다.

    ![][1]

3. 등록 끝점을 선택합니다. API에 대한 몇 가지 기본 설명서를 볼 수 있습니다. **사용해보기**를 클릭합니다.

    ![][2]

4. 본문에서 샘플 문자열을 이전에 지정한 조건을 충족하는 사용자 이름 및 암호로 바꿉니다. 그런 후 **보내기**를 클릭합니다. 응답은 **201/Created**이 됩니다.

    ![][3]

5. 로그인 끝점에 대해 이 프로세스를 반복합니다. 이전에 등록한 동일한 사용자 이름 및 암호를 보낸 후에는 사용자의 ID와 인증 토큰이 수신됩니다.

    ![][4]

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

[사용자 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[계정 테이블 설정]: #table-setup
[등록 끝점 만들기]: #register-endpoint
[LoginProvider 만들기]: #login-provider
[로그인 끝점 만들기]: #login-endpoint
[인증을 요구하도록 모바일 서비스 구성]: #require-authentication
[테스트 클라이언트를 사용하여 로그인 흐름 테스트]: #test-login
[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
[0]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-debug-start.png
[1]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-try-out.png
[2]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-test-client.png
[3]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-send-register.png
[4]: ./media/mobile-services-dotnet-backend-get-started-custom-authentication/mobile-services-dotnet-backend-custom-auth-login-result.png
