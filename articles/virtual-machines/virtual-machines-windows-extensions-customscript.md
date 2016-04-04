<properties
   pageTitle="템플릿을 사용하는 Windows VM의 사용자 지정 스크립트 | Microsoft Azure"
   description="리소스 관리자 템플릿과 함께 사용자 지정 스크립트 확장을 사용하여 Windows VM 구성 작업을 자동화합니다."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Azure Resource Manager 템플릿을 사용하여 Windows VM에 대한 사용자 지정 스크립트 확장 사용

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Windows VM의 템플릿 예제

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

템플릿을 작성한 후 Azure PowerShell을 사용하여 배포할 수 있습니다.

사용자 지정 스크립트 확장을 사용하여 VM에서 응용 프로그램을 구성하는 전체 샘플은 아래 예제를 참조하세요.

* [Windows VM의 사용자 지정 스크립트 확장](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->