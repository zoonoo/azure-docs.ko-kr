---
title: 관리 되는 id VM 확장 사용을 중지 하 고 Azure Instance Metadata Service 끝점을 사용 하 여 시작
description: 단계별 지침을 제공 VM 확장 사용을 중지 하 고는 Azure 메타 데이터 서비스 IMDS (인스턴스)를 사용 하 여 인증에 대 한 시작 단계입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 6ee8891eae108256875660cc3f2256b65703a1aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406782"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Identities 확장과 Azure Instance Metadata Service를 사용 하 여 시작을 관리 하는 가상 머신 사용을 중지 하는 방법

## <a name="virtual-machine-extension-for-managed-identities"></a>관리 되는 id에 대 한 가상 머신 확장

관리 되는 id에 대 한 가상 머신 확장을 가상 머신 내에서 관리 되는 id에 대 한 토큰 요청을 위해 사용 됩니다. 워크플로의 다음 단계로 구성 됩니다.

1. 리소스 내에서 워크 로드에서 로컬 끝점을 호출 하는 먼저 `http://localhost/oauth2/token` 액세스 토큰을 요청 합니다.
2. 다음 가상 머신 확장 관리 서비스 id에 대 한 자격 증명을 사용 하 여 Azure AD에서 액세스 토큰을 요청 하려면... 
3. 액세스 토큰은 호출자에 게 반환 되 고 Azure Key Vault 또는 Azure Storage와 같은 Azure AD 인증을 지 원하는 서비스에 인증에 사용할 수 있습니다.

다음 섹션에 설명 된 몇 가지 제한으로 인해 관리 되는 id VM 확장은 사용 되지 동일한 끝점에는 Azure 메타 데이터 서비스 IMDS (인스턴스)를 사용 하기 위해

### <a name="provision-the-extension"></a>확장을 프로 비전 

가상 컴퓨터 또는 관리 되는 id를 사용 하도록 설정 하는 가상 머신 확장을 구성할 때 필요에 따라 하도록 선택할 수 있습니다 사용 하 여 Azure 리소스 VM 확장에 대 한 관리 되는 id를 프로 비전 합니다 `-Type` 매개 변수는 [ 집합 AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet. 전달할 수 있습니다 `ManagedIdentityExtensionForWindows` 나 `ManagedIdentityExtensionForLinux`, 가상 컴퓨터의 유형에 따라 및 이름을 사용 하 여는 `-Name` 매개 변수입니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다.

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

다음 JSON을 추가 하 여 VM 확장을 프로 비전 하는 Azure Resource Manager 배포 템플릿을 사용할 수도 있습니다는 `resources` 템플릿에 섹션 (사용 하 여 `ManagedIdentityExtensionForLinux` Linux 버전에 대 한 이름 및 형식 요소에 대 한).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
가상 머신 확장 집합을 사용 하는 경우 또한 프로 비전 할 수 관리 되는 id를 사용 하 여 확장 Azure 리소스가 가상 머신 확장 집합에 대 한 합니다 [추가 AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet. 전달할 수 있습니다 `ManagedIdentityExtensionForWindows` 나 `ManagedIdentityExtensionForLinux`가상 머신 확장의 유형에 따라를 설정 하 고 사용 하 여 이름을 `-Name` 매개 변수입니다. `-Settings` 매개 변수는 토큰 획득을 위해 OAuth 토큰 엔드포인트에서 사용하는 포트를 지정합니다.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
다음 JSON을 추가 가상 머신 확장을 프로 비전 할 Azure Resource Manager 배포 템플릿 사용 하 여 확장을 설정 합니다 `extensionpProfile` 템플릿에 섹션 (사용 하 여 `ManagedIdentityExtensionForLinux` Linux 버전에 대 한 이름 및 형식 요소에 대 한).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

가상 머신 확장을 프로 비전 DNS 조회 오류로 인해 실패할 수 있습니다. 이 경우 가상 머신을 다시 시작 하 고 다시 시도. 

### <a name="remove-the-extension"></a>확장 제거 
확장을 제거 하려면 `-n ManagedIdentityExtensionForWindows` 하거나 `-n ManagedIdentityExtensionForLinux` (형식에 따라 가상 머신)으로 전환 [az vm 확장 삭제](https://docs.microsoft.com/cli/azure/vm/), 또는 [az vmss 확장 삭제](https://docs.microsoft.com/cli/azure/vmss) 가상 머신 확장에 대 한 Azure CLI를 사용 하 여 집합 또는 `Remove-AzVMExtension` Powershell에 대 한 합니다.

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>가상 머신 확장을 사용 하 여 토큰을 획득

다음은 관리 되는 id를 사용 하 여 Azure VM 확장 엔드포인트를 리소스에 대 한 샘플 요청:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 요소 | 설명 |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://localhost:50342/oauth2/token` | Azure 리소스에 대한 관리 ID 엔드포인트입니다. 여기서 50342는 기본 포트이며 구성 가능합니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 또한 발급된 토큰의 `aud` (대상) 클레임에서 표시됩니다. 이 예제에서는 Azure Resource Manager에 액세스할 수 있는 토큰을 요청합니다. 여기에는 https://management.azure.com/ 이라는 앱 ID URI가 포함됩니다. |
| `Metadata` | SSRF(서버 쪽 요청 위조) 공격에 대한 완화 수단으로 Azure 리소스에 대한 관리 ID에서 HTTP 요청 헤더 필드가 필요합니다. 이 값은 모두 소문자이며 "true"로 설정되어야 합니다.|
| `object_id` | (선택 사항) 토큰을 원하는 관리 ID의 object_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|
| `client_id` | (선택 사항) 토큰을 원하는 관리 ID의 client_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|


샘플 응답:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 요소 | 설명 |
| ------- | ----------- |
| `access_token` | 요청된 액세스 토큰입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `refresh_token` | Azure 리소스에 대한 관리 ID에서 사용되지 않습니다. |
| `expires_in` | 액세스 토큰의 발급 시간부터 만료 이전까지 계속 유효한 시간(초)입니다. 발급 시간은 토큰의 `iat` 클레임에서 확인할 수 있습니다. |
| `expires_on` | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함). |
| `not_before` | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함). |
| `resource` | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다. |
| `token_type` | 토큰의 형식은 "전달자" 액세스 토큰입니다. 즉, 리소스가 이 토큰의 전달자에 액세스 권한을 제공할 수 있습니다. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>가상 머신 확장 문제 해결 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>오류가 발생 한 후 가상 머신 확장을 다시 시작

Windows 및 특정 버전의 Linux에서 확장이 중지한 경우 다음 cmdlet 사용하여 수동으로 다시 시작할 수 있습니다.

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

위치: 
- 확장 이름 및 Windows에 대 한 형식 다음과 같습니다. `ManagedIdentityExtensionForWindows`
- 확장 이름 및 Linux에 대 한 형식 다음과 같습니다. `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Azure 리소스에 대한 관리 ID 확장에 대해 스키마 내보내기를 시도할 때 "자동화 스크립트"가 실패함

Azure 리소스에 대 한 id를 관리 되는 가상 머신에서 사용 하는 경우 가상 머신 또는 해당 리소스 그룹에 "Automation 스크립트" 기능을 사용 하려고 할 때 다음 오류가 표시 됩니다.

![Azure 리소스에 대한 관리 ID 자동화 스크립트 내보내기 오류](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Azure 리소스의 가상 머신 확장에 대 한 관리 되는 id 현재 리소스 그룹 템플릿으로 해당 스키마를 내보내는 기능을 지원 하지 않습니다. 결과적으로 생성된 템플릿은 리소스에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 구성 매개 변수를 표시하지 않습니다. 이 섹션의 예제를 수행 하 여 수동으로 추가할 수 있습니다 [는 템플릿을 사용 하 여 Azure 가상 머신에서 Azure 리소스에 대 한 id를 관리 하는 구성](qs-configure-template-windows-vm.md)합니다.

에 나타날 것 (2019 년 1 월에서에서 사용 중단 계획 됨) Azure 리소스가 가상 머신 확장에 대 한 관리 되는 id에 사용할 수 있는 스키마 내보내기 기능 면 [VM 확장을 포함 하는 리소스 그룹 내보내기 ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>가상 머신 확장의 제한 사항 

가상 머신 확장을 사용 하 여 여러 주요 제한이 있습니다. 

 * 가장 심각한 제한은 가상 머신에서 토큰을 요청 하는 데 자격 증명에 저장 됩니다. 성공적으로 가상 컴퓨터를 위반 하는 공격자는 자격 증명을 반출 할 수 있습니다. 
 * 또한 가상 머신 확장을 아직 지원 하지 않는 여러 Linux 배포판, 수정, 빌드 및 해당 배포의 각에 확장을 테스트 하는 비용은 거 대 한 개발을 사용 하 여 합니다. 현재 다음 Linux 배포판은 지원 됩니다. 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * 가상 머신 확장도 프로 비전 하는 경우 처럼 관리 되는 id 사용 하 여 가상 컴퓨터를 배포 하는 데 성능에 주는 영향이 있습니다. 
 * 마지막으로, 가상 머신 확장을 가상 머신 당 32 사용자 할당 관리 id를 가진 지원할 수 있습니다. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

합니다 [Azure 메타 데이터 서비스 IMDS (인스턴스)](/azure/virtual-machines/windows/instance-metadata-service) 는 관리 하 고 가상 컴퓨터를 구성할 수 있는 가상 머신 인스턴스를 실행 하는 방법에 대 한 정보를 제공 하는 REST 끝점입니다. 끝점은 잘 알려진 라우팅이 불가능 IP 주소에서 사용할 수 있습니다 (`169.254.169.254`)는 가상 머신 내 에서만 액세스할 수 있습니다.

Azure IMDS를 사용 하 여에 토큰을 요청 하면 여러 이점이 있습니다. 

1. 서비스는 가상 컴퓨터에 외부, 따라서 관리 되는 id로 사용 되는 자격 증명이 더 이상 가상 컴퓨터. 대신 호스트 되며 Azure 가상 컴퓨터의 호스트 컴퓨터에서 보호 됩니다.   
2. Azure IaaS에서 지원 되는 모든 Windows 및 Linux 운영 체제는 관리 되는 id를 사용할 수 있습니다.
3. VM 확장을 더 이상 프로 비전 해야 하므로 배포는 빠르고 쉽게입니다.
4. IMDS를 사용 하 여 1000 개의 사용자 할당 관리 되는 id까지 끝점에는 단일 가상 머신에 할당할 수 있습니다.
5. IMDS를 사용 하 여 가상 머신 확장을 사용 하는 대신 요청에 중요 한 변경 사항이 없는 사용 하므로 포트 상당히 간단한 현재 가상 머신 확장을 사용 하는 기존 배포 합니다.

이러한 이유로 Azure IMDS 서비스가 가상 머신 확장은 사용 되지 않습니다. 되 면 요청 토큰 수준의 서 됩니다. 


## <a name="next-steps"></a>다음 단계

* [Azure 가상 머신에서 Azure 리소스에 대 한 관리 되는 id를 사용 하 여 액세스 토큰을 획득 하는 방법](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
