<properties
   pageTitle="Azure VM 확장을 사용하여 템플릿 작성 | Microsoft Azure"
   description="확장으로 템플릿 작성에 대해 자세히 알아보기"
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
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# VM 확장을 사용하여 Azure 리소스 관리자 템플릿 작성

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.
 

## Azure 리소스 관리자 템플릿 개요

Azure 리소스 관리자 템플릿을 사용하면 Json 언어에서 리소스 간의 종속성을 정의하여 Azure IaaS 인프라를 선언적으로 지정할 수 있습니다. Azure 리소스 관리자 템플릿의 자세한 개요는 아래 문서를 참조하십시오.

[리소스 그룹 개요](../resource-group-overview.md)

## VM 확장에 대한 샘플 템플릿 코드 조각
Azure 리소스 관리자 템플릿의 일부로 VM 확장을 배포하려면 템플릿에 확장 구성을 선언적으로 지정해야 합니다. 다음은 확장 구성을 지정하는 형식입니다.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

위에서 볼 수 있는 것처럼 확장 템플릿은 다음 두 주요 부분으로 구성됩니다.

1. 확장 이름, 게시자 및 버전
2. 확장 구성

## 모든 확장에 대한 게시자, 유형 및 typeHandlerVersion을 식별합니다.

Azure VM 확장은 Microsoft 및 신뢰할 수 있는 타사 게시자가 게시하며 각 확장은 게시자, 유형 및 typeHandlerVersion으로 고유하게 식별됩니다. 다음과 같이 결정될 수 있습니다.

Azure PowerShell에서 다음 Azure PowerShell cmdlet을 실행합니다.

      Get-AzureVMAvailableExtension

Azure CLI에서 다음 Azure PowerShell cmdlet을 실행합니다.

      Azure VM Extension list

이 cmdlet은 다음과 같이 게시자 이름, 확장 이름 및 버전을 반환합니다.

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

이러한 세 속성은 위의 템플릿 조각에서 각각 "게시자", "유형" 및 "typeHandlerVersion"으로 매핑합니다. 참고: 업데이트된 최신 기능을 얻으려면 최신 확장 버전을 사용하는 것이 좋습니다.

## 확장 구성 매개 변수에 대한 스키마를 식별합니다.

확장 템플릿 작성을 사용하는 다음 단계는 구성 매개 변수를 제공하기 위한 형식을 식별하는 것입니다. 각 확장은 자체 매개 변수 집합을 지원합니다.

Windows 확장에 대한 샘플 구성을 보려면 설명서 [Windows 확장 샘플](virtual-machines-extensions-configuration-samples-windows.md)을 클릭하세요.

Linux 확장에 대한 샘플 구성을 보려면 설명서 [Linux 확장 샘플](virtual-machines-extensions-configuration-samples-linux.md)을 클릭하세요.

VM 확장으로 템플릿을 완벽하게 완료하려면 다음 VM 템플릿을 참조하세요.

[Windows VM의 사용자 지정 스크립트 확장](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Linux VM의 사용자 지정 스크립트 확장](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

템플릿을 제작한 후에 Azure CLI 또는 Azure Powershell를 사용하여 배포할 수 있습니다.

<!---HONumber=Oct15_HO3-->