<properties
   pageTitle="리소스 관리자 및 서비스 관리(클래식) 배포 모드 | Azure"
   description="리소스 관리자 및 클래식 배포 모델 간 차이점 이해"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"/>

# Azure 배포 모델

Azure 플랫폼은 전환 중입니다. Azure를 처음 접하든, 수년 간 사용해왔든 상관 없이 이 전환 중에 플랫폼에 적용하는 일부 주요 변경 사항을 이해하는 것이 중요합니다.

모든 Azure 리소스는 다음과 같은 배포 모델 중 하나 또는 모두를 지원합니다.

- **리소스 관리자:** Azure 리소스에 대한 최신 배포 모델입니다. 대부분의 최신 리소스는 이미 이 배포 모델을 지원하고 결국 모든 리소스도 지원할 것입니다.   
 
- **클래식:** 이 모델은 현재 대부분의 기존 Azure 리소스에 의해 지원됩니다. Azure에 추가하는 새 리소스는 이 모델을 지원하지 않습니다.

각 Azure 리소스에 대한 설명서에서 어떤 서비스 모델로 생성할 수 있는지 자세히 설명합니다.

## 이것이 왜 문제가 될까요? 

다음과 같은 이유로 중요합니다.

- 사용하는 Azure 플랫폼 기능은 이들 두 모델 간에 서로 다릅니다. 예를 들어, 리소스 관리자 배포 모델(또는 리소스 관리자만)을 사용하여 생성되는 리소스는 [Azure 리소스 관리자 템플릿](resource-group-overview.md/#template-deployment)으로 만들 수 있는 반면에 클래식 배포 모델로 생성할 수 있는 리소스는 만들 수 없습니다.
- 개별 Azure 리소스 기능 또는 동작은 두 모델에 걸쳐 다를 수 있으며, 아니면 하나의 모델 또는 다른 모델에서만 존재합니다. 예를 들어, 클래식 배포 모델로 생성된 가상 컴퓨터 간의 트래픽 부하 분산은 가상 컴퓨터들이 Azure 클라우드 서비스의 멤버이며 부하가 클라우드 서비스 내에서 가상 컴퓨터에 걸쳐 자동으로 분산되므로 *암시적*입니다. 리소스 관리자를 사용하여 생성된 가상 컴퓨터는 클라우드 서비스의 멤버가 아니며 별도의 Azure 부하 분산 리소스를 *명시적으로* 생성해서 여러 가상 컴퓨터 간에 트래픽의 부하를 분산해야 합니다.  
- Azure 리소스의 생성, 구성 및 관리는 이 두 모델 간에 차이가 있습니다.
- 하나의 배포 모델을 사용하여 생성된 리소스와 다른 배포 모델을 사용하여 생성된 리소스가 반드시 상호 운영 가능한 것은 아닙니다. 예를 들어, 하나의 배포 모델을 사용하여 만든 Azure 가상 컴퓨터는 동일한 배포 모델을 사용하여 만든 Azure 가상 네트워크에만 연결할 수 있습니다.    

각 배포 모델의 기반이 되는 것은 각 리소스에 대한 API(응용 프로그래밍 인터페이스)입니다. 리소스 관리자 배포 모델에 대해서는 [리소스 관리자 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)가, 클래식 배포 모델에 대해서는 [서비스 관리 API](https://msdn.microsoft.com/library/azure/ee460799.aspx)가 있습니다. 개발자는 이러한 API와 *직접* 상호 작용하도록 코드를 작성할 수 있습니다.

그러나 IT 전문가들은 일반적으로 웹 브라우저에서 그래픽 포털을 사용하고, Windows 컴퓨터에서 Azure PowerShell cmdlet을 사용하거나 Windows, OS X 또는 Linux 컴퓨터에서 Azure CLI(명령줄 인터페이스)를 사용하여 이러한 API와 *간접적으로* 상호 작용할 수 있습니다. IT 전문가가 사용하는 이 모든 세 가지 간접 메서드는 API와 직접적으로 상호 작용합니다. 즉, 새로운 기능이 Azure 플랫폼 또는 리소스에 도입되면 항상 API를 통해 먼저 직접적으로 사용 가능하며, 간접적인 메서드는 API가 사용 가능하게 된 후에 새로운 리소스와 기능에 대한 지원을 받게 됩니다.

아래 섹션에서는 Azure 리소스가 어떻게 3개의 간접 메서드를 통해 다양한 배포 모델을 사용하여 구성되는지를 설명합니다.

## 포털
Azure에는 다음 두 포털이 있습니다.

- **[Azure 포털](https://manage.windowsazure.com):** Azure를 어느 정도 사용했다면 이 포털을 사용한 것입니다. 클래식 배포 모델을 지원하는 기존의 Azure 리소스를 생성 및 구성하는 데 사용됩니다. 이것을 사용하여 리소스 관리자만 지원하는 리소스를 생성 또는 구성할 수 없습니다. 
- **[Azure 미리보기 포털](https://azure.microsoft.com/overview/preview-portal/):** 최신 Azure 리소스를 사용하는 경우 이 포털을 사용해봤을 것입니다. 일부 Azure 리소스를 생성 및 구성하는 데 사용할 수 있습니다. 결국 모든 Azure 리소스를 생성 및 구성하는 데 사용할 수 있을 것입니다. 두 배포 모델을 모두 지원하는 일부 리소스의 경우, 이 포털에서 두 배포 모델 중 하나를 사용하여 리소스를 생성 및 구성할 수 있습니다. 

일부 리소스 및 기능은 하나의 포털 또는 다른 포털에서만 생성 및 구성할 수 있습니다. 일부 리소스 또는 기능은 두 포털 모두에서는 생성 또는 구성할 수 없으며, PowerShell, CLI 또는 둘 모두로 구성할 수 있습니다. 각 Azure 리소스에 대한 설명서에서 어떤 메서드로 생성할 수 있는지 자세히 설명합니다.

## PowerShell
[PowerShell](powershell-install-configure.md)에서 명령줄이나 작성 스크립트를 사용하여 Windows 컴퓨터에서 Azure 리소스를 생성 및 구성할 수 있습니다. 개별 Azure 리소스는 [리소스 관리자 cmdlet](https://msdn.microsoft.com/library/azure/mt125356.aspx), [서비스 관리 cmdlet](https://msdn.microsoft.com/library/azure/dn708504.aspx) 또는 두 가지 모두를 가질 수 있습니다. 일부 리소스 및 기능은 PowerShell 또는 CLI를 사용해서만 생성 및/또는 구성할 수 있습니다. 리소스에 따라 리소스 관리자 PowerShell cmdlet 사용 시 Azure 리소스의 생성 및 구성에 두 가지 옵션을 가질 수 있습니다.

- **PowerShell cmdlet만:** 각 리소스에 대해 cmdlet를 사용하여 각 Azure 리소스를 개별적으로 생성 및 구성할 수 있습니다. 이 작업은 명령줄에서 또는 저장할 수 있는 PowerShell 스크립트 및 버전으로 여러 명령을 포함하여 수행할 수 있습니다.

- **Azure 리소스 관리자 템플릿을 사용한 PowerShell cmdlet:** PowerShell에서 Azure 리소스 관리자 템플릿을 사용하여 Azure 리소스를 생성할 수 있습니다. 템플릿을 저장하고 버전을 지정할 수 있습니다. [Azure 리소스 관리자 템플릿으로 응용 프로그램 배포](resource-group-template-deploy.md) 문서를 참조하여 더 알아볼 수 있습니다. 다운로드하고 수정할 수 있는 일반 솔루션에 대해 몇 가지 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)도 지원됩니다.

## CLI
CLI를 사용하여 Windows, OS X 또는 Linux 컴퓨터에서 Azure 리소스를 생성 및 구성할 수 있습니다. [Azure CLI 설치](xplat-cli-install.md) 문서를 참조하여 선택한 운영 체제에서 CLI를 설치합니다. PowerShell처럼 [리소스 관리자](xplat-cli-azure-resource-manager.md) 또는 [클래식(서비스 관리)](virtual-machines-command-line-tools.md) 배포 모델을 사용하여 리소스를 생성하는지 여부에 따라 사용해야 하는 명령이 서로 다릅니다.

## 다음 단계

- [리소스 관리자](/resource-group-overview.md)에 대해 알아봅니다.
- [템플릿 설계](/best-practices-resource-manager-design-templates.md) 방법을 알아봅니다.
- [모범 사례](/best-practices-resource-manager-examples.md) 사용

<!---HONumber=AcomDC_0128_2016-->