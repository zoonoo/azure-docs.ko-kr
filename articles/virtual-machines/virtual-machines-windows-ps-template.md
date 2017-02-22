<properties
  pageTitle="리소스 관리자 템플릿을 사용하여 VM 만들기 | Microsoft Docs"
  description="리소스 관리자 템플릿 및 PowerShell을 사용하여 새 Windows 가상 컴퓨터를 쉽게 만들 수 있습니다."
  services="virtual-machines-windows" 
  documentationcenter=""
  author="davidmu1"
  manager="timlt"
  editor=""
  tags="azure-resource-manager"/>

<tags
  ms.assetid="19129d61-8c04-4aa9-a01f-361a09466805"
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="01/06/2017"
  ms.author="davidmu"/>


# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기

이 문서에서는 PowerShell을 사용하여 Azure Resource Manager 템플릿을 배포하는 방법을 보여줍니다. [템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 컴퓨터를 배포합니다.

가상 컴퓨터 리소스에 대한 자세한 설명은 [Azure Resource Manager 템플릿의 가상 컴퓨터](virtual-machines-windows-template-description.md)를 참조하세요. 템플릿에 있는 모든 리소스에 대한 자세한 내용은 [Azure Resource Manager 템플릿 연습](../resource-manager-template-walkthrough.md)을 참조하세요.

이 문서의 단계를 수행하려면 약&5;분이 걸립니다.

## <a name="step-1-install-azure-powershell"></a>1단계: Azure PowerShell 설치
최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

## <a name="step-2-create-a-resource-group"></a>2단계: 리소스 그룹 만들기
모든 리소스는 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)에 배포되어야 합니다.

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.
   
  ```powershell   
  Get-AzureRmLocation | sort DisplayName | Select DisplayName
  ```

2. 선택한 위치에서 리소스 그룹을 만듭니다. 이 예에서는 **미국 중부** 지역에 **myResourceGroup**이라는 이름의 리소스 그룹을 만드는 과정을 보여줍니다.

  ```powershell   
  New-AzureRmResourceGroup -Name "myResourceGroup" -Location "Central US"
  ```
   
  다음 예제와 유사한 결과가 표시됩니다.

  ```
  ResourceGroupName : myResourceGroup
  Location          : centralus
  ProvisioningState : Succeeded
  Tags              :
  ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup
  ```

## <a name="step-3-create-the-resources"></a>3단계: 리소스 만들기
템플릿을 배포하고 메시지가 표시되면 매개 변수 값을 제공 합니다. 이 예제에서는 사용자가 만든 리소스 그룹에서 101-vm-simple-windows 템플릿을 배포합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" 
```
VM에서 관리자 계정의 이름, 계정 암호와 DNS 접두사를 제공하도록 요청합니다.

다음 예제와 유사한 결과가 표시됩니다.

```
DeploymentName    : azuredeploy
ResourceGroupName : myResourceGroup
ProvisioningState : Succeeded
Timestamp         : 12/29/2016 8:11:37 PM
Mode              : Incremental
TemplateLink      :
                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/
                    101-vm-simple-windows/azuredeploy.json
                    ContentVersion : 1.0.0.0
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myAdminUser
                    adminPassword    SecureString
                    dnsLabelPrefix   String                     myDomain
                    windowsOSVersion String                     2016-Datacenter
Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ===========
                    hostname         String                     myDomain.centralus.cloudapp.azure.com
DeploymentDebugLogLevel :
```

> [!NOTE]
> 로컬 파일에서 템플릿 및 매개 변수를 배포할 수도 있습니다. 자세한 내용은 [Azure Storage와 함께 Azure PowerShell 사용](../storage/storage-powershell-guide-full.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 살펴봅니다.
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.




<!--HONumber=Jan17_HO2-->


