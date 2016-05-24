<properties
   pageTitle="포털에서 AD 응용 프로그램 및 서비스 주체 만들기 | Microsoft Azure"
   description="Azure 리소스 관리자에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Active Directory 응용 프로그램 및 서비스 주체를 만드는 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="tomfitz"/>

# 포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기

## 개요
리소스를 액세스하거나 수정해야 하는 자동화된 프로세스 또는 응용 프로그램이 있는 경우 클래식 포털을 사용하여 Active Directory 응용 프로그램을 만들 수 있습니다. 응용 프로그램의 ID 또는 응용 프로그램에 로그인한 사용자의 ID로 응용 프로그램을 실행할 수 있습니다. 이러한 두 가지 응용 프로그램 인증 방법은 대화형(사용자 로그인) 및 비대화형(앱이 자체적으로 자격 증명 제공)이라고 합니다. 비대화형 모드에서는 응용 프로그램 ID에 맞는 권한과 함께 역할을 반드시 할당해야 합니다. 앱이 백 엔드 프로세스처럼 무인 모드로 실행되면 비대화형 인증을 사용해야 합니다.

이 항목에서는 클래식 포털을 사용하여 새 응용 프로그램을 만드는 방법을 보여 줍니다. 현재는 새 Active Directory 응용 프로그램을 만들려면 클래식 포털을 사용해야 합니다. 포털을 사용하여 응용 프로그램을 역할에 할당할 수 있습니다.

Azure PowerShell 또는 Azure CLI를 통해 이러한 단계를 수행할 수도 있습니다. 서비스 주체와 함께 PowerShell 또는 CLI를 사용하는 방법에 대한 자세한 내용은 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md)을 참조하세요.

## 개념
1. AAD(Azure Active Directory) - 클라우드에 대한 ID 및 액세스 관리 서비스 빌드입니다. 자세한 내용은 [Azure Active Directory란](active-directory/active-directory-whatis.md)을 참조하세요.
2. AD 응용 프로그램 - 응용 프로그램을 식별하는 Active Directory의 디렉터리 레코드입니다. 
3. 서비스 주체 - 액세스 제어 역할을 적용할 수 있는 응용 프로그램의 인스턴스입니다.

응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](active-directory/active-directory-authentication-scenarios.md)를 참조하세요.


## 응용 프로그램 만들기

대화형 및 비대화형 응용 프로그램의 경우, Active Directory 응용 프로그램을 만들고 구성해야 합니다.

1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.

2. 왼쪽 창에서 **Active Directory**를 선택합니다.

     ![Active Directory 선택][1]
     
3. 새 응용 프로그램을 만드는 데 사용할 디렉터리를 선택합니다. 구독에 있는 리소스의 경우 구독과 동일한 디렉터리에 서비스 주체에 대한 액세스를 할당할 수 있습니다. 일반적으로 구독이 있는 디렉터리에서 응용 프로그램을 만들려고 합니다.

     ![디렉터리 선택][2]
     
    구독을 위한 디렉터리를 찾아야 할 경우 **설정**을 선택하고 디렉터리 이름을 찾습니다.
   
     ![기본 디렉터리 찾기](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. 디렉터리에서 응용 프로그램을 보려면 **응용 프로그램**을 클릭합니다.

     ![응용 프로그램 보기][11]

4. 해당 디렉터리에서 응용 프로그램을 만든 적이 없는 경우 다음과 비슷한 이미지가 표시됩니다. **응용 프로그램 추가**를 클릭합니다.

     ![응용 프로그램 추가][6]

     또는 아래쪽 창에서 **추가**를 클릭합니다.

     ![추가][12]

5. 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 갤러리에서 응용 프로그램을 사용하지 않습니다.

     ![새 응용 프로그램][10]

6. 응용 프로그램의 이름을 입력하고 사용할 응용 프로그램의 유형을 선택합니다. 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **웹 응용 프로그램 및/또는 웹 API**를 만들기로 선택하고 다음 단추를 클릭합니다.

     ![응용 프로그램 이름 지정][9]

7. 앱에 대한 속성을 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. **앱 ID URI**의 경우 응용 프로그램을 식별하는 URI를 제공합니다. 끝점의 고유성 또는 존재 여부는 확인되지 않습니다. 응용 프로그램 유형으로 **네이티브 클라이언트 응용 프로그램**을 선택한 경우에는 **리디렉션 URI** 값을 입력합니다. **완료**를 클릭하여 AAD 응용 프로그램을 만듭니다.

     ![응용 프로그램 속성][4]

응용 프로그램이 만들어졌습니다.

## 클라이언트 ID 및 테넌트 ID 가져오기

프로그래밍 방식으로 응용 프로그램에 액세스하는 경우에는 응용 프로그램에 대한 ID가 필요합니다. **구성** 탭을 선택하고 **클라이언트 ID**를 복사합니다.
  
   ![클라이언트 ID][5]

경우에 따라 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 웹앱 및 Web API 앱의 경우 아래와 같이 화면 아래쪽에서 **끝점 보기**를 선택하고 ID를 검색하여 테넌트 ID를 검색할 수 있습니다.

   ![테넌트 ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

끝점은 네이티브 클라이언트 응용 프로그램에 사용할 수 없습니다. 대신 PowerShell을 통해 테넌트 ID를 검색할 수 있습니다.

    Get-AzureRmSubscription

또는 Azure CLI:

    azure account show --json

## 인증 키 만들기

응용 프로그램이 자체적인 자격 증명으로 실행되는 경우에는 응용 프로그램에 대한 키를 반드시 만들어야 합니다.

1. **구성** 탭을 클릭하여 응용 프로그램의 암호를 구성합니다.

     ![응용 프로그램 구성][3]

2. **키** 섹션까지 아래로 스크롤하여 암호가 유효한 기간을 선택합니다.

     ![키][7]

3. **저장**을 선택하여 키를 만듭니다.

     ![저장][13]

     저장된 키가 표시되고 키를 복사할 수 있습니다. 나중에 키를 검색할 수 없으므로 지금 복사합니다.

     ![공유 키][8]

이제 응용 프로그램이 준비되고 테넌트에서 서비스 사용자가 만들어졌습니다. 서비스 사용자로 로그인할 때 다음을 사용해야 합니다.

* **클라이언트 ID** - 사용자 이름으로 사용합니다.
* **키** - 암호로 사용합니다.

## 위임된 권한 설정

응용 프로그램이 로그인한 사용자를 대신하여 리소스에 액세스하는 경우에는 응용 프로그램이 다른 응용 프로그램에 액세스하도록 위임된 권한을 부여해야 합니다. **구성** 탭의 **다른 응용 프로그램에 대한 권한** 섹션에서 이 작업을 수행합니다. 기본적으로 위임된 권한은 Azure Active Directory에서 사용하도록 이미 설정되어 있습니다. 위임된 권한을 변경하지 않고 그대로 둡니다.

1. **응용 프로그램 추가**를 선택합니다.

2. 목록에서 **Azure Service Management API**를 선택합니다.

      ![앱 선택](./media/resource-group-create-service-principal-portal/select-app.png)

3. **Azure Service Management 액세스(미리 보기)** 위임된 권한을 Service Management API에 추가합니다.

       ![사용 권한 선택](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 변경 내용을 저장합니다.

## 다중 테넌트 응용 프로그램 구성

다른 Azure Active Directory의 사용자가 응용 프로그램에 동의하고 로그인할 수 있다면, 다중 테넌트를 사용하도록 설정해야 합니다. **구성** 탭에서 **응용 프로그램이 다중 테넌트입니다.**를 **예**로 설정합니다.

![다중 테넌트](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## 응용 프로그램을 역할에 할당

응용 프로그램이 로그인한 사용자의 ID로 실행되지 않는 경우에는 응용 프로그램을 역할에 할당하여 작업 수행 권한을 부여해야 합니다. 응용 프로그램에 역할을 할당하려면 클래식 포털에서 [Azure 포털](https://portal.azure.com)로 전환하세요. 응용 프로그램에 어떤 역할을, 어느 범위에서 할당할 것인지 결정해야 합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md)을 참조하세요. 구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 포털에서 응용 프로그램에 할당하려는 범위 수준으로 이동합니다. 이 항목의 경우 리소스 그룹으로 이동한 다음 리소스 그룹 블레이드에서 **액세스** 아이콘을 선택합니다.

     ![사용자 선택](./media/resource-group-create-service-principal-portal/select-users.png)

2. **추가**를 선택합니다.

     ![추가 선택](./media/resource-group-create-service-principal-portal/select-add.png)

3. **읽기 권한자** 역할(또는 응용 프로그램에 할당하고 싶은 역할)을 선택합니다.

     ![역할 선택](./media/resource-group-create-service-principal-portal/select-role.png)

4. 역할에 추가할 수 있는 사용자 목록이 처음 표시될 때에는 응용 프로그램이 표시되지 않고 그룹 및 사용자만 보입니다.

     ![사용자 표시](./media/resource-group-create-service-principal-portal/show-users.png)

5. 응용 프로그램을 찾으려면 응용 프로그램을 검색해야 합니다. 응용 프로그램 이름을 입력하면 사용 가능한 옵션 목록이 변경됩니다. 목록에 응용 프로그램이 표시되면 해당 응용 프로그램을 선택합니다.

     ![역할에 할당](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. **확인**을 선택하여 역할 할당을 완료합니다. 이제 목록에서 리소스 그룹에 대한 역할에 할당된 사용자 목록에 응용 프로그램이 표시될 것입니다.

     ![표시](./media/resource-group-create-service-principal-portal/show-app.png)

포털을 통해 역할에 사용자 및 응용 프로그램 할당에 대한 자세한 내용은 [Azure 관리 포털을 사용하여 액세스 관리](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)를 참조하세요.

## 코드에서 액세스 토큰 가져오기

.NET을 사용하는 경우 다음 코드로 응용 프로그램에 대한 액세스 토큰을 검색할 수 있습니다.

먼저, Visual Studio 프로젝트에 Active Directory 인증 라이브러리를 설치해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 NuGet 패키지를 사용하는 것입니다. 패키지 관리자 콘솔을 열고 다음 명령을 입력합니다.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

클라이언트 ID와 암호로 로그인하려면 다음 방법을 사용하여 토큰을 검색합니다.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

사용자를 대신하여 로그인하려면 다음 방법을 사용하여 토큰을 검색합니다.

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

다음 코드를 사용하여 요청 헤더의 토큰을 전달할 수 있습니다.

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## 다음 단계

- 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.  
- 이러한 단계에 대한 비디오 데모를 보려면 [Azure Active Directory에서 Azure 리소스의 프로그래밍 방식 관리 활성화](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)를 참조하세요.
- Azure PowerShell 또는 Azure CLI를 사용하여 Active Directory 응용 프로그램 및 서비스 주체로 작업 및 인증을 위해 인증서를 사용하는 방법에 대해 자세히 알아보려면 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md)을 참조하세요.
- Azure 리소스 관리자에서 보안 구현에 대한 지침은 [Azure 리소스 관리자에 대한 보안 고려 사항](best-practices-resource-manager-security.md)을 참조하세요.


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0511_2016-->