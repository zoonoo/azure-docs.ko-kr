<properties
   pageTitle="리소스 관리자 및 클래식 배포 모델 간 차이점 이해"
   description="리소스 관리자 배포 모델 및 기본(또는 서비스 관리) 배포 모델 간 차이점을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/13/2015"
   ms.author="tomfitz"/>

# 리소스 관리자 배포 및 클래식 배포 이해

리소스 관리자 배포 모델은 응용 프로그램을 구성하는 서비스를 배포하고 관리하는 새로운 방법을 제공합니다. 이 새 모델은 클래식 배포 모델에서 중요한 차이점을 포함하며 두 모델은 서로 완전히 호환되지 않습니다. 리소스의 배포 및 관리를 단순화하기 위해, Microsoft는 새 리소스에 대한 리소스 관리자를 사용하고, 가능한 경우 리소스 관리자를 통해 기존 리소스를 다시 배포할 것을 권장합니다.

서비스 관리 모델로 클래식 배포 모델도 알 수 있습니다.

이 항목은 두 모델 간 차이점 및 클래식 모델에서 리소스 관리자로 전환할 때 발생할 수 있는 문제 중 일부를 설명합니다. 이 모델의 개요를 제공하지만 개별 서비스로 차이점을 자세히 다루지 않습니다. 전환, 계산, 저장소 및 네트워킹 리소스에 관한 자세한 내용은 [Azure 리소스 관리자에서 Azure 계산, 네트워크 및 저장소 공급자](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.

많은 리소스가 클래식 모델 및 리소스 관리자 모두에서 문제 없이 작동합니다. 이 리소스는 클래식 모델에서 만든 경우에도 완벽하게 리소스 관리자를 지원합니다. 문제나 추가 작업 없이 리소스 관리자로 전환할 수 있습니다.

그러나, 몇 리소스 공급자 모델은 모델 간 구조적 차이로 인해 리소스의 두 버전(클래식용, 및 리소스 관리자용)을 제공합니다. 두 모델 간을 구분하는 리소스 공급자는 다음과 같습니다.

- 계산
- 저장소
- 네트워크

이 리소스 종류에 대해 지원되는 작업이 서로 다르기 때문에 사용 중인 버전을 알고 있어야 합니다.

두 모델 간 아키텍처 차이점을 이해하려면 [Azure 리소스 관리자 아키텍처](virtual-machines/virtual-machines-azure-resource-manager-architecture.md)을 참조하세요.

## 리소스 관리자 특성

리소스 관리자를 통해 만든 리소스는 다음 특징을 공유합니다.

- 다음 방법 중 하나를 통해 만들어집니다.

  - [미리 보기 포털](https://portal.azure.com/).

        ![preview portal](./media/resource-manager-deployment-model/preview-portal.png)

        For Compute, Storage, and Networking resources, you have the option of using either Resourece Manager or Classic deployment. Select **Resource Manager**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - PowerShell 명령은 **AzureResourceManager** 모드에서 실행합니다.

            PS C:\> Switch-AzureMode -Name AzureResourceManager

  - REST용 [Azure 리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx) 작업.
  - Azure CLI 명령은 **arm** 모드에서 실행합니다.

            azure config mode arm

- 리소스 종류는 이름에 **(클래식)**을 포함하지 않습니다. 아래 이미지는 형식을 **저장소 계정**으로 표시합니다.

    ![웹앱](./media/resource-manager-deployment-model/resource-manager-type.png)

## 클래식 배포 특성

클래식 배포 모델에서 만든 리소스는 다음 특징을 공유합니다.

- 다음 방법 중 하나를 통해 만들어집니다.

  - [Azure 포털](https://manage.windowsazure.com)

        ![Azure portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the preview portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - PowerShell 명령은 **AzureServiceManagement** 모드에서 실행합니다(기본 모드이며, 그렇지 않은 경우 AzureResourceManager로 구체적으로 전환된 경우, AzureServiceManagement 모드에서 실행 중입니다).

            PS C:\> Switch-AzureMode -Name AzureServiceManagement

  - REST용 [서비스 관리 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) 작업.
  - Azure CLI 명령은 **asm** 또는 기본 모드에서 실행됩니다.
- 리소스 종류는 이름에 **(클래식)**을 포함합니다. 아래 이미지는 형식을 **저장소 계정(클래식)**으로 표시합니다.

    ![클래식 유형](./media/resource-manager-deployment-model/classic-type.png)

여전히 클래식 배포를 통해 생성된 리소스를 관리하는 미리 보기 포털을 사용할 수 있습니다.

## 리소스 관리자 및 리소스 그룹을 사용할 경우의 이점

리소스 관리자는 리소스 그룹의 개념을 추가했습니다. 리소스 관리자를 통해 만드는 모든 리소스는 리소스 그룹 내에 있습니다. 리소스 관리자 배포 모델에는 여러 이점을 제공합니다.

- 이 서비스를 개별적으로 처리하는 것이 아니라 그룹으로 솔루션에 대한 모든 서비스를 배포, 관리 및 모니터링할 수 있습니다.
- 앱 수명 주기 내내 응용 프로그램을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.
- 선언적 템플릿을 사용하여 배포를 정의할 수 있습니다. 
- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.
- 역할 기반 액세스 제어(RBAC)가 관리 플랫폼으로 통합되기 때문에 리소스 그룹의 모든 서비스에 대해 액세스 제어를 적용할 수 있습니다.
- 리소스에 태그를 적용하여 논리적으로 구독에서 모든 리소스를 구성할 수 있습니다.


리소스 관리자 전에 클래식 배포를 통해 만든 모든 리소스는 리소스 그룹 내에서 존재하지 않습니다. 리소스 관리자를 추가할 때 모든 리소스는 기본 리소스 그룹에 소급 추가되었습니다. 이제 클래식 배포를 통해 리소스를 만들 경우, 배포 시 해당 리소스 그룹을 지정하지 않은 경우에도 리소스가 빈 리소스 그룹 내에서 자동으로 생성됩니다. 그러나 리소스 그룹 내에 존재한다 해도도 리소스 관리자 모델로 변환되었음을 의미하지는 않습니다. 클래식 배포를 통해 리소스를 만든 경우 기존 작업을 통해 계속 작업해야 합니다.

다른 리소스 그룹으로 리소스를 이동하고 기존 리소스 그룹에 새 리소스를 추가할 수 있습니다. 따라서 리소스 그룹은 리소스 관리자 및 클래식 배포를 통해 만든 리소스의 혼합을 포함할 수 있습니다. 리소스가 동일한 작업을 지원하지 않기 때문에 이러한 리소스 조합은 예기치 않은 결과를 만들 수 있습니다.

선언적 템플릿을 사용하여 배포를 위해 스크립트를 간소화할 수 있습니다. 서비스 관리에서 리소스 관리자로 기존 스크립트를 변환하는 대신, 템플릿에서 인프라 및 구성을 정의하는 작업을 활용하기 위해 배포 전략을 다시 작업하는 것이 좋습니다.

## 태그 사용

태그를 사용하여 리소스를 논리적으로 구성할 수 있습니다. 리소스 관리자를 통해 만든 리소스만이 태그를 지원합니다. 클래식 리소스에 태그를 적용할 수 없습니다.

리소스 관리자에서 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

## 배포 모델을 위해 지원되는 작업

클래식 배포 모델에서 만든 리소스는 리소스 관리자 작업을 지원하지 않습니다. 경우에 따라 리소스 관리자 명령은 클래식 배포를 통해 생성되는 리소스에 대한 정보를 검색하거나, 다른 리소스 그룹으로 기존 리소스 이동과 같은 관리 작업을 수행할 수 있지만, 이 경우 해당 유형이 리소스 관리자 작업을 지원한다는 느낌을 주어서는 안됩니다. 예를 들어, 리소스 관리자 및 클래식으로 만든 가상 컴퓨터가 포함된 리소스 그룹이 있다고 가정합니다. 다음 PowerShell 명령을 실행하면 모든 가상 컴퓨터를 표시합니다.

    PS C:\> Get-AzureResourceGroup -Name ExampleGroup
    ...
    Resources :
     Name                 Type                                          Location
     ================     ============================================  ========
     ExampleClassicVM     Microsoft.ClassicCompute/domainNames          eastus
     ExampleClassicVM     Microsoft.ClassicCompute/virtualMachines      eastus
     ExampleResourceVM    Microsoft.Compute/virtualMachines             eastus
    ...

그러나 Get-AzureVM 명령을 실행하는 경우, 리소스 관리자로 만든 가상 컴퓨터만을 사용해야 합니다.

    PS C:\> Get-AzureVM -ResourceGroupName ExampleGroup
    ...
    Id       : /subscriptions/xxxx/resourceGroups/ExampleGroup/providers/Microsoft.Compute/virtualMachines/ExampleResourceVM
    Name     : ExampleResourceVM
    ...

일반적으로 클래식 배포를 통해 만든 리소스는 리소스 관리자 명령과 함께 작동되지 않습니다.

리소스 관리자를 통해 생성되는 리소스와 함께 작업하는 경우, 리소스 관리자 작업을 사용해야 하며 서비스 관리 작업으로 다시 전환되지 않습니다.

## 가상 컴퓨터에 대한 고려 사항

가상 컴퓨터 작업 시, 다음과 같은 사항을 고려해야 합니다.

- 클래식 배포 모델에 배포된 가상 컴퓨터는 리소스 관리자를 사용하여 배포된 가상 네트워크에 포함할 수 없습니다.
- 리소스 관리자 배포 모델로 배포된 가상 컴퓨터는 가상 네트워크에 포함되어야 합니다.
- 클래식 배포 모델로 배포된 가상 컴퓨터는 가상 네트워크에 포함되어야 합니다.

클래식 배포에서 리소스 관리자로 전환 시 해당 Azure CLI 명령 목록은 [VM 작업을 위한 해당 리소스 관리자 및 서비스 관리 명령](./virtual-machines/xplat-cli-azure-manage-vm-asm-arm.md)을 참조하세요.

전환, 계산, 저장소 및 네트워킹 리소스에 관한 자세한 내용은 [Azure 리소스 관리자에서 Azure 계산, 네트워크 및 저장소 공급자](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.

## 다음 단계

- 선언적 배포 템플릿 만들기에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 제작](resource-group-authoring-templates.md)을 참조하세요.
- 템플릿 배포을 위한 명령에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=August15_HO8-->