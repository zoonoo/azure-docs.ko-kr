---
title: "C# 및 Resource Manager 템플릿을 사용하여 VM 배포 | Microsoft Docs"
description: "C# 및 Resource Manager 템플릿을 사용하여 Azure VM을 배포하는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 738ff9882cffc428f571ab6aea96c0927d2ce443
ms.lasthandoff: 03/31/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# 및 Resource Manager 템플릿을 사용하여 Azure 가상 컴퓨터 배포
이 문서에서는 C#을 사용하여 Azure Resource Manager 템플릿을 배포하는 방법을 보여줍니다. [템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 컴퓨터를 배포합니다.

가상 컴퓨터 리소스에 대한 자세한 설명은 [Azure Resource Manager 템플릿의 가상 컴퓨터](template-description.md)를 참조하세요. 템플릿에 있는 모든 리소스에 대한 자세한 내용은 [Azure Resource Manager 템플릿 연습](../../azure-resource-manager/resource-manager-template-walkthrough.md)을 참조하세요.

이러한 단계를 수행하려면 약 10분이 걸립니다.

## <a name="step-1-create-a-visual-studio-project"></a>1단계: Visual Studio 프로젝트 만들기

이 단계에서는 Visual Studio가 설치되어 있는지 확인하고 템플릿을 배포하는 데 사용하는 콘솔 응용 프로그램을 만듭니다.

1. [Visual Studio](https://www.visualstudio.com/)를 아직 설치하지 않았으면 설치합니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

## <a name="step-2-install-libraries"></a>2단계: 라이브러리 설치

NuGet 패키지는 이러한 단계를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. 리소스를 만들려면 Azure Resource Manager 라이브러리 및 Azure Active Directory 인증 라이브러리가 필요합니다. Visual Studio에서 이러한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭하고 **찾아보기**를 클릭합니다.
2. 검색 상자에 *Microsoft.IdentityModel.Clients.ActiveDirectory*를 입력하고 **설치**를 클릭한 후 지침에 따라 패키지를 설치합니다.
3. 페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.ResourceManager*를 입력하고 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

## <a name="step-3-create-credentials-used-to-authenticate-requests"></a>3단계: 요청을 인증하는 데 사용하는 자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 주체](../../resource-group-authenticate-service-principal.md)에 액세스할 수 있는지 확인합니다. 서비스 주체에서 Azure Resource Manager에서 요청을 인증받기 위한 토큰을 얻을 수 있습니다.

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. Program 클래스에 다음 메서드를 추가하여 자격 증명을 만드는 데 필요한 토큰을 가져옵니다.

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    다음 값을 바꿉니다.
    
    - *{client-id}*를 Azure Active Directory 응용 프로그램의 식별자로 이 식별자는 AD 응용 프로그램의 속성 블레이드에서 찾을 수 있습니다. Azure Portal에서 AD 응용 프로그램을 찾으려면 리소스 메뉴에서 **Azure Active Directory**를 클릭한 다음 **앱 등록**을 클릭합니다.
    - *{client-secret}*을 AD 응용 프로그램의 선택키 이 식별자는 AD 응용 프로그램의 속성 블레이드에서 찾을 수 있습니다.
    - *{tenant-id}*를 구독의 테넌트 식별자로 Azure Portal의 Azure Active Directory에 대한 속성 블레이드에서 테넌트 식별자를 찾을 수 있습니다. *디렉터리 ID*로 레이블이 지정되어 있습니다.

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Program.cs 파일을 저장합니다.

## <a name="step-4-create-a-resource-group"></a>4단계: 리소스 그룹 만들기

템플릿에서 리소스 그룹을 만들 수 있지만 갤러리에서 사용하는 템플릿은 리소스 그룹을 만들지 않습니다. 이 단계에서는 리소스 그룹을 만드릭 위해 코드를 추가합니다.

1. 응용 프로그램에 대한 값을 지정하려면 변수를 프로그램 클래스의 Main 메서드에 추가합니다.

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName;
    var location = "location";
    ```

    다음 값을 바꿉니다.
    
    - *myResourceGroup*을 만들려는 리소스 그룹의 이름으로
    - *subscriptionId*를 구독 ID로 Azure Portal의 구독 블레이드에서 구독 식별자를 찾을 수 있습니다.
    - *deploymentName*을 배포 이름으로
    - *location*를 리소스를 만들려는 [Azure 지역](https://azure.microsoft.com/regions/)으로

2. 리소스 그룹을 만들려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-5-create-a-parameters-file"></a>5단계: 매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 들어 있는 매개 변수 파일을 만듭니다. 매개 변수 파일은 템플릿을 배포할 때 사용합니다. 갤러리에서 사용하는 템플릿은 *adminUserName*, *adminPassword* 및 *dnsLabelPrefix* 매개 변수에 대한 값을 예상합니다.

Visual Studio에서 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**)로 바꿉니다.
2. **웹**을 클릭하고 **JSON 파일**을 선택하고 이름으로 *Parameters.json*을 입력한 다음 **추가**를 클릭합니다.
3. Parameters.json 파일을 열고 다음 JSON 내용을 추가합니다.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    사용자 환경에서 작동하는 값으로 매개 변수 값을 바꿉니다.

4. Parameters.json 파일을 저장합니다.

## <a name="step-6-deploy-a-template"></a>6단계: 템플릿 배포

이 예제에서는 Azure 템플릿 갤러리에서 템플릿을 배포하고 만든 로컬 파일에서 매개 변수 값을 제공합니다. 

1. 템플릿을 배포하려면 Program 클래스에 다음 메서드를 추가합니다.

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    또한 TemplateLink 속성 대신 템플릿 속성을 사용하여 로컬 폴더에서 템플릿을 배포할 수도 있습니다.

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-7-delete-the-resources"></a>7단계: 리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

1. 리소스 그룹을 삭제하려면 Program 클래스에 다음 메서드를 추가합니다.

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. 방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

## <a name="step-8-run-the-console-application"></a>8단계: 콘솔 응용 프로그램 실행

이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. 

1. 콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작** 을 클릭한 다음 구독에 사용되는 동일한 자격 증명을 사용하여 Azure AD에 로그인합니다.

2. *성공* 상태가 표시된 후에 **Enter** 키를 누릅니다. 

    또한 Azure Portal에서 리소스 그룹에 대한 개요 블레이드의 배포에서 **1 성공**이 나타납니다.

3. **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. 배포에 대한 정보를 보려면 배포 상태를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../resource-manager-common-deployment-errors.md)을 살펴봅니다.
* [C#를 사용하여 Azure Virtual Machine 배포](csharp.md)를 검토하여 가상 컴퓨터 및 지원 리소스 배포 방법에 대해 알아 봅니다.
* [Azure Resource Manager 및 C#을 사용하여 Azure Virtual Machines 관리](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.

