---
title: Azure Stack Edge Pro 장치에서 Vm에 대 한 사용자 지정 스크립트 확장 사용
description: 템플릿을 사용 하 여 Azure Stack Edge Pro 장치에서 실행 되는 Vm (가상 컴퓨터)에 사용자 지정 스크립트 확장을 설치 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f622d6e752b658172bce115cde4f30d555e2ed3a
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763855"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro 장치에서 실행 되는 Vm에 사용자 지정 스크립트 확장 배포

사용자 지정 스크립트 확장은 Azure Stack Edge Pro 장치에서 실행 되는 가상 컴퓨터에서 스크립트나 명령을 다운로드 하 고 실행 합니다. 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 사용자 지정 스크립트 확장을 설치 하 고 실행 하는 방법에 대해 자세히 설명 합니다. 

이 문서는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다.

## <a name="about-custom-script-extension"></a>사용자 지정 스크립트 확장 정보

사용자 지정 스크립트 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용 합니다. Azure Storage 또는 액세스할 수 있는 다른 인터넷 위치에서 스크립트를 다운로드 하거나 확장 런타임에 스크립트나 명령을 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합됩니다. Azure CLI, PowerShell 또는 Azure Virtual Machines REST API를 사용하여 실행할 수도 있습니다.

## <a name="os-for-custom-script-extension"></a>사용자 지정 스크립트 확장에 대 한 OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows에서 사용자 지정 스크립트 확장에 대해 지원 되는 OS

Windows 용 사용자 지정 스크립트 확장은 다음 OSs에서 실행 됩니다. 다른 버전은 작동 하지만 Azure Stack Edge Pro 장치에서 실행 되는 Vm에서 내부 테스트 되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Windows Server 2019 | 핵심 |
| Windows Server 2016 | 핵심 |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Linux에서 사용자 지정 스크립트 확장에 대해 지원 되는 OS

Linux 용 사용자 지정 스크립트 확장은 다음 OSs에서 실행 됩니다. 다른 버전은 작동 하지만 Azure Stack Edge Pro 장치에서 실행 되는 Vm에서 내부 테스트 되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Linux: Ubuntu | 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.4 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>필수 구성 요소

1. 클라이언트 컴퓨터에 [VM 템플릿 및 매개 변수 파일을 다운로드](https://aka.ms/ase-vm-templates) 합니다. 작업 디렉터리로 사용할 디렉터리에 압축을 풉니다.

1. VM을 만들어 장치에 배포 해야 합니다. Vm을 만들려면 [템플릿을 사용 하 여 Azure Stack Edge Pro에서 Vm 배포](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)의 모든 단계를 따르세요.

    GitHub 또는 Azure Storage에서와 같이 외부에서 스크립트를 다운로드 해야 하는 경우 compute 네트워크를 구성 하는 동안 계산을 위해 인터넷에 연결 된 포트를 사용 하도록 설정 합니다. 이를 통해 스크립트를 다운로드할 수 있습니다.

    포트 2가 인터넷에 연결 되어 있고 계산 네트워크를 사용 하도록 설정 하는 데 사용 된 예제는 다음과 같습니다. 이전 단계에서 Kubernetes가 필요 하지 않은 것으로 확인 된 경우 Kubernetes 노드 IP 및 외부 서비스 IP 할당을 건너뛸 수 있습니다.    

    ![인터넷에 연결 된 포트에서 계산 설정 사용](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>사용자 지정 스크립트 확장 설치

VM의 운영 체제에 따라 Windows 또는 Linux 용 사용자 지정 스크립트 확장을 설치할 수 있습니다.
 

### <a name="custom-script-extension-for-windows"></a>Windows용 사용자 지정 스크립트 확장

장치에서 실행 되는 VM에 대해 Windows 용 사용자 지정 스크립트 확장을 배포 하려면 `addCSExtWindowsVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿을 배포 `addCSextensiontoVM.json` 합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

이 파일은 `addCSExtWindowsVM.parameters.json` 다음 매개 변수를 사용 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 이름, 확장에 대 한 이름 및 실행 하려는 명령을 제공 합니다.

다음은이 문서에서 사용 된 샘플 매개 변수 파일입니다. 

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>템플릿 배포

템플릿을 배포 `addCSextensiontoVM.json` 합니다. 이 템플릿은 기존 VM에 확장을 배포 합니다. 다음 명령을 실행합니다.

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> 확장 배포는 장기 실행 작업 이므로 완료 하는 데 약 10 분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>배포 추적

지정 된 VM에 대 한 확장의 배포 상태를 확인 하려면 다음 명령을 실행 합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 배포가 완료 되 면이 `ProvisioningState` 로 변경 `Succeeded` 됩니다.

확장 출력은 대상 가상 머신의 다음 폴더 아래에 있는 파일에 기록됩니다. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

지정된 파일은 대상 가상 머신의 다음 폴더에 다운로드됩니다.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
여기서 <n>은 확장의 실행 간에 변경될 수 있는 10진수 정수입니다. 1. * 값은 확장의 실제 현재 값과 일치 합니다 `typeHandlerVersion` . 예를 들어이 인스턴스의 실제 디렉터리는 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` 입니다. 


이 인스턴스에서 사용자 지정 확장 프로그램을 실행 하는 명령은 `md C:\\Users\\Public\\Documents\\test` 입니다. 확장이 성공적으로 설치 되 면 명령의 지정 된 경로에 있는 VM에서 디렉터리가 만들어졌는지 확인할 수 있습니다. 


### <a name="custom-script-extension-for-linux"></a>Linux용 사용자 지정 스크립트 확장

장치에서 실행 되는 VM에 대해 Windows 용 사용자 지정 스크립트 확장을 배포 하려면 `addCSExtLinuxVM.parameters.json` 매개 변수 파일을 편집한 다음 템플릿을 배포 `addCSExtensiontoVM.json` 합니다.

#### <a name="edit-parameters-file"></a>매개 변수 파일 편집

이 파일은 `addCSExtLinuxVM.parameters.json` 다음 매개 변수를 사용 합니다.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
VM 이름, 확장에 대 한 이름 및 실행 하려는 명령을 제공 합니다.

다음은이 문서에서 사용 된 샘플 매개 변수 파일입니다.

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> 확장 배포는 장기 실행 작업 이므로 완료 하는 데 약 10 분이 걸립니다.

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`가 디렉터리에 파일을 만들도록 설정 되었고 `file2.txt` `/home/Administrator` 파일의 내용이 인 `some text` 경우 이 경우 파일이 지정 된 경로에 만들어졌는지 확인할 수 있습니다.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>배포 상태 추적    
    
템플릿 배포는 장기 실행 작업입니다. 지정 된 VM에 대 한 확장의 배포 상태를 확인 하려면 다른 PowerShell 세션 (관리자 권한으로 실행)을 엽니다. 다음 명령을 실행합니다. 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
샘플 출력은 다음과 같습니다. 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 배포가 완료 되 면이 `ProvisioningState` 로 변경 `Succeeded` 됩니다.

확장 실행 출력은 다음 파일에 기록 됩니다 `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>사용자 지정 스크립트 확장 제거

사용자 지정 스크립트 확장을 제거 하려면 다음 명령을 사용 합니다.

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

샘플 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)