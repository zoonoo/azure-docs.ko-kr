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
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Resource Manager 템플릿과 Windows VM 사용자 지정 스크립트 확장

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Windows VM의 템플릿 예제

템플릿의 리소스 섹션에서 다음 리소스를 정의합니다.

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
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

앞의 예제에서 사용자 고유의 설정으로 파일 URL 및 파일 이름을 바꿉니다. 템플릿을 작성한 후 Azure PowerShell을 사용하여 배포할 수 있습니다.

스크립트 URL 및 매개 변수를 비공개로 유지하려는 경우 스크립트 URL을 **개인**으로 설정할 수 있습니다. 스크립트 URL을 **개인**으로 설정한 경우 보호된 설정으로 전송된 저장소 계정 이름 및 키를 사용해야만 액세스할 수 있습니다. 스크립트 매개 변수도 사용자 지정 스크립트 확장용 버전 1.7 이상을 사용하여 보호된 설정으로 제공할 수 있습니다.

## 보호된 설정을 사용하는 Windows VM에 대한 템플릿 예제

        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
최신 버전 사용자 지정 스크립트 확장의 스키마에 대한 자세한 내용은 [Azure Windows VM 확장 구성 샘플](virtual-machines-windows-extensions-configuration-samples.md)을 참조하세요.

사용자 지정 스크립트 확장을 사용하는 VM의 응용 프로그램 구성 예제에 대해서는 [Windows VM의 사용자 지정 스크립트 확장](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)을 참조하세요.

<!---HONumber=AcomDC_0824_2016-->