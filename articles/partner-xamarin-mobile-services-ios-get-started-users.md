<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# 모바일 서비스에서 인증 시작

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

이 항목은 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서에서는 앱에서 인증을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증을 위해 앱 등록 및 모바일 서비스 구성][인증을 위해 앱 등록 및 모바일 서비스 구성]
2.  [테이블 사용 권한을 인증된 사용자로 제한][테이블 사용 권한을 인증된 사용자로 제한]
3.  [앱에 인증 추가][앱에 인증 추가]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 먼저 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료해야 합니다.

이 자습서를 완료하려면 [Xamarin.iOS], XCode 5.0 및 iOS 5.0 이상 버전이 필요합니다.

## <a name="register"></a><span class="short-header">앱 등록</span>인증을 위해 앱 등록 및 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">사용 권한 제한</span>사용 권한을 인증된 사용자로 제한

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Xcode에서 [모바일 서비스 시작][모바일 서비스 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

2.  **Run** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다. 그리고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다.

    이는 앱이 인증되지 않은 사용자로 모바일 서비스에 액세스하려고 시도하지만 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 모바일 서비스의 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a><span class="short-header">인증 추가</span>앱에 인증 추가

1.  **TodoService** 프로젝트 파일을 열고 다음 변수를 추가합니다.

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  다음과 같이 정의된 새 메서드 **Authenticate**를 **TodoService**에 추가합니다.

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout">

    **참고**
    Microsoft 계정 이외의 ID 공급자를 사용하는 경우 위의 **LoginAsync**에 전달된 값을 *Facebook*, *Twitter*, *Google* 또는 *WindowsAzureActiveDirectory* 중 하나로 변경합니다.

    </div>

3.  **TodoItem** 테이블에 대한 요청을 **TodoService** 생성자에서 새 메서드 **CreateTable**로 이동합니다.

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  다음과 같이 정의된 새 비동기 public 메서드 **LoginAndGetData**를 만듭니다.

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  **TodoListViewController**에서 **ViewDidAppear** 메서드를 무시하고 아래와 같이 정의합니다. 그러면 **TodoService**에 사용자에 대한 핸들이 없는 경우 사용자가 로그인됩니다.

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 

            RefreshAsync();
        }

6.  **RefreshAsync**에 대한 원래 호출을 **TodoListViewController.ViewDidLoad**에서 제거합니다.

7.  **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작한 후 선택한 ID 공급자로 로그온합니다.

    로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.

## 완성된 예 가져오기

[완성된 예제 프로젝트][완성된 예제 프로젝트]를 다운로드합니다. 고유한 Azure 설정으로 **applicationURL** 및 **applicationKey** 변수를 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계

다음 자습서인 [스크립트를 통해 사용자 권한 부여][스크립트를 통해 사용자 권한 부여]에서는 인증된 사용자를 기준으로 모바일 서비스에서 제공한 사용자 ID 값을 가져와 모바일 서비스에서 반환된 데이터를 필터링하는 데 사용합니다.

  <!-- URLs. TODO:: update completed example project link with project download -->

  [인증을 위해 앱 등록 및 모바일 서비스 구성]: #register
  [테이블 사용 권한을 인증된 사용자로 제한]: #permissions
  [앱에 인증 추가]: #add-authentication
  [모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-xamarin-ios
  [완성된 예제 프로젝트]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
