---
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65827500"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager 토큰 가져오기
Azure Active Directory는 Azure 리소스 관리자를 사용하여 리소스에서 수행하는 모든 작업을 인증해야 합니다. 아래의 예는 암호 인증을 사용하며, 다른 방법은 [Azure Resource Manager 요청 인증][lnk-authenticate-arm]을 참조하세요.

1. Program.cs의 **Main** 메서드에 다음 코드를 추가하여 애플리케이션 ID 및 암호를 사용해 Azure AD에서 토큰을 검색합니다.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;

    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. **Main** 메서드의 끝에 다음 코드를 추가하여 토큰을 사용하는 **ResourceManagementClient** 개체를 만듭니다.

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. 사용하고 있는 리소스 그룹에 대한 참조를 만들거나 가져옵니다.

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/zh-cn/library/azure/dn790557.aspx