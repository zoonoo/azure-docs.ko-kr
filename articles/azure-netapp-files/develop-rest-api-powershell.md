---
title: PowerShell을 사용 하 여 REST API를 사용 하 여 Azure NetApp Files 개발 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure NetApp Files REST API를 시작 하는 방법을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: 90e88020f735f34d308935f1233fb91c0eddfe32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85961067"
---
# <a name="develop-for-azure-netapp-files-with-rest-api-using-powershell"></a>PowerShell을 사용 하 여 REST API를 사용 하 여 Azure NetApp Files 개발

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. 이 문서는 PowerShell을 사용 하 여 Azure NetApp Files REST API 사용을 시작 하는 데 도움이 됩니다.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API 사양

Azure NetApp Files에 대 한 REST API 사양은 [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)를 통해 게시 됩니다.

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Azure NetApp Files REST API 액세스  

1. 아직 설치하지 않은 경우 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)합니다.
2. Azure AD(Azure Active Directory)에 서비스 주체를 만듭니다.
   1. [충분한 권한](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)이 있는지 확인합니다.

   2. Azure CLI에서 다음 명령을 입력합니다.  

      ```azurepowershell
      $RBAC_SP = az ad sp create-for-rbac --name <YOURSPNAMEGOESHERE> | ConvertFrom-Json         
      ```

      서비스 사용자 정보를 표시 하려면를 입력 하 `$RBAC_SP` 고 enter 키를 누릅니다.

      ```output
      appId       : appID displays here
      displayName : displayName
      name        : http://SP_Name
      password    : super secret password
      tenant      : your tenant shows here
      ```
        
      출력은 변수 개체에 저장 됩니다 `$RBAC_SP` . 여기서는 `$RBAC_SP.appId` , `$RBAC_SP.password` 및 값을 사용 `$RBAC_SP.tenant` 합니다.

3. OAuth 액세스 토큰을 요청합니다.

    이 문서의 예제에서는 PowerShell을 사용 합니다. [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) 및 [Paw](https://paw.cloud/)와 같은 다양한 API 도구를 사용할 수도 있습니다.  

    이제 변수를 사용 하 여 `$RBAC_SP` 전달자 토큰을 가져옵니다. 
    
    ```azurepowershell
    $body = "grant_type=client_credentials&client_id=$($RBAC_SP.appId)&client_secret=$($RBAC_SP.password)&resource=https://management.azure.com/"
    $BearerToken = Invoke-RestMethod -Method Post -body $body -Uri https://login.microsoftonline.com/$($RBAC_SP.tenant)/oauth2/token
    ```
    출력은 전달자 토큰 개체를 제공 합니다. 을 입력 하 여 액세스 토큰을 볼 수 있습니다 `$BearerToken.access_token` . 다음 예제와 같이 표시됩니다.

    ```output
    eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9
    ```

    표시된 토큰은 3600초간 유효합니다. 그 후에는 새 토큰을 요청해야 합니다. 토큰은 변수에 저장 되 고 다음 단계에서 사용 됩니다.

4. `headers` 개체를 만듭니다.

    ```azurepowershell  
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $($BearerToken.access_token)")
    $headers.Add("Content-Type", "application/json")
    ```

5. 테스트 호출을 보내고 REST API에 대한 액세스 권한을 확인할 토큰을 포함합니다.

    ```azurepowershell
    $SubId = (Get-AzureRmContext).Subscription.Id 
    Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SubId/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>API 사용 예제  

이 문서에서는 요청 기준선으로 다음 URL을 사용합니다. 이 URL은 Azure NetApp Files 네임스페이스의 루트를 가리킵니다. 

`https://management.azure.com/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

사용자 고유의 값을 사용 하 여 다음 예제를 실행 하기 전에 변수 값을 할당 해야 합니다. PowerShell 변수는를 입력 하 여 액세스 `$variablename` 합니다.
PowerShell 변수는를 사용 하 여 할당 됩니다 `$variablename = “value”` .

```azurepowershell
$Region = “westus2" 
$ResourceGroup = “MYTest-RG-63" 
$ANFvnetname = “NetAppFilesVnet-63"
$ANFvnetCIDR = “10.63.64.0/18"
$ANFsubnet = “NetAppFilesSubnet-63"
$ANFsubnetCIDR = “10.63.120.0/28"
$ANFsubnetID = “/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname/subnets/$ANFSubnet"
$ANFAccount = “TestoftheAPI"
$ANFCapacityPool = “ANFTestPool"
$ANFServicelevel = “Standard"
$ANFVolume = “ANFTestVolume"
$ANFVolumeShareName = “Share-TEST"
$ANFVolumesize = 100GB
$ANFSnapshot = “ANFTestSnapshot"
```

### <a name="put-request-examples"></a>PUT 요청 예제

다음 예제와 같이 PUT 요청을 사용하여 Azure NetApp Files에 새 개체를 만듭니다. PUT 요청의 본문에는 변경 내용에 대 한 JSON 형식의 데이터가 포함 됩니다. PowerShell 명령에 텍스트로 포함 되거나 파일로 참조 되어야 합니다. 파일의 본문을 참조 하려면 json 예제를 파일에 저장 하 고 `-body (Get-Content @<filename>)` PowerShell 명령에를 추가 합니다.

```azurepowershell
    #create a NetApp account  
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "

    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version"  
```

```azurepowershell
    #create a capacity pool  
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
```

```azurepowershell
    #create a volume  
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
```

```azurepowershell
    #create a volume snapshot
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version"
```

### <a name="json-examples"></a>JSON 예

다음 예제에서는 NetApp 계정을 만드는 방법을 보여 줍니다.

```json
    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 
```

다음 예제에서는 용량 풀을 만드는 방법을 보여 줍니다. 

```json
    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME",
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }
```

다음 예에서는 새 볼륨을 만드는 방법을 보여 줍니다. 볼륨의 기본 프로토콜은 NFSV3입니다. 

```json
    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/$SUBID/resourceGroups/$RESOURCEGROUP/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
        }
    }
```

다음 예제에서는 볼륨의 스냅샷을 만드는 방법을 보여 줍니다. 

```json
    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }
```

> [!NOTE] 
> 스냅샷을 만들려면 `fileSystemId`를 지정해야 합니다.  볼륨에 대한 GET 요청을 사용하여 `fileSystemId` 값을 얻을 수 있습니다. 

### <a name="get-request-examples"></a>GET 요청 예제

리소스가 없는 경우 오류가 발생 합니다. 다음 예제와 같이 GET 요청을 사용하여 구독에서 Azure NetApp Files의 개체를 쿼리합니다.

```azurepowershell
#get NetApp accounts 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get capacity pools for NetApp account 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get volumes in NetApp account & capacity pool 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes?api-version=2019-11-01 | ConvertTo-Json
```

```azurepowershell
#get snapshots for a volume 
Invoke-RestMethod -Method Get -Headers $headers -Uri https://management.azure.com/subscriptions/$SUBID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFACCOUNT/capacityPools/$ANFCAPACITYPOOL/volumes/$ANFVOLUME/snapshots?api-version=2019-11-01 | ConvertTo-Json
```

### <a name="complete-powershell-scripts"></a>전체 PowerShell 스크립트
이 섹션에는 PowerShell에 대 한 샘플 스크립트가 나와 있습니다.

```azurepowershell
    <#
    Disclaimer 
    The sample scripts are not supported under any Microsoft standard support program or service. The sample scripts are provided AS IS without warranty of any kind. Microsoft further disclaims all implied warranties including, without limitation, any implied warranties of merchantability or of fitness for a particular purpose. The entire risk arising out of the use or performance of the sample scripts and documentation remains with you. In no event shall Microsoft, its authors, or anyone else involved in the creation, production, or delivery of the scripts be liable for any damages whatsoever (including, without limitation, damages for loss of business profits, business interruption, loss of business information, or other pecuniary loss) arising out of the use of or inability to use the sample scripts or documentation, even if Microsoft has been advised of the possibility of such damages.
    #>
    
    $Region = "westus2"
    $SubID = (Get-AzureRmContext).Subscription.Id
    $MyRandomID =  Get-Random -Minimum 100 -Maximum 999
    $ResourceGroup = "MYTest-RG-" + $MyRandomID
    $ANFAccount = "ANFTestAccount-$Region-" + $MyRandomID
    $ANFCapacityPool =  "ANFTestPool-$Region-" + $MyRandomID
    $ANFVolume = "ANFTestVolume-$Region-" + $MyRandomID
    $ANFVolumesize = 100GB
    $ANFVolumeShareName = "Share-" + $MyRandomID
    $ANFSnapshot = "ANFTestSnapshot-$Region-" + $MyRandomID
    $ANFServicelevel = "Standard"
    $Octet2 = Get-Random -Minimum 1 -Maximum 254
    $ANFvnetname = "NetAppFilesVnet-$Octet2-$Region-" + $MyRandomID
    $ANFvnetCIDR = "10.$Octet2.64.0/18"
    $ANFsubnet = "NetAppFilesSubnet-$Octet2-$Region-" + $MyRandomID
    $ANFsubnetCIDR = "10.$Octet2.120.0/28"
    $vmsubnet = "VM-subnet-$Octet2-$Region-" + $MyRandomID
    $vmsubnetCIDR = "10.$Octet2.121.0/24"
    
    $BearerToken = (az account get-access-token | ConvertFrom-Json).accessToken
    
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer $BearerToken")
    $headers.Add("Content-Type", "application/json")
    
    " ** Creating Resource Group $ResourceGroup *************"
    $api_version = "2020-01-01"
    $body = "    {
      `"location`": `"$Region`",
      `"name`": `"$ResourceGroup`"
    },"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourcegroups/$ResourceGroup`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup`?api-version=$api_version" 
       } 
    
    " ** Creating Virtual Network $ANFvnetname *************"
    $body = "{
      `"properties`": {
        `"addressSpace`": {
          `"addressPrefixes`": [
            `"$ANFvnetCIDR`"
          ]
        },
        `"subnets`": [
          {
            `"name`": `"$ANFsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$ANFsubnetCIDR`",
              `"delegations`": [
                {
                  `"name`": `"Microsoft.NetApp`",
                  `"properties`": {
                    `"serviceName`": `"Microsoft.NetApp/volumes`"
                  }
                }
              ]
            }
          },
         {
            `"name`": `"$vmsubnet`",
            `"properties`": {
              `"addressPrefix`": `"$vmsubnetCIDR`"
                }
         }
        ]
      },
      `"location`": `"$Region`"
    }"
    
    $api_version = "2020-03-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
           sleep 5
           $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.Network/virtualNetworks/$ANFvnetname`?api-version=$api_version" 
           $response.properties.provisioningState
           }  
    $ANFsubnetID = $response.properties.subnets.id[0]
    
    " ** Creating ANF Account $ANFAccount *************"
    $body = "{ 
        `"name`": `"$ANFAccount`", 
        `"type`": `"Microsoft.NetApp/netAppAccounts`", 
        `"location`": `"$Region`", 
        `"properties`": { 
            `"name`": `"$ANFAccount`" 
        }
    } "
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating Capacity Pool $ANFCapacityPool *************"
    $body = "{
      `"location`": `"$Region`",
      `"properties`": {
        `"size`": " + 4TB + ",
        `"serviceLevel`": `"Standard`"
      }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool`?api-version=$api_version" 
    $response.properties.provisioningState
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount`?api-version=$api_version" 
       }  
    
    " ** Creating ANF Volume $ANFVolume *************"
    $body = "{
        `"name`": `"$ANFVolume`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/volumes`",
        `"location`": `"$Region`",
        `"properties`": {
            `"serviceLevel`": `"$ANFServicelevel`",
            `"usageThreshold`": `"$ANFVolumesize`",
            `"creationToken`": `"$ANFVolumeShareName`",
            `"snapshotId`": `"`",
            `"subnetId`": `"$ANFSubnetID`"
        }
    }"
    $api_version = "2020-02-01"
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 15
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume`?api-version=$api_version" 
       } 
    $Volume = $response 
    $FileSystemID = $response.properties.fileSystemId
    
    " ** Creating ANF Volume Snapshot $ANFSnapshot *************"
    $body = "{
        `"name`": `"$ANFAccount/$ANFCapacityPool/$ANFVolume/$ANFSnapshot`",
        `"type`": `"Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots`",
        `"location`": `"$Region`",
        `"properties`": {
            `"name`": `"$ANFSnapshot`",
            `"fileSystemId`": `"$FileSystemID`"
        }
    }"
    $api_version = '2020-02-01'
    $response = Invoke-RestMethod -Method 'PUT' -Headers $headers -Body $body -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
    While ($response.properties.provisioningState -notmatch "Succeeded") {
       $response.properties.provisioningState
       sleep 5
       $response = Invoke-RestMethod -Method ‘GET’ -Headers $headers -Uri "https://management.azure.com/subscriptions/$SubID/resourceGroups/$ResourceGroup/providers/Microsoft.NetApp/netAppAccounts/$ANFAccount/capacityPools/$ANFCapacityPool/volumes/$ANFVolume/Snapshots/$ANFSnapshot`?api-version=$api_version" 
       }  
```

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files REST API 참조 문서 참조](https://docs.microsoft.com/rest/api/netapp/)
