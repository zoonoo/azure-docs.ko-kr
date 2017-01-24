---
title: "Log Analytics에 Azure 가상 컴퓨터 연결 | Microsoft Docs"
description: "Azure에서 실행 중인 Windows 및 Linux 가상 컴퓨터의 경우 로그 및 메트릭을 수집하는 좋은 방법은 Log Analytics Azure VM 확장을 설치하는 것입니다. Azure Portal 또는 PowerShell을 사용하여 Azure VM에 Log Analytics 가상 컴퓨터 확장을 설치할 수 있습니다."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: ca39e586-a6af-42fe-862e-80978a58d9b1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: cab40991e5b0628f422b9eb91130d8135c1434f1


---
# <a name="connect-azure-virtual-machines-to-log-analytics"></a>Log Analytics에 Azure 가상 컴퓨터 연결
Windows 및 Linux 컴퓨터의 경우 로그 및 메트릭을 수집하는 좋은 방법은 Log Analytics로 에이전트를 설치하는 것입니다.

Azure 가상 컴퓨터에 Log Analytics 에이전트를 설치하는 가장 쉬운 방법은 Log Analytics VM 확장을 사용하는 것입니다.  이 확장을 사용하면 설치 프로세스가 간소화되고 지정한 Log Analytics 작업 영역에 데이터를 전송하도록 에이전트가 자동으로 구성됩니다. 에이전트도 자동으로 업그레이드되므로 최신 기능 및 수정 사항을 받아볼 수 있습니다.

Windows 가상 컴퓨터에 대해 *Microsoft Monitoring Agent* 가상 컴퓨터 확장을 사용하도록 설정합니다.
Linux 가상 컴퓨터에 대해 *OMS Agent For Linux* 가상 컴퓨터 확장을 사용하도록 설정합니다.

[Azure 가상 컴퓨터 확장](../virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux 에이전트](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대해 자세히 알아봅니다.

로그 데이터에 대해 에이전트 기반 컬렉션을 사용하는 경우 수집할 로그 및 메트릭을 지정하도록 [Log Analytics의 데이터 원본](log-analytics-data-sources.md) 을 구성해야 합니다.

> [!IMPORTANT]
> [Azure Diagnostics](log-analytics-azure-storage.md)를 사용하여 로그 데이터를 인덱싱하도록 Log Analytics를 구성했으며 동일한 로그를 수집하도록 에이전트를 구성하면 해당 로그가 두 번 수집됩니다. 두 데이터 원본 모두에 대해 청구됩니다. 에이전트를 설치한 경우 에이전트만을 사용하여 로그 데이터를 수집해야 합니다. Azure Diagnostics로부터 로그 데이터를 수집하도록 Log Analytics를 구성하지 않습니다.
> 
> 

다음과 같은 3가지 방법으로 Log Analytics 가상 컴퓨터 확장을 사용하도록 설정할 수 있습니다.

* Azure Portal 사용
* Azure PowerShell 사용
* Azure Resource Manager 템플릿을 사용하여

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Azure Portal에서 VM 확장 사용
Log Analytics용 에이전트를 설치하고 [Azure Portal](https://portal.azure.com)을 사용하여 해당 에이전트가 실행되는 Azure 가상 컴퓨터를 연결할 수 있습니다.

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Log Analytics 에이전트를 설치하고 가상 컴퓨터를 Log Analytics 작업 영역에 연결하려면
1. [Azure 포털](http://portal.azure.com)에 로그인합니다.
2. 포털 왼쪽에서 **찾아보기**를 선택한 다음 **Log Analytics(OMS)**로 이동하여 선택합니다.
3. Log Analytics 작업 영역 목록에서 Azure VM에 사용할 작업 영역을 선택합니다.  
   ![OMS 작업 영역](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4. **Log Analytics 관리**에서 **가상 컴퓨터**를 클릭합니다.  
   ![가상 컴퓨터](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5. **가상 컴퓨터**목록에서 에이전트를 설치할 가상 컴퓨터를 선택합니다. VM에 대한 **OMS 연결 상태**가 **연결되지 않음**을 나타냅니다.   
   ![연결되지 않은 VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6. 가상 컴퓨터에 대한 세부 정보에서 **연결**을 선택합니다. Log Analytics 작업 영역에 대해 에이전트가 자동적으로 설치되고 구성됩니다. 이 프로세스는 몇 분 정도 걸리며 이 동안 OMS 연결 상태는 *연결 중...*입니다.  
   ![VM 연결](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7. 에이전트가 설치 및 연결된 후에는 **OMS 연결** 상태가 업데이트되어 **이 작업 영역**을 표시합니다.  
   ![연결됨](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)

## <a name="enable-the-vm-extension-using-powershell"></a>PowerShell을 통해 VM 확장 사용
Azure 클래식 가상 컴퓨터와 Resource Manager 가상 컴퓨터에 대한 명령이 서로 다릅니다.  다음은 클래식 및 Resource Manager 가상 컴퓨터의 예제입니다.

클래식 가상 컴퓨터의 경우 다음 PowerShell 예제를 사용합니다.

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Resource Manager 가상 컴퓨터의 경우 다음 PowerShell 예제를 사용합니다.

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```
PowerShell을 사용하여 가상 컴퓨터를 구성할 때 **작업 영역 ID** 및 **기본 키**를 제공해야 합니다. OMS 포털의 **설정** 페이지나 앞 예제처럼 PowerShell을 사용하여 ID와 키를 찾을 수 있습니다.

![작업 영역 ID 및 기본 키](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

## <a name="deploy-the-vm-extension-using-a-template"></a>템플릿을 사용하여 VM 확장 배포
Azure Resource Manager로 응용 프로그램의 배포 및 구성을 정의하는 간단한 템플릿을(JSON 형식으로) 만들 수 있습니다. 이 템플릿은 리소스 관리자 템플릿이며 배포를 정의하는 선언적 방법을 제공합니다. 템플릿을 사용하여 수명 주기 내내 응용 프로그램을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.

Resource Manager 템플릿의 일부로 Log Analytics 에이전트를 포함하면 각 가상 컴퓨터를 사전 구성하여 Log Analytics 작업 영역에 보고하게 할 수 있습니다.

리소스 관리자 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

다음은 Microsoft Monitoring Agent 확장이 설치된 Windows 기반 가상 컴퓨터를 배포하는 데 사용되는 Resource Manager 템플릿 예제입니다. 이 템플릿은 일반 가상 컴퓨터 템플릿에 다음이 추가된 것입니다.

* workspaceId 및 workspaceName 매개 변수
* Microsoft.EnterpriseCloud.Monitoring 리소스 확장 섹션
* workspaceId 및 workspaceSharedKey를 조회하기 위한 출력

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMS workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

다음 PowerShell 명령을 사용하여 템플릿을 배포할 수 있습니다.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-windows-virtual-machines"></a>Windows Virtual Machines 문제 해결
*Microsoft Monitoring Agent* VM 에이전트 확장이 설치되지 않거나 문제를 보고할 경우 다음 절차를 수행하여 문제를 해결할 수 있습니다.

1. [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)의 절차를 통해 Azure VM 에이전트가 올바르게 설치되어 작동하는지 확인합니다.
   * VM 에이전트 로그 파일 `C:\WindowsAzure\logs\WaAppAgent.log`를 검토할 수도 있습니다.
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
     * [클래식 VM에 Azure VM 에이전트 설치](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
2. 다음 절차를 통해 Microsoft Monitoring Agent 확장 하트 비트 작업이 실행되고 있는지 확인합니다.
   * 가상 컴퓨터에 로그인합니다.
   * 작업 스케줄러를 열고 `update_azureoperationalinsight_agent_heartbeat` 작업을 찾습니다.
   * 작업이 활성화되었고 1분 간격으로 실행되는지 확인합니다.
   * `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`에서 하트비트 로그 파일을 확인합니다.
3. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`에서 Microsoft Monitoring Agent VM 확장 로그 파일을 검토합니다.
4. 가상 컴퓨터가 PowerShell 스크립트를 실행할 수 있는지 확인합니다.
5. C:\Windows\temp 권한이 변경되지 않았는지 확인합니다.
6. 가상 컴퓨터 `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`에 나타나는 PowerShell 창에서 다음을 입력하여Microsoft Monitoring Agent의 상태를 봅니다. 
7. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`에서 Microsoft Monitoring Agent 설정 로그 파일을 검토합니다.

자세한 내용은 [Windows 확장 문제 해결](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

## <a name="troubleshooting-linux-virtual-machines"></a>Linux Virtual Machines 문제 해결
*OMS Agent for Linux* VM 에이전트 확장이 설치되지 않거나 문제를 보고할 경우 다음 절차를 수행하여 문제를 해결할 수 있습니다.

1. 확장 상태가 *알 수 없음*이면 VM 에이전트 로그 파일 `/var/log/waagent.log`를 검토하여 Azure VM 에이전트가 올바르게 설치되어 작동하고 있는지 확인합니다. 
   * 로그가 없는 경우 VM 에이전트가 설치되지 않은 것입니다.
   * [Linux VM에 Azure VM 에이전트 설치](../virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
2. 그 밖의 비정상 상태에 대해 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 및 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`에서 OMS Agent for Linux VM 확장 로그 파일을 검토합니다. 
3. 확장 상태가 정상이나 데이터가 업로드되지 않는 경우 `/var/opt/microsoft/omsagent/log/omsagent.log`에서 OMS Agent for Linux 로그 파일을 검토합니다.

자세한 내용은 [Linux 확장 문제 해결](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Log Analytics의 데이터 원본](log-analytics-data-sources.md) 을 구성하여 수집할 로그 및 메트릭을 지정합니다.
* 가상 컴퓨터에서 데이터를 수집하려면 [솔루션 갤러리에서 Log Analytics 솔루션을 추가](log-analytics-add-solutions.md)합니다.
* Azure에서 실행 중인 다른 리소스에 대해 [Azure Diagnostics를 사용하여 데이터를 수집](log-analytics-azure-storage.md) 합니다.

Azure에 없는 컴퓨터의 경우, 다음 문서에 설명된 방법을 사용하여 Log Analytics 에이전트를 설치할 수 있습니다.

* [Log Analytics에 Windows 컴퓨터 연결](log-analytics-windows-agents.md)
* [Log Analytics에 Linux 컴퓨터 연결](log-analytics-linux-agents.md)




<!--HONumber=Dec16_HO4-->


