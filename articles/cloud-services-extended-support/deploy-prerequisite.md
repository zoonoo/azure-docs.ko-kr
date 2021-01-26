---
title: Azure Cloud Services 배포를 위한 필수 구성 요소 (확장 지원)
description: Azure Cloud Services 배포를 위한 필수 구성 요소 (확장 지원)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f112d0e96c6ff0caf3c5e3762304158f70963f14
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787032"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Azure Cloud Services 배포를 위한 필수 구성 요소 (확장 지원)

> [!IMPORTANT]
> Cloud Services (확장 지원)은 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

성공적인 Cloud Services (확장 지원) 배포를 보장 하려면 배포를 시도 하기 전에 아래 단계를 검토 하 고 각 항목을 완료 합니다. 

## <a name="register-the-cloudservices-feature"></a>CloudServices 기능 등록
구독에 대 한 기능을 등록 합니다. 등록을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

다음을 사용 하 여 등록 상태를 확인 합니다.  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>필요한 서비스 구성 (.cscfg) 파일 업데이트

### <a name="1-virtual-network"></a>1) Virtual Network
클라우드 서비스 (확장 지원) 배포는 가상 네트워크에 있어야 합니다. [Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal), [PowerShell](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell), [Azure CLI](https://docs.microsoft.com/azure/virtual-network/quick-create-cli) 또는 [ARM 템플릿을](https://docs.microsoft.com/azure/virtual-network/quick-create-template)통해 가상 네트워크를 만들 수 있습니다. [NetworkConfiguration](schema-cscfg-networkconfiguration.md) 섹션 아래의 서비스 구성 (.cscfg) 에서도 가상 네트워크 및 서브넷을 참조 해야 합니다. 

클라우드 서비스와 동일한 리소스 그룹에 속한 가상 네트워크의 경우 서비스 구성 (.cscfg) 파일의 가상 네트워크 이름만 참조 해도 충분 합니다. 가상 네트워크와 클라우드 서비스가 서로 다른 두 리소스 그룹에 있는 경우 가상 네트워크의 전체 Azure Resource Manager ID를 서비스 구성 (.cscfg) 파일에 지정 해야 합니다.
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Virtual Network 동일한 리소스 그룹에 있습니다.
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>다른 리소스 그룹에 있는 가상 네트워크
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) 이전 플러그 인을 제거 합니다.

서비스 구성 (.cscfg) 파일에서 이전 원격 데스크톱 설정을 제거 합니다.  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>필요한 서비스 정의 파일 (.csdef) 업데이트

### <a name="1-virtual-machine-sizes"></a>1) 가상 머신 크기
다음 크기는 Azure Resource Manager에서 더 이상 사용 되지 않습니다. 그러나 계속 사용 하려는 경우에는 `vmsize` 연결 된 Azure Resource Manager 명명 규칙을 사용 하 여 이름을 업데이트 합니다.  

| 이전 크기 이름 | 업데이트 된 크기 이름 | 
|---|---|
| 매우 작음 | Standard_A0 | 
| 소형 | Standard_A1 |
| 중간 | Standard_A2 | 
| 대형 | Standard_A3 | 
| 매우 큼 | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 예를 들어 `<WorkerRole name="WorkerRole1" vmsize="Medium"`는 `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`가 됩니다.
 
> [!NOTE]
> 사용 가능한 크기 목록을 검색 하려면 [리소스 sku](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 를 참조 하 고 다음 필터를 적용 합니다. <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 이전 원격 데스크톱 플러그 인 제거
이전 원격 데스크톱 플러그 인을 사용 하는 배포에는 서비스 정의 (.csdef) 파일 및 연결 된 모든 인증서에서 모듈을 제거 해야 합니다. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Key Vault 만들기 

Key Vault은 Cloud Services (확장 지원)에 연결 된 인증서를 저장 하는 데 사용 됩니다. Key Vault에 인증서를 추가한 다음 서비스 구성 파일에서 인증서 지문을 참조 합니다. 또한 Cloud Services (확장 된 지원) 리소스가 Key Vault에서 암호로 저장 된 인증서를 검색할 수 있도록 적절 한 사용 권한에 대 한 Key Vault를 사용 하도록 설정 해야 합니다. Key Vault는 [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)및  [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)을 통해 만들 수 있습니다. Key Vault 클라우드 서비스와 동일한 지역 및 구독에서 만들어야 합니다. 자세한 내용은 [Azure Cloud Services에서 인증서 사용 (확장 지원)](certificates-and-key-vault.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계 
- Cloud Services (확장 지원)에 대 한 [배포 필수 구성 요소](deploy-prerequisite.md) 를 검토 합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용 하 여 클라우드 서비스 (확장 지원)를 배포 합니다.
- Cloud Services (확장 지원)에 대 한 질문과 [대답](faq.md) 을 검토 합니다.
