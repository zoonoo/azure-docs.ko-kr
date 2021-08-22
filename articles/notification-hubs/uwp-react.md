---
title: Android 및 iOS 애플리케이션에 Azure Notification Hubs 알림 보내기
description: Azure Notification Hubs의 플랫폼 간 기능에 대해 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: template-concept
ms.openlocfilehash: 229de776e7e2dace0253d7cc4495663969cb3e62
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285236"
---
# <a name="send-notifications-to-android-and-ios-applications"></a>Android 및 iOS 애플리케이션에 알림 보내기

이 문서에서는 여러 플랫폼에서 Azure Notification Hub의 기능을 보여 주도록 빌드된 Azure Notification Hub 샘플 애플리케이션의 개요를 제공합니다. 애플리케이션은 데스크톱 **Contoso Land Survey** 애플리케이션이 Android 및 iOS Contoso 애플리케이션이 모두 수신할 수 있는 알림을 보내는 토지 설문 조사 시나리오를 사용합니다.

[전체 샘플은 GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/main/NotificationHubSample)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

샘플을 빌드하려면 다음 필수 구성 요소가 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Microsoft Visual Studio 2019 이상. 이 예제에서는 [Visual Studio 2019](https://www.visualstudio.com/products)를 사용합니다.
- 다음 워크로드로 설치된 Visual Studio 2019.
  - .NET 5.0 SDK
  - ASP.NET 및 웹 개발
  - Azure 개발
  - Node.js 개발
  - [UWP 앱 개발 도구](/windows/uwp/get-started/get-set-up)
- Android에 푸시 알림을 사용하도록 설정하는 Firebase 계정.
- iOS에 푸시 알림을 사용하도록 설정하는 Apple 개발자 계정.
- Azure에서 호스트되는 SQL Server 데이터베이스 인스턴스.
- Azure Notification Hub 네임스페이스 및 허브.

## <a name="sample-architecture"></a>샘플 아키텍처

솔루션은 다음 구성 요소로 이루어져 있습니다.

- Azure Notification Hub 인스턴스: [Azure Portal](https://portal.azure.com)에 구성된 ANH 네임스페이스 및 허브입니다.
- SQL Server 데이터베이스: [Azure Portal](https://portal.azure.com)에 구성된 SQL Server 데이터베이스 인스턴스입니다.
- ASP.NET 앱 백 엔드: 알림 허브와 연결되고 Azure App Service로 호스트되는 .NET 5.0을 기반으로 하는 웹 API 백 엔드입니다.
- Windows UWP 애플리케이션: React Native를 사용하여 빌드되고 다양한 사용자 및 설문 조사 그룹에 뉴스 및 설문 조사 정보를 발송하는 “관리자” 애플리케이션 역할을 하는 UWP 애플리케이션입니다. 또한 애플리케이션은 새 사용자를 만들고 사용자가 할당된 그룹을 편집하는 데 도움이 됩니다.
- Android 및 iOS 클라이언트 앱: React Native를 사용하여 빌드된 “Land Survey” 애플리케이션입니다. 이러한 앱은 UWP 관리자 애플리케이션에서 발송된 정보를 사용자에게 표시합니다.

## <a name="sample-folder-structure"></a>샘플 폴더 구조

GitHub의 샘플 애플리케이션에는 다음 폴더가 포함되어 있습니다.

- **NotificationHub.Sample.API**: 백 엔드 역할을 하는 Visual Studio 2019 ASP.NET Web API 솔루션입니다.
- **앱**: 관리자 로그인으로 알림을 발송하고 설문 조사 사용자 로그인으로 알림을 받을 수 있도록 하는 플랫폼 간 React Native 애플리케이션입니다.
- **azure-template**: Azure 구독에서 이 배포를 구성하는 데 필요한 모든 리소스를 배포하는 데 사용할 수 있는 Azure Resource Manager 템플릿(`parameters.json` 및 `template.json`)입니다. Resource Manager 템플릿 배포에 대한 자세한 내용은 [Azure Portal을 사용하여 ARM 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 참조하세요.

## <a name="sample-overview"></a>샘플 개요

다음 섹션에서는 샘플을 구성하는 구성 요소의 개요를 제공합니다.

### <a name="controllers"></a>Controllers

#### <a name="authentication"></a>인증

AuthenticateController.cs의 다음 메서드는 로그인한 사용자를 인증하는 데 사용됩니다.

```cs
[HttpPost]
[Route("login")]
public async Task<IActionResult> Login([FromBody] LoginModel model)
{
   var user = await userManager.FindByNameAsync(model.Username);
    if (user != null && await userManager.CheckPasswordAsync(user, model.Password))
    {
        var userRoles = await userManager.GetRolesAsync(user);

        var authClaims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, user.UserName),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
        };

        foreach (var userRole in userRoles)
        {
            authClaims.Add(new Claim(ClaimTypes.Role, userRole));
        }

        var authSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["JWT:Secret"]));

        var token = new JwtSecurityToken(
            issuer: _configuration["JWT:ValidIssuer"],
            audience: _configuration["JWT:ValidAudience"],
            expires: DateTime.Now.AddHours(3),
            claims: authClaims,
            signingCredentials: new SigningCredentials(authSigningKey, SecurityAlgorithms.HmacSha256)
            );

        UserDetails userDetails = new UserDetails();
        userDetails.FirstName = model.Username;
        userDetails.LastName = model.Username;
        userDetails.UserName = model.Username;

        return Ok(new
        {
            token = new JwtSecurityTokenHandler().WriteToken(token),
            expiration = token.ValidTo,
            username = model.Username,
            email = user.Email,
            role = userRoles != null ? userRoles[0] : "Site-Manager",
            user = userDetails
        });
    }
    return Unauthorized();
}

[HttpPost]
[Route("register")]
public async Task<IActionResult> Register([FromBody] RegisterModel model)
{
    var userExists = await userManager.FindByNameAsync(model.Username);
    if (userExists != null)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User already exists!" });

    ApplicationUser user = new ApplicationUser()
    {
        Email = model.Email,
        SecurityStamp = Guid.NewGuid().ToString(),
        UserName = model.Username
    };
    var result = await userManager.CreateAsync(user, model.Password);
    if (!result.Succeeded)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User creation failed! Please check user details and try again." });

    if (!await roleManager.RoleExistsAsync(UserRoles.SiteManager))
        await roleManager.CreateAsync(new IdentityRole(UserRoles.SiteManager));

    if (await roleManager.RoleExistsAsync(UserRoles.SiteManager))
    {
        await userManager.AddToRoleAsync(user, UserRoles.SiteManager);
    }

    return Ok(new Response { Status = "Success", Message = "User created successfully!" });
}
```

### <a name="dashboard"></a>대시보드

DashboardController.cs의 대시보드 컨트롤러는 모든 알림 정보를 반환합니다.

```cs
public class DashboardController : ControllerBase
{
    private readonly ApplicationDbContext _db;
    private readonly INotificationService _notificationService;

    public DashboardController(ApplicationDbContext dbContext, INotificationService notificationService)
    {
        _db = dbContext;
        _notificationService = notificationService;
    }

    [HttpGet("insights")]
    public async Task<IActionResult> GetDashboardInsight(string duration)
    {
        DashboardInsight dashboardInsight = new DashboardInsight();

        dashboardInsight.DeviceTrends = await _notificationService.GetAllRegistrationInfoAsync();

        var notificationMessages = _db.NotificationMessages.ToList();

        switch (duration)
        {
            case "Daily":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key.ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Weekly":
                {
                    dashboardInsight.NotificationTrends = notificationMessages
                                                            .GroupBy(m => WeekNumber(m.SentTime.Date))
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = FirstDateOfWeekISO8601(DateTime.Now.Year, m.Key).ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Monthly":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date.Month)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key + "-" + DateTime.Now.Year,
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            default:
                break;
        }

        dashboardInsight.TotalGroups = _db.SurveyGroups.Count();
        dashboardInsight.TotalUsers = _db.Users.Count();
        dashboardInsight.TotalNotificationsSent = _db.NotificationMessages.Count();

        return Ok(dashboardInsight);
    }
```

### <a name="front-end"></a>프런트 엔드

백 엔드 API를 호출하기 위해 샘플은 실제 API 호출을 만드는 notification.service.js 서비스를 만듭니다. 이 코드는 다음과 같이 app\data\services\notification.service.js에 위치합니다.

```nodejs
export const sendNotificationAPI = async (userInfo) => {
  let url = `${api}notification/send`;
  let authHeader = await getAuthHeaders();
  return await post(url, userInfo, { ...authHeader });
};

export const getNotificationsAPI = async () => {
  let url = `${api}notification/get`;
  let authHeader = await getAuthHeaders();
  return await get(url, { ...authHeader });
};
```

### <a name="manager-application"></a>관리자 애플리케이션

이 샘플에는 React Native를 사용하여 빌드된 UWP 애플리케이션이 있습니다. 다양한 사용자 및 설문 조사 그룹에 뉴스 및 설문 조사 정보를 발송하는 “관리자” 애플리케이션 역할을 합니다. 또한 애플리케이션은 새 사용자를 만들고 사용자가 할당된 그룹을 편집하는 데 도움이 됩니다.

관리자 로그인의 경우 다음 엔드포인트 및 POST 본문을 사용하여 선택한 사용자 자격 증명을 생성합니다. 원하는 HTTP REST 클라이언트를 사용할 수 있습니다.

#### <a name="endpoint"></a>엔드포인트

```http
POST {{endpoint}}api/authenticate/register-admin
```

#### <a name="body"></a>본문

```http
{
  "username": "<USER_NAME>",
  "email": "<EMAIL>",
  "password": "<PASSWORD>"
}
```

등록되면 동일한 자격 증명을 사용하여 UWP 애플리케이션에 로그인할 수 있어야 합니다.

### <a name="notification-controller"></a>알림 컨트롤러

NotificationController.cs의 다음 코드는 알림을 가져오고 보냅니다.

```cs
[Produces("application/json")]
[Consumes("application/json")]
[HttpPost("send")]
public async Task<ActionResult> SendNotification([FromBody] NotificationMessage notificationMessage)
{
    try
    {
        List<string> tags = new List<string>();

        // attach survey group and user information with notificationMessage
        notificationMessage.SurveyGroupTags.ForEach(surveyGroupId =>
        {
            var group = _db.SurveyGroups.Where(g => g.Id == surveyGroupId).FirstOrDefault();
            if (group != null)
            {
                notificationMessage.SurveyGroups.Add(group);
                tags.Add($"group:{group.GroupName.Replace(' ', '-')}");
            }
        });

        notificationMessage.UserTags.ForEach(userId =>
        {
            var user = _db.Users.Where(u => u.Id == userId).FirstOrDefault();
            if (user != null)
            {
                notificationMessage.Users.Add(user);
                tags.Add($"username:{user.UserName}");
            }
        });
        _db.NotificationMessages.Add(notificationMessage);

        // send template notification
        var notification = new Dictionary<string, string>();
        notification.Add("title", notificationMessage.NotificationTitle);
        notification.Add("message", notificationMessage.NotificationDescription);

        var res = await _notificationService.RequestNotificationAsync(notificationMessage, tags, HttpContext.RequestAborted);

        await _db.SaveChangesAsync();
        return Ok(notificationMessage);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.Message);
    }
}

[Produces("application/json")]
[HttpGet("get")]
public async Task<ActionResult> Get()
{
    try
    {
        var surveyGroups = _db.NotificationMessages.Include(message => message.SurveyGroups).Include(message => message.Users).ToList();
        return Ok(surveyGroups);
    }
    catch (Exception ex)
    {
        return BadRequest();
    }
}
```

### <a name="notification-service"></a>알림 서비스

**NotificationHub.Sample.API/NotificationHub.Sample.API/Services/Notifications/INotificationService.cs** 에 있는 알림 서비스에는 설치를 만들고 삭제하는 메서드가 있습니다. 등록된 모든 사용자에게 알림을 보내고 모든 등록 정보를 얻는 메서드도 있습니다.

```cs
public interface INotificationService
{
   Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken cancellationToken);
   Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken cancellationToken);
   Task<bool> RequestNotificationAsync(NotificationMessage notificationMessage, IList<string> tags, CancellationToken cancellationToken);
   Task<List<DeviceTrend>> GetAllRegistrationInfoAsync();
}
```

## <a name="deploy-the-solution"></a>솔루션 배포

샘플을 실행하려면 다음과 같은 필수 구성 요소가 필요합니다.

- Azure 구독 Azure 구독이 없는 경우 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Android에 푸시 알림을 설정하는 Firebase 계정.
- iOS에 푸시 알림을 설정하는 Apple 개발자 계정.
- ASP.NET Core 및 UWP 애플리케이션 개발 지원과 함께 Visual Studio 2019가 설치된 Windows 컴퓨터.
- Android 애플리케이션을 실행하는 경우 개발 머신의 Android SDK 및 Android Studio.
- iOS 애플리케이션을 실행하는 경우 XCode 및 최신 iOS SDK가 있는 Mac OSX 머신.

### <a name="deploy-resources"></a>리소스 배포

- **[Azure Notification Hub 인스턴스](#create-resource-notification-hub):** Azure에 구성된 알림 허브입니다.
- **[SQL Server 데이터베이스 인스턴스](#create-resource-sql-database):** Azure에서 호스트되는 SQL Server 데이터베이스입니다.
- **ASP.NET 앱 백 엔드:** .NET 5.0을 통해 빌드된 Web API 백 엔드는 Azure App Service로 호스팅되는 Azure Notification Hub와 연결됩니다. 자세한 내용은 [빠른 시작: ASP.NET 웹앱 배포](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=net50)를 참조하세요.

모든 리소스를 수동으로 배포하지 않으려면 GitHub 리포지토리에 제공된 Azure Resource Manager template.json 파일을 사용하여 한 단계에서 필요한 모든 인스턴스를 배포할 수 있습니다. 템플릿 파일은 /azure-template의 리포지터리에서 사용할 수 있습니다. Resource Manager 템플릿을 사용하는 방법에 대한 자세한 내용은 [자습서: Azure 빠른 시작 템플릿 사용](../azure-resource-manager/templates/template-tutorial-quickstart-template.md?tabs=azure-powershell)을 참조하세요.

### <a name="set-up-notifications-for-android-and-ios"></a>Android 및 iOS에 대한 알림 설정

- **Firebase:** Android에서 알림을 받으려면 Firebase 서비스를 구성하고 Azure 알림 허브 인스턴스에 연결합니다. 자세한 내용은 [Google Firebase 설정 구성](configure-google-firebase-cloud-messaging.md)을 참조하세요.
- **APNS(Apple Push Notification Service):** iOS에서 알림을 받으려면 Apple 개발자 계정을 사용하여 APNS 서비스를 구성하고 Azure 알림 허브 인스턴스에 연결합니다. 자세한 내용은 [Apple Push Notification Service 설정 구성](configure-apple-push-notification-service.md)을 참조하세요.

## <a name="build-the-solution"></a>솔루션 빌드

샘플을 빌드하려면 다음 단계를 수행합니다.

### <a name="create-resource-sql-database"></a>리소스 만들기: SQL 데이터베이스

Azure Portal에서 [SQL Server 데이터베이스 인스턴스를 만듭니다](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal). 예를 들면 다음과 같습니다.

:::image type="content" source="media/uwp-react/resources-sql.png" alt-text="SQL 인스턴스 리소스":::

:::image type="content" source="media/uwp-react/template-screenshot.png" alt-text="배포 템플릿":::

### <a name="create-resource-notification-hub"></a>리소스 만들기: 알림 허브

다음과 같이 Azure Portal에서 알림 허브를 만듭니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-backend"></a>백 엔드 구성

앱 백 엔드를 구성하려면 **/NotificationHub.Sample.API/appsettings.json** 파일을 찾아 SQL Server 연결 문자열을 구성합니다.

```json
"ConnectionStrings": {
    "SQLServerConnectionString": "Server=tcp:<SERVER_NAME>,1433;Initial Catalog=<DB_NAME>;Persist Security Info=False;User ID=<DB_USER_NAME>;Password=<PASSWORD>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
  },
```

`<SERVER_NAME>`을 SQL Server의 이름으로, `<DB_NAME>`을 배포된 데이터베이스 URL로, `<DB_USER_NAME>`을 구성된 사용자 이름으로, `<PASSWORD>`를 구성된 암호로 바꿉니다.

API 솔루션을 로컬로 또는 IIS 서버에서 실행하거나 Azure Web App Service로 배포할 수 있습니다. API의 URL을 사용하기 편한 곳에 두세요.

### <a name="build-the-notificationhubsampleapi-application"></a>NotificationHub.Sample.API 애플리케이션 빌드

1. Visual Studio에서 /NotificationHubSample/NotificationHub.Sample.API/NotificationHub.Sample.API.sln 솔루션을 로드합니다.
2. **빌드** 메뉴에서 **솔루션 빌드** 를 선택합니다.
3. Azure에 솔루션 게시: 솔루션 탐색기에서 **NotificationHub.Sample.API** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **게시** 를 선택합니다.
4. **게시** 대화 상자에서 **Azure**(첫 번째 옵션)를 선택한 후, **다음** 을 선택합니다.
5. **Azure App Service(Windows)** 를 선택한 후, **다음** 을 선택합니다.
6. 적절한 구독 이름 및 AppService 인스턴스를 선택할 수 있는 다음 대화 상자로 리디렉션됩니다. 백 엔드가 Azure에 배포되고 새 URL로 리디렉션됩니다.

   :::image type="content" source="media/uwp-react/publish.png" alt-text="프로젝트 게시":::

7. 백 엔드가 게시되면 생성된 URL을 **/app** 폴더 아래에 있는 **config.js** 에 추가합니다. URL 뒤에 `/api/`를 추가해야 합니다.

## <a name="run-react-native-frontend-application-for-windows"></a>Windows용 React Native 프런트 엔드 애플리케이션 실행

애플리케이션에는 모바일 애플리케이션(Android 또는 iOS) 및 UWP 관리자 애플리케이션이 동시에 실행되어야 합니다. 다음 단계에 따라 두 애플리케이션을 모두 실행합니다.

원하는 터미널 또는 셸 창에서 **앱** 폴더를 엽니다. 그런 후 다음을 수행합니다.

### <a name="windows"></a>Windows

1. `npm install`을 실행하여 모든 패키지 종속성을 설치합니다.
2. `npm run start`를 실행하여 한 콘솔 창에서 메트로 서버를 시작합니다.
3. 다른 터미널 창을 열고 `npx react-native run-windows`를 실행하여 UWP 애플리케이션을 실행합니다.
4. 배포 또는 빌드가 실패하는 경우 문제 해결 가이드를 참조하세요.

### <a name="android"></a>Android

1. React-Native 프로젝트에서 Firebase를 구성하여 알림 기능을 사용할 수 있는지 확인합니다.
2. Firebase 프로젝트를 성공적으로 구성한 후 Firebase 포털에서 **google-services.json** 파일을 다운로드합니다.
3. **./app/android/app/google-services.json** 을 이 새 파일로 바꿉니다. 애플리케이션 패키지 이름이 Firebase에 구성된 이름과 일치하는지 확인합니다. 패키지 이름은 **AndroidManifest.xml** 파일에서 구성됩니다.
4. React Native 애플리케이션에서 알림을 구성하는 방법에 대한 자세한 내용은 [자습서: React Native에 푸시 알림 보내기](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)를 참조하세요.
5. 알림이 구성되면 `npm run start`를 실행하여 한 콘솔 창에서 메트로 서버를 시작합니다. Windows 애플리케이션이 이미 실행 중인 경우 이 단계를 건너뛸 수 있습니다.
6. 새 콘솔 창에서 `npx react-native run-android`를 실행하여 Android 애플리케이션을 실행합니다.

### <a name="ios"></a>iOS

1. React-Native 프로젝트에서 APNS를 구성하여 알림 기능을 사용할 수 있는지 확인합니다.
2. iOS에서는 App Store 또는 TestFlight를 통해 서명되고 설치된 애플리케이션에서만 알림을 받을 수 있습니다. Apple 개발자 계정에서 애플리케이션을 만들어야 합니다. Apple 개발자 계정에 구성된 애플리케이션의 번들 식별자는 애플리케이션의 **Info.plist** 및 **Entitlements.plist** 파일에서 구성해야 합니다.
3. React Native 애플리케이션에서 알림을 구성하는 방법에 대한 자세한 내용은 [자습서: React Native에 푸시 알림 보내기](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)를 참조하세요.
4. 알림이 구성되면 `npm run start`를 실행하여 한 콘솔 창에서 메트로 서버를 시작합니다. Windows 애플리케이션이 이미 실행 중인 경우 이 단계를 건너뛸 수 있습니다.
5. 새 콘솔 창에서 `npx react-native run-ios`를 실행하여 iOS 애플리케이션을 실행합니다. 앞서 언급했듯이 알림은 로컬로 배포된 경우 iOS에서 작동하지 않습니다. iOS 시뮬레이터에서는 알림을 받을 수 없습니다.

## <a name="troubleshooting"></a>문제 해결

Windows 애플리케이션용 React Native를 실행하는 동안 다음과 같은 오류가 발생할 수 있습니다.

`error MSB4057: The target "Deploy" does not exist in the project`

이는 Windows용 React Native에서 알려진 문제입니다. 이 문제를 해결하려면 **app/windows** 폴더에서 **app.sln** 을 연 후 Visual Studio에서 **CheckboxWindows** 및 **ReactNativeAsyncStorage** 프로젝트에 대한 **.csproj** 파일을 언로드합니다. 그런 다음, **app.csproj** 파일에서 다음 줄을 `...</Project>` 바로 앞에 추가한 다음, 프로젝트를 다시 로드합니다.

```xml
<Target Name="Deploy"/>
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Notification Hubs를 사용하여 React Native 앱에 푸시 알림 보내기](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)
- [자습서: Firebase SDK를 사용하여 Android 디바이스에 푸시 알림 보내기](android-sdk.md)
- [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Windows용 React Native](https://microsoft.github.io/react-native-windows/)