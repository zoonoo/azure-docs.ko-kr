<properties
   pageTitle="Azure 포털을 사용하여 새 Azure 서비스 사용자 만들기"
   description="Azure 리소스 관리자에서 리소스에 대한 액세스를 관리하기 위해 역할 기반 액세스 제어와 함께 사용할 수 있는 새 Azure 서비스 사용자를 만드는 방법을 설명합니다."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# Azure 포털을 사용하여 새 Azure 서비스 사용자 만들기

## 개요
서비스 사용자는 다른 리소스에 액세스하는 데 필요한 자동화된 프로세스, 응용 프로그램 또는 서비스입니다. Azure 리소스 관리자를 사용하여 서비스 사용자가 구독에 있거나 테넌트로 존재하는 리소스에 대해 허용된 관리 작업을 수행할 수 있도록 서비스 사용자에게 액세스 권한을 부여하고 서비스 사용자를 인증할 수 있습니다.

이 항목에서는 Azure 포털을 사용하여 새 서비스 사용자를 만드는 방법을 보여 줍니다. 현재는 새 서비스 사용자를 만들려면 Microsoft Azure 포털을 사용해야 합니다. 이 기능은 이후 릴리스에서 Azure Preview 포털에 추가될 예정입니다.

## 개념
1. AAD(Azure Active Directory) - 클라우드에 대한 ID 및 액세스 관리 서비스 빌드입니다. 자세한 내용은 [Azure Active Directory란](./active-directory-whatis/)을 참조하세요.
2. 서비스 사용자 - 디렉터리의 응용 프로그램 인스턴스입니다.
3. AD 응용 프로그램 - AAD에 응용 프로그램을 식별하는 AAD의 디렉터리 레코드입니다. 자세한 내용은 [Azure AD의 인증 기본 사항](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)을 참조하세요.


## Active Directory 응용 프로그램 만들기
1. [클래식 포털](https://manage.windowsazure.com/)을 통해 Azure 계정에 로그인합니다.

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

## 서비스 사용자 암호 만들기
이제 포털에서 개발자의 응용 프로그램이 선택되어 있습니다.

1. **구성** 탭을 클릭하여 응용 프로그램의 암호를 구성합니다.

   ![응용 프로그램 구성][3]

2. **키** 섹션까지 아래로 스크롤하여 암호가 유효한 기간을 선택합니다.

   ![키][7]

3. **저장**을 선택하여 키를 만듭니다.

   ![저장][13]

   저장된 키가 표시되고 키를 복사할 수 있습니다.

   ![공유 키][8]

4. 이제 키를 사용하여 서비스 사용자로 인증할 수 있습니다. 로그인하려면 **키** 이외에 **클라이언트 ID**가 필요합니다. **클라이언트 ID**로 이동하여 ID를 복사합니다.
  
   ![클라이언트 ID][5]


이제 응용 프로그램이 준비되고 테넌트에서 서비스 사용자가 만들어졌습니다. 서비스 사용자로 로그인할 때 다음을 사용해야 합니다.

* **클라이언트 ID** - 사용자 이름으로 사용합니다.
* **키** - 암호로 사용합니다.

## 다음 단계
시작하기

- [Azure 리소스 관리자 개요](./resource-group-overview.md)  
- [Azure 리소스 관리자로 Azure PowerShell 사용](./powershell-azure-resource-manager.md)
- [Azure 리소스 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [Azure 포털을 사용하여 Azure 리소스 관리](azure-portal/resource-group-portal.md)  
  
응용 프로그램 만들기 및 배포
  
- [Azure 리소스 관리자 템플릿 작성](./resource-group-authoring-templates.md)  
- [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](azure-portal/resource-group-template-deploy.md)  
- [Azure에서 리소스 그룹 배포 문제 해결](virtual-machines/resource-group-deploy-debug.md)  
- [Azure 리소스 관리자 템플릿 함수](./resource-group-template-functions.md)  
- [고급 템플릿 작업](./resource-group-advanced-template.md)  
- [.NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포](virtual-machines/arm-template-deployment.md)
  
리소스 구성
  
- [태그를 사용하여 Azure 리소스 구성](./resource-group-using-tags.md)  
  
액세스 관리 및 감사
  
- [리소스에 대한 액세스 관리 및 감사](azure-portal/resource-group-rbac.md)  
- [Azure 리소스 관리자를 사용하여 서비스 사용자 인증](./resource-group-authenticate-service-principal.md)  

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

<!---HONumber=July15_HO1-->