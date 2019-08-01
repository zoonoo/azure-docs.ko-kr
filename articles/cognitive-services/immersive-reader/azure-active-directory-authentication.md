---
title: Azure Active Directory (Azure AD) 인증
titleSuffix: Azure Cognitive Services
description: 몰입 형 판독기 SDK에 대 한 참조
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: de6a29e1c4c102aa7d4038185c1635544ba9dfe2
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688787"
---
# <a name="use-azure-active-directory-azure-ad-authentication-with-the-immersive-reader-service"></a>몰입 형 판독기 서비스에서 Azure Active Directory (Azure AD) 인증 사용

다음 섹션에서는 Azure Cloud Shell 환경 또는 Azure CLI를 사용 하 여 사용자 지정 하위 도메인으로 새 몰입 형 판독기 리소스를 만든 다음 Azure 테 넌 트에서 Azure AD를 구성 합니다. 초기 구성을 완료 한 후에는 몰입 형 판독기 SDK를 사용 하는 경우와 마찬가지로 Azure AD를 호출 하 여 액세스 토큰을 가져옵니다. 이 문제가 발생 하면 각 섹션에서 각 Azure CLI 명령에 대해 사용 가능한 모든 옵션을 사용 하 여 링크가 제공 됩니다.

## <a name="create-an-immersive-reader-resource-with-a-custom-subdomain"></a>사용자 지정 하위 도메인을 사용 하 여 몰입 형 판독기 리소스 만들기

1. [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)를 열어 시작 합니다. 그런 다음 [구독을 선택 합니다](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description).

   ```azurecli-interactive
   Select-AzSubscription -SubscriptionName <YOUR_SUBSCRIPTION>
   ```

2. 다음으로 사용자 지정 하위 도메인을 사용 하 여 [몰입 형 판독기 리소스를 만듭니다](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) .

   >[!NOTE]
   > 하위 도메인 이름은 launchAsync 함수를 사용 하 여 판독기를 시작할 때 몰입 형 판독기 SDK에서 사용 됩니다.

   -F0 (무료 계층) 또는 S0 (표준 계층, 공개 미리 보기 중에도 무료) 일 수 있습니다. S0 계층은 호출 속도가 더 높고 호출 수에 대 한 월간 할당량은 없습니다.

   -Location `eastus`은, `westus`, `australiaeast`, `centralindia` ,,`northeurope`, 중 하나일 수 있습니다. `japaneast``westeurope`

   -CustomSubdomainName은 전역적으로 고유 해야 하며 특수 문자 (예: ".", "!", ",")를 포함할 수 없습니다.


   ```azurecli-interactive
   $resource = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME> -Type ImmersiveReader -SkuName S0 -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>

   // Display the Resource info
   $resource
   ```

   성공 하면 리소스 **끝점** 은 리소스에 고유한 하위 도메인 이름을 표시 해야 합니다.

   여기서는 나중에이 리소스에 대 한 액세스 권한을 부여 하는 데 사용 되므로 새로 만든 리소스 개체를 **$resource** 변수로 캡처합니다.


   >[!NOTE]
   > Azure Portal에서 리소스를 만드는 경우 ' Name ' 리소스가 사용자 지정 하위 도메인으로 사용 됩니다. 리소스 개요 페이지로 이동 하 고 여기에 나열 된 끝점에서 하위 도메인 (예: `https://[SUBDOMAIN].cognitiveservices.azure.com/`)을 찾아 포털에서 하위 도메인 이름을 확인할 수 있습니다. SDK와 통합 하기 위해 하위 도메인을 가져와야 하는 경우 나중에 확인할 수도 있습니다.

   포털에서 리소스를 만든 경우 지금 [기존 리소스를 가져올](https://docs.microsoft.com/powershell/module/az.cognitiveservices/get-azcognitiveservicesaccount?view=azps-1.8.0) 수도 있습니다.

   ```azurecli-interactive
   $resource = Get-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <RESOURCE_NAME>

   // Display the Resource info
   $resource
   ```

## <a name="assign-a-role-to-a-service-principal"></a>서비스 주체에 역할 할당

이제 리소스와 연결 된 사용자 지정 하위 도메인이 있으므로 서비스 주체에 역할을 할당 해야 합니다.

1. 먼저 [AZURE AD 응용 프로그램을 만들어](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)보겠습니다.

   >[!NOTE]
   > ' 클라이언트 암호 ' 라고도 하는 암호는 인증 토큰을 가져올 때 사용 됩니다.

   ```azurecli-interactive
   $password = "<YOUR_PASSWORD>"
   $secureStringPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
   $aadApp = New-AzADApplication -DisplayName ImmersiveReaderAAD -IdentifierUris http://ImmersiveReaderAAD -Password $secureStringPassword

   // Display the Azure AD app info
   $aadApp
   ```

   여기서는 다음 단계에서 사용 하기 위해 새로 만든 Azure AD app 개체를 **$aadApp** 변수로 캡처합니다.

2. 다음으로, Azure AD 응용 프로그램에 대 한 [서비스 주체를 만들어야](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) 합니다.

   ```azurecli-interactive
   $principal = New-AzADServicePrincipal -ApplicationId $aadApp.ApplicationId

   // Display the service principal info
   $principal
   ```

   여기서는 다음 단계에서 사용 하기 위해 새로 만든 서비스 사용자 개체를 **$principal** 변수로 캡처합니다.


3. 마지막 단계는 ["Cognitive Services 사용자" 역할](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) 을 서비스 사용자 (리소스로 범위 지정)에 할당 하는 것입니다. 역할을 할당 하 여 서비스 사용자에 게이 리소스에 대 한 액세스 권한을 부여 합니다. 구독에서 여러 리소스에 대 한 동일한 서비스 사용자 액세스 권한을 부여할 수 있습니다.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId $principal.Id -Scope $resource.Id -RoleDefinitionName "Cognitive Services User"
   ```

   >[!NOTE]
   > 사용자가 만드는 각 몰입 형 판독기 리소스에 대해이 단계를 수행 해야 합니다. 예를 들어 여러 글로벌 지역에 대해 여러 리소스를 만드는 경우 Azure AD 인증이 모든 지역에 대해 작동 하도록 각 지역 리소스에 대해이 단계를 수행 해야 합니다. 또는 나중에 새 리소스를 만드는 경우 해당 새 리소스에 대해서도이 역할 할당 단계를 수행 해야 합니다.


## <a name="obtain-an-azure-ad-token"></a>Azure AD 토큰 가져오기

이 예제에서는 Azure AD 토큰을 가져오기 위해 서비스 주체를 인증 하는 데 암호를 사용 합니다.

1. **TenantId**가져오기:
   ```azurecli-interactive
   $context = Get-AzContext
   $context.Tenant.Id
   ```

2. 토큰 가져오기:
   ```azurecli-interactive
   $authority = "https://login.windows.net/" + $context.Tenant.Id
   $resource = "https://cognitiveservices.azure.com/"
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $aadApp.ApplicationId, $password
   $token = $authContext.AcquireTokenAsync($resource, $clientCredential).Result
   $token
   ```

   >[!NOTE]
   > 몰입 형 판독기 SDK는 토큰의 AccessToken 속성 (예: $token을 사용 합니다. AccessToken. 자세한 내용은 SDK [참조](reference.md) 및 코드 [샘플](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples) 을 참조 하세요.

또는 인증서를 사용 하 여 서비스 사용자를 인증할 수 있습니다. 서비스 주체 외에도 다른 Azure AD 응용 프로그램을 통해 위임 된 사용 권한을 사용 하 여 사용자 보안 주체를 지원 합니다. 이 경우 암호 또는 인증서 대신 토큰을 획득 하는 경우 사용자에 게 2 단계 인증을 요구 하는 메시지가 표시 됩니다.

## <a name="next-steps"></a>다음 단계

* 몰입형 판독기 SDK를 사용하여 수행할 수 있는 다른 작업을 확인하려면 [자습서](./tutorial.md)를 살펴보세요.
* [몰입형 판독기 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기