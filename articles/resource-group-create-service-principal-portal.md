<properties
   pageTitle="포털에서 Active Directory 응용 프로그램 만들기 | Microsoft Azure"
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
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# 포털을 사용하여 리소스에 액세스할 수 있는 Active Directory 응용 프로그램 만들기

리소스를 액세스하거나 수정해야 하는 자동화된 프로세스 또는 응용 프로그램이 있는 경우 Active Directory 응용 프로그램을 설정하고 필수 사용 권한을 할당해야 합니다. 이 항목에서는 포털을 통해 이러한 단계를 수행하는 방법을 보여 줍니다. 현재 클래식 포털을 사용하여 새 Active Directory 응용 프로그램을 만든 후 Azure 포털로 전환하여 응용 프로그램에 역할을 할당해야 합니다.

Active Directory 응용 프로그램에 대한 다음과 같은 두 가지 인증 옵션을 사용할 수 있습니다.

1. 응용 프로그램에 대한 ID 및 인증 키를 만들고 응용 프로그램을 실행할 때 해당 자격 증명을 제공합니다. 사용자 개입 없이 실행되는 자동화된 프로세스에 대해 이 옵션을 사용합니다.
2. 사용자가 응용 프로그램을 통해 Azure에 로그인할 수 있도록 한 다음 해당 자격 증명을 사용하여 사용자 대신 리소스에 액세스합니다. 사용자가 실행하는 응용 프로그램에 대해 이 옵션을 사용합니다.

Active Directory 개념에 대한 설명을 보려면 [응용 프로그램 개체 및 서비스 주체 개체](./active-directory/active-directory-application-objects.md)를 참조하세요. Active Directory 인증에 대한 자세한 내용은 [Azure AD에 대한 인증 시나리오](./active-directory/active-directory-authentication-scenarios.md)를 참조하세요.

리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

## Active Directory 응용 프로그램 만들기

1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.

2. 왼쪽 창에서 **Active Directory**를 선택합니다.

     ![Active Directory 선택](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. 새 응용 프로그램을 만드는 데 사용할 Active Directory를 선택합니다. 둘 이상의 Active Directory가 있는 경우 일반적으로 구독이 있는 디렉터리에서 응용 프로그램을 만들려고 합니다. 구독과 동일한 디렉터리에 있는 응용 프로그램에 대해서만 구독에 있는 리소스에 대한 액세스 권한을 부여할 수 있습니다.

     ![디렉터리 선택](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    구독을 위한 디렉터리를 찾아야 할 경우 **설정**을 선택하고 디렉터리 이름을 찾습니다.
   
     ![기본 디렉터리 찾기](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. 디렉터리에서 응용 프로그램을 보려면 **응용 프로그램**을 클릭합니다.

     ![응용 프로그램 보기](./media/resource-group-create-service-principal-portal/view-applications.png)

4. 해당 디렉터리에서 응용 프로그램을 만든 적이 없는 경우 다음과 비슷한 이미지가 표시됩니다. **응용 프로그램 추가**를 클릭합니다.

     ![응용 프로그램 추가](./media/resource-group-create-service-principal-portal/create-application.png)

     또는 아래쪽 창에서 **추가**를 클릭합니다.

     ![추가](./media/resource-group-create-service-principal-portal/add-icon.png)

5. 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다.

     ![새 응용 프로그램](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. 응용 프로그램의 이름을 입력하고 만들 응용 프로그램의 유형을 선택합니다. 이 자습서에서는 **웹 응용 프로그램 및/또는 웹 API**를 만들기로 선택하고 다음 단추를 클릭합니다. **네이티브 클라이언트 응용 프로그램**을 선택하는 경우 이 문서의 나머지 단계가 작업 환경과 일치하지 않게 됩니다.

     ![응용 프로그램 이름 지정](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. 앱에 대한 속성을 입력합니다. **로그온 URL**의 경우 응용 프로그램을 설명하는 웹 사이트에 대한 URI를 제공합니다. 웹 사이트의 존재 여부는 확인되지 않습니다. **앱 ID URI**의 경우 응용 프로그램을 식별하는 URI를 제공합니다.

     ![응용 프로그램 속성](./media/resource-group-create-service-principal-portal/app-properties.png)

응용 프로그램이 만들어졌습니다.

## 클라이언트 ID 및 인증 키 가져오기

프로그래밍 방식으로 로그인하는 경우 응용 프로그램에 대한 ID가 필요합니다. 응용 프로그램이 자체 자격 증명에서 실행되는 경우 인증 키도 필요합니다.

1. **구성** 탭을 클릭하여 응용 프로그램의 암호를 구성합니다.

     ![응용 프로그램 구성](./media/resource-group-create-service-principal-portal/application-configure.png)

2. **클라이언트 ID**를 복사합니다.
  
     ![클라이언트 ID](./media/resource-group-create-service-principal-portal/client-id.png)

3. 응용 프로그램이 자체 자격 증명에서 실행되는 경우 **키** 섹션까지 아래로 스크롤하여 암호가 유효한 기간을 선택합니다.

     ![키](./media/resource-group-create-service-principal-portal/create-key.png)

4. **저장**을 선택하여 키를 만듭니다.

     ![저장](./media/resource-group-create-service-principal-portal/save-icon.png)

     저장된 키가 표시되고 키를 복사할 수 있습니다. 나중에 키를 검색할 수 없으므로 지금 복사합니다.

     ![공유 키](./media/resource-group-create-service-principal-portal/save-key.png)

## 테넌트 ID 가져오기

프로그래밍 방식으로 로그인하는 경우 인증 요청과 함께 테넌트 ID를 전달해야 합니다. 웹앱 및 Web API 앱의 경우 아래와 같이 화면 아래쪽에서 **끝점 보기**를 선택하고 ID를 검색하여 테넌트 ID를 검색할 수 있습니다.

   ![테넌트 ID](./media/resource-group-create-service-principal-portal/save-tenant.png)

PowerShell을 통해 테넌트 ID를 검색할 수도 있습니다.

    Get-AzureRmSubscription

또는 Azure CLI:

    azure account show --json

## 위임된 권한 설정

응용 프로그램이 로그인한 사용자를 대신하여 리소스에 액세스하는 경우에는 응용 프로그램이 다른 응용 프로그램에 액세스하도록 위임된 권한을 부여해야 합니다. **구성** 탭의 **다른 응용 프로그램에 대한 권한** 섹션에서 이 작업을 수행합니다. 기본적으로 위임된 권한은 Azure Active Directory에서 사용하도록 이미 설정되어 있습니다. 위임된 권한을 변경하지 않고 그대로 둡니다.

1. **응용 프로그램 추가**를 선택합니다.

2. 목록에서 **Azure Service Management API**를 선택합니다. 그런 후 완료 아이콘을 선택합니다.

      ![앱 선택](./media/resource-group-create-service-principal-portal/select-app.png)

3. 위임된 권한의 드롭다운 목록에서 **조직으로 Azure 서비스 관리에 액세스**를 선택합니다.

      ![사용 권한 선택](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 변경 내용을 저장합니다.

## 다중 테넌트 응용 프로그램 구성

다른 Azure Active Directory의 사용자가 응용 프로그램에 동의하고 로그인할 수 있다면, 다중 테넌트를 사용하도록 설정해야 합니다. **구성** 탭에서 **응용 프로그램이 다중 테넌트입니다.**를 **예**로 설정합니다.

![다중 테넌트](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## 응용 프로그램을 역할에 할당

응용 프로그램이 자체 자격 증명으로 실행되는 경우 응용 프로그램을 역할에 할당해야 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정해야 합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](./active-directory/role-based-access-built-in-roles.md)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정할 수 있습니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어 응용 프로그램에 리소스 그룹에 대한 읽기 권한자 역할을 추가하면 응용 프로그램이 리소스 그룹과 그 안에 포함된 모든 리소스를 읽을 수 있습니다.

1. 응용 프로그램에 역할을 할당하려면 클래식 포털에서 [Azure 포털](https://portal.azure.com)로 전환하세요.

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

Active Directory 응용 프로그램은 현재 리소스에 액세스하도록 구성되어 있습니다. 응용 프로그램에서 자격 증명을 제공하고 액세스 토큰을 받습니다. 리소스 액세스 요청에 대해 이 액세스 토큰을 사용합니다.

응용 프로그램에 프로그래밍 방식으로 로그인할 준비가 되었습니다.

- .NET 예제의 경우 [.NET용 Azure Resource Manager SDK](resource-manager-net-sdk.md)를 참조하세요.
- Java 예제의 경우 [Java용 Azure Resource Manager SDK](resource-manager-java-sdk.md)를 참조하세요.
- Python 예제의 경우 [Python에 대한 리소스 관리 인증](https://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagementauthentication.html)을 참조하세요.
- REST 예제의 경우 [리소스 관리자 REST API](resource-manager-rest-api.md)를 참조하세요.

리소스 관리를 위해 Azure에 응용 프로그램을 통합하는 자세한 단계를 보려면 [Azure Resource Manager API를 사용한 권한 부여 개발자 가이드](resource-manager-api-authentication.md)를 참조하세요.

## 다음 단계

- 보안 정책 지정에 대해 자세히 알아보려면 [Azure 역할 기반 액세스 제어](./active-directory/role-based-access-control-configure.md)를 참조하세요.  
- 이러한 단계에 대한 비디오 데모를 보려면 [Azure Active Directory에서 Azure 리소스의 프로그래밍 방식 관리 활성화](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)를 참조하세요.

<!---HONumber=AcomDC_0525_2016-->