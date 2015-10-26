<properties
   pageTitle="템플릿을 사용하는 VM의 사용자 지정 스크립트 | Microsoft Azure"
   description="리소스 관리자 템플릿과 함께 사용자 지정 스크립트 확장을 사용하여 Windows 및 Linux Azure VM 구성 작업을 자동화합니다."
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/01/2015"
   ms.author="kundanap"/>

# Azure 리소스 관리자 템플릿과 함께 사용자 지정 스크립트 확장 사용

이 문서에서는 Linux 또는 Windows VM의 작업을 부트스트랩하기 위해 사용자 지정 스크립트 확장을 사용하여 Azure 리소스 관리자 템플릿을 작성하는 방법을 간략하게 설명합니다.

사용자 지정 스크립트 확장의 개요는 [이](virtual-machines-extensions-customscript.md) 문서를 참조하세요.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

사용자 지정 스크립트 확장은 도입된 이후 Windows 및 Linux VM 둘 다에서 작업을 구성하는 데 널리 사용되었습니다. Azure 리소스 관리자 템플릿이 도입되면서 이제 사용자는 VM을 프로비전할 뿐만 아니라 VM의 작업을 구성하는 단일 템플릿을 만들 수 있습니다.

## Azure 리소스 관리자 템플릿 개요

Azure 리소스 관리자 템플릿을 사용하면 리소스 간의 종속성을 정의하여 JSON 언어에서 Azure IaaS 인프라를 선언적으로 지정할 수 있습니다. Azure 리소스 관리자 템플릿에 대한 자세한 개요는 아래 문서를 참조하세요.

<a href="https://azure.microsoft.com/ko-KR/documentation/articles/resource-group-overview/" target="_blank">리소스 그룹 개요</a> <br/> <a href="https://azure.microsoft.com/ko-KR/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Azure CLI을 사용하여 템플릿 배포</a> <br/> <a href="https://azure.microsoft.com/ko-KR/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Azure PowerShell을 사용하여 템플릿 배포</a>

### 사용자 지정 스크립트 확장을 실행하기 위한 필수 조건

1. <a href="http://azure.microsoft.com/downloads" target="_blank">여기</a>서 최신 Azure PowerShell Cmdlet 또는 Azure CLI를 설치합니다.
2. 기존 VM에서 스크립트를 실행하려는 경우, 해당 VM에서 VM 에이전트를 사용하도록 설정되어 있는지 확인하고, 사용하도록 설정되어 있지 않으면 이 <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">문서</a>의 지침에 따라 설치합니다.
3. VM에서 실행할 스크립트를 Azure 저장소에 업로드합니다. 단일 저장소 컨테이너 또는 여러 저장소 컨테이너의 스크립트를 업로드할 수 있습니다.
4. 또는 스크립트를 Github 계정에 업로드할 수 있습니다.
5. 확장을 통해 시작되는 엔트리 스크립트가 다른 스크립트를 시작하는 방식으로 스크립트를 작성해야 합니다.

## 템플릿과 함께 사용자 지정 스크립트 확장 사용 개요:

템플릿을 배포하는 경우 Azure 서비스 관리 API에 사용할 수 있는 동일한 버전의 사용자 지정 스크립트 확장을 사용합니다. 확장은 Azure 저장소 계정 또는 Github 위치에 파일을 업로드하는 동일한 매개 변수 및 시나리오를 지원합니다. 템플릿을 사용하는 동안 중요한 차이점은 majorversion.* 형식에서 버전을 지정하는 대신 확장의 정확한 버전을 지정해야 한다는 점입니다.

 ## Linux VM의 사용자 지정 스크립트 확장에 대한 템플릿 코드 조각

템플릿의 리소스 섹션에서 다음 확장 리소스 정의

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Windows VM의 사용자 지정 스크립트 확장에 대한 템플릿 코드 조각

템플릿의 리소스 섹션에서 다음 리소스 정의

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

위의 예제에서 사용자 고유의 설정으로 파일 URL 및 파일 이름을 바꿉니다.

템플릿을 제작한 후에 Azure CLI 또는 Azure Powershell를 사용하여 배포할 수 있습니다.

사용자 지정 스크립트 확장을 사용하여 VM에서 응용 프로그램을 구성하는 전체 샘플은 아래 예제를 참조하세요.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Linux VM의 사용자 지정 스크립트 확장</a> </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Windows VM의 사용자 지정 스크립트 확장</a>

<!---HONumber=Oct15_HO3-->