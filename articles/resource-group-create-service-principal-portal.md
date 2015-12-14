<properties
   pageTitle="포털에서 AD 응용 프로그램 및 서비스 주체 만들기 | Microsoft Azure"
   description="Azure 리소스 관리자에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Active Directory 응용 프로그램 및 서비스 주체를 만드는 방법을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# 포털을 사용하여 Active Directory 응용 프로그램 및 서비스 주체 만들기

## 개요
구독에서 리소스를 액세스하거나 수정해야 하는 자동화된 프로세스 또는 응용 프로그램이 있는 경우 포털을 사용하여 Active Directory 응용 프로그램을 만들고 적절한 권한이 있는 역할에 할당할 수 있습니다. 포털을 통해 Active Directory 응용 프로그램을 만들 때 실제로 응용 프로그램과 서비스 주체를 만듭니다. 사용 권한을 설정하는 경우 서비스 주체를 사용합니다.

이 항목에서는 Azure 포털을 사용하여 새 응용 프로그램 및 서비스 주체를 만드는 방법을 보여줍니다. 현재는 새 Active Directory 응용 프로그램을 만들려면 Microsoft Azure 포털을 사용해야 합니다. 이 기능은 이후 릴리스에서 Azure Preview 포털에 추가될 예정입니다. Preview 포털을 사용하여 응용 프로그램을 역할에 할당할 수 있습니다. Azure PowerShell 또는 Azure CLI를 통해 이러한 단계를 수행할 수도 있습니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](resource-group-authenticate-service-principal.md)을 참조하세요.

## 개념
1. AAD(Azure Active Directory) - 클라우드에 대한 ID 및 액세스 관리 서비스 빌드입니다. 자세한 내용은 [Azure Active Directory란](active-directory/active-directory-whatis.md)을 참조하세요.
2. 서비스 사용자 - 디렉터리의 응용 프로그램 인스턴스입니다.
3. AD 응용 프로그램 - AAD에 응용 프로그램을 식별하는 AAD의 디렉터리 레코드입니다. 

응용 프로그램 및 서비스 주체에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체](active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](active-directory/active-directory-authentication-scenarios.md)를 참조하세요.


## 응용 프로그램 및 서비스 주체 개체 만들기

1. [포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.

2. 왼쪽 창에서 **Active Directory**를 선택합니다.

     ![Active Directory 선택][1]

3. 새 응용 프로그램을 만드는 데 사용할 디렉터리를 선택합니다.

     ![디렉터리 선택][2]

3. 디렉터리에서 응용 프로그램을 보려면 **응용 프로그램**을 클릭합니다.

     ![응용 프로그램 보기][11]

4. 해당 디렉터리에서 응용 프로그램을 만든 적이 없는 경우 다음과 비슷한 이미지가 표시됩니다. **응용 프로그램 추가**를 클릭합니다.

     ![응용 프로그램 추가][6]

     또는 아래쪽 창에서 **추가**를 클릭합니다.

     ![추가][12]

5. 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 갤러리에서 응용 프로그램을 사용하지 않습니다.

     ![새 응용 프로그램][10]

6. 응용 프로그램의 이름을 입력하고 사용할 응용 프로그램의 유형을 선택합니다. 여기서는 이 응용 프로그램의 서비스 사용자를 사용하여 Azure 리소스 관리자로 인증할 것이므로, **웹 응용 프로그램 및/또는 웹 API**를 만들도록 선택한 후 다음 단추를 클릭합니다.

     ![응용 프로그램 이름 지정][9]

7. 앱에 대한 속성을 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. **앱 ID URI**의 경우 응용 프로그램을 식별하는 URI를 제공합니다. 끝점의 고유성 또는 존재 여부는 확인되지 않습니다. **완료**를 클릭하여 AAD 응용 프로그램을 만듭니다.

     ![응용 프로그램 속성][4]

## 응용 프로그램에 대한 인증 키 만들기
이제 포털에서 개발자의 응용 프로그램이 선택되어 있습니다.

1. **구성** 탭을 클릭하여 응용 프로그램의 암호를 구성합니다.

     ![응용 프로그램 구성][3]

2. **키** 섹션까지 아래로 스크롤하여 암호가 유효한 기간을 선택합니다.

     ![키][7]

3. **저장**을 선택하여 키를 만듭니다.

     ![저장][13]

     저장된 키가 표시되고 키를 복사할 수 있습니다. 나중에 키를 검색할 수 없으므로 지금 복사합니다.

     ![공유 키][8]

4. 이제 키를 사용하여 서비스 사용자로 인증할 수 있습니다. 로그인하려면 **키** 이외에 **클라이언트 ID**가 필요합니다. **클라이언트 ID**로 이동하여 ID를 복사합니다.
  
     ![클라이언트 ID][5]

5. 경우에 따라 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 아래와 같이 **끝점 보기**를 선택하고 ID를 검색하여 테넌트 ID를 검색할 수 있습니다.

     ![테넌트 ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

이제 응용 프로그램이 준비되고 테넌트에서 서비스 사용자가 만들어졌습니다. 서비스 사용자로 로그인할 때 다음을 사용해야 합니다.

* **클라이언트 ID** - 사용자 이름으로 사용합니다.
* **키** - 암호로 사용합니다.

## 응용 프로그램을 역할에 할당

작업 수행 권한을 부여하려면 응용 프로그램을 역할에 할당해야 합니다. [Preview 포털](https://portal.azure.com)을 사용하여 올바른 권한을 가진 역할에 Active Directory 응용 프로그램을 할당할 수 있습니다.

Preview 포털에서 액세스 제어를 시작하려면 **액세스** 아이콘을 선택합니다.

![사용자 선택](./media/resource-group-create-service-principal-portal/select-users.png)

응용 프로그램을 할당할 역할을 선택하고 응용 프로그램을 검색합니다.

![사용자 선택](./media/resource-group-create-service-principal-portal/assign-to-role.png)

포털을 통해 역할에 사용자 및 응용 프로그램 할당에 대한 자세한 내용은 [Azure 관리 포털을 사용하여 액세스 관리](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)를 참조하세요.

## 코드에서 액세스 토큰 가져오기

.NET을 사용하는 경우 다음 코드로 응용 프로그램에 대한 액세스 토큰을 검색할 수 있습니다.

먼저, Visual Studio 프로젝트에 Active Directory 인증 라이브러리를 설치해야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 NuGet 패키지를 사용하는 것입니다. 패키지 관리자 콘솔을 열고 다음 명령을 입력합니다.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

응용 프로그램에서 토큰을 검색하는 다음과 같은 메서드를 추가합니다.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## 다음 단계

- 보안 정책 지정에 대해 자세히 알아보려면 [리소스에 대한 액세스 관리 및 감사](resource-group-rbac.md)를 참조하세요.  
- 이러한 단계에 대한 비디오 데모를 보려면 [Azure Active Directory에서 Azure 리소스의 프로그래밍 방식 관리 활성화](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)를 참조하세요.
- Azure PowerShell 또는 Azure CLI를 사용하여 Active Directory 응용 프로그램 및 서비스 주체로 작업 및 인증을 위해 인증서를 사용하는 방법에 대해 자세히 알아보려면 [Azure 리소스 관리자를 사용하여 서비스 주체 인증](./resource-group-authenticate-service-principal.md)을 참조하세요.
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

<!---HONumber=AcomDC_1203_2015-->