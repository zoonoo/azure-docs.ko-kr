---
title: Azure Cloud Services용 CSCFG/CSDEF를 통해 SKU 정보 재정의(추가 지원)
description: Azure Cloud Services용 CSCFG/CSDEF를 통해 SKU 정보 재정의(추가 지원)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739263"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>Cloud Services에서 CSCFG/CSDEF를 통해 SKU 정보 재정의(추가 지원) 

이 기능을 사용하면 사용자가 서비스 구성 및 서비스 정의 파일을 업데이트하지 않고도 **allowModelOverride** 속성을 사용하여 클라우드 서비스에서 역할 크기 및 인스턴스 수를 업데이트할 수 있으므로, 클라우드 서비스는 다시 패키징 및 다시 배포 없이 스케일업/다운/인/아웃할 수 있습니다.

## <a name="set-allowmodeloverride-property"></a>allowModelOverride 속성 설정
allowModelOverride 속성은 다음과 같은 방법으로 설정할 수 있습니다.
* allowModelOverride = true인 경우 API 호출은 csdef 및 cscfg 파일을 사용하여 값의 유효성을 검사하지 않고 클라우드 서비스의 역할 크기 및 인스턴스 수를 업데이트합니다. 
> [!Note]
> cscfg는 역할 인스턴스 수를 반영하도록 업데이트되지만 cspkg 내의 csdef는 이전 값을 유지합니다.
* allowModelOverride = false인 경우 역할 크기 및 인스턴스 수 값이 각각 csdef 및 cscfg 파일과 일치하지 않으면 API 호출에서 오류를 throw합니다.

기본값은 false로 설정됩니다. 속성을 true에서 false로 다시 설정하면 csdef 및 cscfg 파일의 유효성 검사를 다시 확인합니다.

아래 샘플을 통해 PowerShell, 템플릿 및 SDK의 속성을 적용하세요.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
여기서 "allowModelOverride" = true 속성을 설정하면 roleProfile 섹션에 정의된 역할 속성으로 클라우드 서비스를 업데이트합니다.
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
새 New-AzCloudService cmdlet에서 "AllowModelOverride" 스위치를 설정하면 RoleProfile에 정의된 SKU 속성으로 클라우드 서비스를 업데이트합니다.
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK)
AllowModelOverride= true 변수를 설정하면 RoleProfile에 정의된 SKU 속성으로 클라우드 서비스를 업데이트합니다.

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure Portal
포털에서는 위의 속성이 csdef 및 cscfg의 역할 크기 및 인스턴스 수를 재정의하는 것을 허용하지 않습니다. 


## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [사전 요구 사항](deploy-prerequisite.md)을 검토합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
