<properties
   pageTitle="리소스 관리자 및 클래식 배포 | Microsoft Azure"
   description="리소스 관리자 배포 모델 및 기본(또는 서비스 관리) 배포 모델 간 차이점을 설명합니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해

이 토픽에서는 Azure Resource Manager 및 클래식 배포 모델, 리소스 상태, 리소스가 둘 중 하나로 배포된 이유에 대해 알아봅니다. Resource Manager 배포 모델은 클래식 배포 모델에서 중요한 차이점을 포함하며 두 모델은 서로 완전히 호환되지 않습니다. 리소스의 배포 및 관리를 단순화하기 위해, Microsoft는 새 리소스에 대한 리소스 관리자를 사용하고, 가능한 경우 리소스 관리자를 통해 기존 리소스를 다시 배포할 것을 권장합니다.

Resource Manager를 완전히 처음 접하는 경우 먼저 [Azure Resource Manager 개요](resource-group-overview.md)에 정의된 용어를 검토할 수 있습니다.

## 배포 모델 내역

Azure에서는 원래 클래식 배포 모델만 제공했습니다. 이 모델에서는 각 리소스가 독립적으로 존재하며 관련 리소스를 함께 그룹화할 방법이 없습니다. 대신, 어떤 리소스로 솔루션 또는 응용 프로그램이 구성되었는지를 수동으로 추적하고 통합된 방식으로 이를 관리해야 했습니다. 솔루션을 배포하려면 클래식 포털을 통해 각 리소스를 개별적으로 만들거나 모든 리소스를 정확한 순서로 배포하는 스크립트를 만들어야 했습니다. 솔루션을 삭제하려면 각 리소스를 개별적으로 삭제해야 했습니다. 관련 리소스에 대한 액세스 제어 정책을 쉽게 적용 및 업데이트할 수 없었습니다. 마지막으로 리소스를 모니터링하고 청구를 관리하는 데 도움이 되는 조건으로 레이블을 지정하기 위한 태그를 리소스에 적용할 수 없었습니다.

2014년에는 리소스 그룹의 개념을 추가한 Resource Manager가 Azure에 도입되었습니다. 리소스 그룹은 공통 수명 주기를 공유하는 리소스의 컨테이너입니다. 리소스 관리자 배포 모델에는 여러 이점을 제공합니다.

- 이 서비스를 개별적으로 처리하는 것이 아니라 그룹으로 솔루션에 대한 모든 서비스를 배포, 관리 및 모니터링할 수 있습니다.
- 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.
- 리소스 그룹의 모든 리소스에 액세스 제어를 적용할 수 있으며 새 리소스를 리소스 그룹에 추가할 때 해당 정책이 자동으로 적용됩니다.
- 리소스에 태그를 적용하여 논리적으로 구독에서 모든 리소스를 구성할 수 있습니다.
- JSON(JavaScript Object Notation)을 사용하여 솔루션에 대한 인프라를 정의할 수 있습니다. JSON 파일을 Resource Manager 템플릿이라고 합니다.
- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.

리소스 관리자를 추가할 때 모든 리소스는 기본 리소스 그룹에 소급 추가되었습니다. 이제 클래식 배포를 통해 리소스를 만들 경우, 배포 시 해당 리소스 그룹을 지정하지 않은 경우에도 리소스가 해당 서비스에 대한 기본 리소스 그룹 내에서 자동으로 생성됩니다. 그러나 리소스 그룹 내에 존재한다 해도도 리소스 관리자 모델로 변환되었음을 의미하지는 않습니다. 다음 섹션에서는 각 서비스에서 두 배포 모델을 어떻게 처리하는지 살펴보겠습니다.

## 모델에 대한 지원 이해 

리소스에 사용할 배포 모델을 결정할 때는 알아야 할 세 가지 시나리오가 있습니다.

1. 서비스에서 Resource Manager를 지원하고 단일 형식만 제공합니다.
2. 서비스에서 Resource Manager를 지원하지만 두 가지 형식(하나는 Resource Manager용, 하나는 클래식용)을 제공합니다. 이 내용은 가상 컴퓨터, 저장소 계정 및 가상 네트워크에만 적용됩니다.
3. 서비스에서 Resource Manager를 지원하지 않습니다.

서비스에서 Resource Manager를 지원하는지 여부를 검색하려면 [Resource Manager 지원되는 공급자](resource-manager-supported-services.md)를 참조하세요.

사용하려는 서비스에서 Resource Manager를 지원하지 않으면 클래식 배포를 계속해서 사용해야 합니다.

서비스에서 Resource Manager를 지원하고 가상 컴퓨터, 저장소 계정 또는 가상 네트워크가 **아니면** 간단하게 Resource Manager를 사용할 수 있습니다.

가상 컴퓨터, 저장소 계정 및 가상 네트워크의 경우 클래식 배포를 통해 리소스를 만든 경우 기존 작업을 통해 계속 작업해야 합니다. Resource Manager 배포를 통해 가상 컴퓨터, 저장소 계정 또는 가상 네트워크를 만든 경우 Resource Manager 작업을 계속 사용해야 합니다. 이러한 차이는 구독에 Resource Manager 및 클래식 배포를 통해 만든 리소스의 혼합이 포함되는 경우 특히 혼동을 줄 수 있습니다. 리소스가 동일한 작업을 지원하지 않기 때문에 이러한 리소스 조합은 예기치 않은 결과를 만들 수 있습니다.

경우에 따라 리소스 관리자 명령은 클래식 배포를 통해 생성되는 리소스에 대한 정보를 검색하거나, 다른 리소스 그룹으로 기존 리소스 이동과 같은 관리 작업을 수행할 수 있지만, 이 경우 해당 유형이 리소스 관리자 작업을 지원한다는 느낌을 주어서는 안됩니다. 예를 들어 클래식 배포로 만든 가상 컴퓨터가 포함된 리소스 그룹이 있다고 가정합니다. 다음 Resource Manager PowerShell 명령을 실행하는 경우

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

가상 컴퓨터를 반환합니다.
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

그러나 Resource Manager cmdlet **Get-AzureRmVM**만 Resource Manager를 통해 배포된 가상 컴퓨터를 반환합니다. 다음 명령은 클래식 배포를 통해 만든 가상 컴퓨터를 반환하지 않습니다.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

가상 컴퓨터 작업 시, 다음과 같은 기타 중요 사항을 고려해야 합니다.

- 클래식 배포 모델에 배포된 가상 컴퓨터는 리소스 관리자를 사용하여 배포된 가상 네트워크에 포함할 수 없습니다.
- 리소스 관리자 배포 모델로 배포된 가상 컴퓨터는 가상 네트워크에 포함되어야 합니다.
- 클래식 배포 모델로 배포된 가상 컴퓨터는 가상 네트워크에 포함되어야 합니다.

여러 배포 모델에서 가상 네트워크 연결에 대한 자세한 내용은 [클래식 VNet을 새 VNet에 연결](./virtual-network/virtual-networks-arm-asm-s2s.md)을 참조하세요.

리소스 관리자를 통해 만든 리소스만이 태그를 지원합니다. 클래식 리소스에 태그를 적용할 수 없습니다. 리소스 관리자에서 태그 사용에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](resource-group-using-tags.md)을 참조하세요.

## 리소스 관리자 특성

두 모델을 이해하기 위해 Resource Manager 형식의 특성을 검토해 보겠습니다.

- [Azure 포털](https://portal.azure.com/)을 통해 생성되었습니다.

     ![Azure 포털](./media/resource-manager-deployment-model/portal.png)

     계산, 저장, 네트워킹 리소스에 리소스 관리자를 사용할지 클래식 배포를 사용할지 선택할 수 있습니다. **리소스 관리자**를 선택합니다.

     ![리소스 관리자 배포](./media/resource-manager-deployment-model/select-resource-manager.png)

- Azure PowerShell cmdlet의 Resource Manager 버전으로 생성되었습니다. 이 명령은 다음과 같이 *동사-AzureRm명사* 형식을 갖습니다.

        New-AzureRmResourceGroupDeployment

- REST 작업을 위한 [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)를 통해 생성되었습니다.

- **arm** 모드에서 실행되는 Azure CLI 명령을 통해 생성되었습니다.

        azure config mode arm
        azure group deployment create 

- 리소스 종류는 이름에 **(클래식)**을 포함하지 않습니다. 아래 이미지는 형식을 **저장소 계정**으로 표시합니다.

    ![웹앱](./media/resource-manager-deployment-model/resource-manager-type.png)

다음 다이어그램에 표시된 응용 프로그램은 리소스 관리자를 통해 배포된 리소스가 단일 리소스 그룹에 포함되는 방식을 보여줍니다.

  ![Resource Manager 아키텍처](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

또한 다음과 같이 리소스 공급자 내에서 리소스 간에 관계가 있습니다.

- Blob 저장소(필수)에 해당 디스크를 저장하기 위해 가상 컴퓨터는 SRP에서 정의된 특정 저장소 계정에 따라 달라집니다.
- 가상 컴퓨터는 NRP(필수)에서 정의된 특정 NIC 및 CRP(옵션)에서 정의된 가용성 집합을 참조합니다.
- NIC는 가상 컴퓨터의 할당된 IP 주소(필수), 가상 컴퓨터에 대한 가상 네트워크의 서브넷(필수) 및 네트워크 보안 그룹(옵션)을 참조합니다.
- 가상 네트워크 내의 서브넷은 네트워크 보안 그룹(옵션)을 참조합니다.
- 부하 분산 장치 인스턴스는 가상 컴퓨터의 NIC(옵션)를 포함하는 IP 주소의 백 엔드 풀을 참조하며 로드 분산 장치 공용 또는 개인 IP 주소(옵션)를 참조합니다.

## 클래식 배포 특성

서비스 관리 모델로 클래식 배포 모델도 알 수 있습니다.

클래식 배포 모델에서 만든 리소스는 다음 특징을 공유합니다.

- [클래식 포털](https://manage.windowsazure.com)을 통해 생성되었습니다.

     ![클래식 포털](./media/resource-manager-deployment-model/classic-portal.png)

     또는 Azure 포털과 **클래식** 배포(계산, 저장소 및 네트워킹의 경우)를 지정합니다.

     ![클래식 배포](./media/resource-manager-deployment-model/select-classic.png)

- Azure PowerShell cmdlet의 서비스 관리 버전을 통해 생성되었습니다. 이러한 명령의 이름은 다음과 같이 *동사-Azure명사* 형식을 갖습니다.

        New-AzureVM 

- REST 작업을 위한 [서비스 관리 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)를 통해 생성되었습니다.
- **asm** 모드에서 실행되는 Azure CLI 명령을 통해 생성되었습니다.

        azure config mode asm
        azure vm create 

- 리소스 종류는 이름에 **(클래식)**을 포함합니다. 아래 이미지는 형식을 **저장소 계정(클래식)**으로 표시합니다.

    ![클래식 유형](./media/resource-manager-deployment-model/classic-type.png)

여전히 클래식 배포를 통해 생성된 리소스를 관리하는 Azure 포털을 사용할 수 있습니다.

Azure 서비스 관리에서 다음을 통해 가상 컴퓨터 호스팅에 대한 계산, 저장소 또는 계산 리소스가 제공됩니다.

- 가상 컴퓨터 호스팅(계산)을 위한 컨테이너 역할을 하는 데 필요한 클라우드 서비스. 가상 컴퓨터는 NIC(네트워크 인터페이스 카드) 및 Azure에서 할당된 IP 주소와 함께 자동으로 제공됩니다. 또한 클라우드 서비스는 외부 부하 분산 장치 인스턴스, 공용 IP 주소, 기본 끝점을 포함하여 Windows 기반의 가상 컴퓨터를 위한 원격 데스크톱 및 원격 PowerShell 트래픽 및 Linux 기반의 가상 컴퓨터를 위한 SSH(Secure Shell) 트래픽을 허용합니다.
- 운영 체제, 임시 및 추가 데이터 디스크(저장소)를 비롯하여 가상 컴퓨터를 위한 VHD를 저장하는 데 필요한 저장소 계정.
- 서브넷된 구조를 만들고 가상 컴퓨터가 위치한(네트워크) 서브넷을 지정할 수 있는 추가 컨테이너 역할을 하는 옵션 가상 네트워크입니다.

Azure 서비스 관리를 위한 구성 요소 및 해당 관계는 다음과 같습니다.

  ![클래식 아키텍처](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## 클래식에서 Resource Manager로 마이그레이션

클래식 배포에서 Resource Manager 배포로 마이그레이션할 준비가 되었다면 다음을 참조하세요.

1. [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## 다음 단계

- 가상 컴퓨터, 저장소 계정 및 가상 네트워크를 정의하는 템플릿 생성을 연습하려면 [Resource Manager 템플릿 연습](resource-manager-template-walkthrough.md)을 참조하세요.
- Resource Manager 템플릿의 구조에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
- 템플릿 배포에 대한 명령을 보려면 [Azure 리소스 관리자 템플릿으로 응용 프로그램 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0803_2016-->