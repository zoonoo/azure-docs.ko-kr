---
title: Azure 가상 머신 확장 집합 개요 | Microsoft Docs
description: Azure 가상 머신 확장 집합에 대해 자세히 알아보세요.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64c4b309d214b91c759ff93ad659b9c69320bea7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201158"
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Azure에서 말하는 가상 머신 확장 집합이란?
가상 머신 확장 집합은 동일한 VM 집합을 배포하고 관리하는데 사용할 수 있는 Azure 계산 리소스입니다. 모든 VM은 동일하게 구성되었으며 확장 집합은 true 자동 크기 조정을 지원하도록 디자인되었고 VM의 사전 프로비저닝이 필요하지 않습니다. 따라서 큰 계산, 대용량 데이터 및 컨테이너화된 워크로드를 대상으로 하는 대규모 서비스를 손쉽게 만들 수 있습니다.

계산 리소스 크기를 조정해야 하는 응용 프로그램의 경우 크기 조정 작업은 장애 도메인 및 업데이트 도메인 간에 암시적으로 균형이 조정됩니다. 확장 집합에 대한 자세한 소개는 [Azure 블로그 공지](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)를 참조하세요.

확장 집합에 대한 자세한 내용은 다음 비디오를 보세요.

* [Mark Russinovich의 Azure 확장 집합 설명](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Guy Bowerman과 Virtual Machine Scale Sets](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>확장 집합 만들기 및 관리
**리소스 만들기**를 선택하고 검색 표시줄에 **확장**을 입력하여 [Azure Portal](https://portal.azure.com)에 확장 집합을 만들 수 있습니다. 결과에 **가상 머신 확장 집합**이 나열됩니다. 여기에서 필수 필드를 입력하여 확장 집합을 사용자 지정하고 배포할 수 있습니다. 또 포털에는 CPU 사용량에 따라 기본 자동 크기 조정 규칙을 설정하는 옵션도 있습니다. 확장 집합을 관리하기 위해 Azure Portal, [Azure PowerShell cmdlets](virtual-machine-scale-sets-windows-manage.md) 또는 Azure CLI 2.0을 사용할 수 있습니다.

확장 집합은 [가용성 영역](virtual-machine-scale-sets-use-availability-zones.md)에 배포할 수 있습니다.

개별 Azure Resource Manager VM처럼 JSON 템플릿 및 [REST API](https://msdn.microsoft.com/library/mt589023.aspx)를 사용하여 확장 집합을 정의하고 배포할 수 있습니다. 따라서 모든 표준 Azure Resource Manager 배포 방법을 사용할 수 있습니다. 템플릿에 대한 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)를 참조하세요.

가상 머신 확장 집합에 대한 예제 템플릿의 집합은 [Azure 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 찾을 수 있습니다. (제목에서 **vmss**로 템플릿을 찾습니다.)

빠른 시작 템플릿 예의 경우, 포털 배포 기능에 연결되는 각 템플릿 링크의 추가 정보에 있는 "Azure에 배포" 단추입니다. 확장 집합을 배포하려면 단추를 클릭하고 포털에 필요한 매개 변수를 채웁니다. 


## <a name="autoscale"></a>Autoscale
일관된 응용 프로그램 성능을 유지하기 위해 확장 집합에서 VM 인스턴스의 수를 자동으로 늘리거나 줄일 수 있습니다. 이 자동 크기 조정 기능은 관리 오버헤드를 감소시켜 시간에 따른 고객 요구 변경 내용으로 확장 집합을 모니터링 및 튜닝합니다. 성능 메트릭, 응용 프로그램 응답 또는 고정된 일정에 따라 규칙을 정의하고 확장 집합은 필요에 따라 자동 크기 조정합니다.

기본 자동 크기 조정 규칙의 경우 CPU 사용량 또는 디스크 I/O와 같은 호스트 기반 성능 메트릭을 사용할 수 있습니다. 이러한 호스트 기반 메트릭은 설치 및 구성할 추가 에이전트 또는 확장 없이 자동으로 사용할 수 있습니다. 호스트 기반 메트릭을 사용하는 자동 크기 조정 규칙은 다음 도구 중 하나로 만들 수 있습니다.

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md)

보다 세부적인 성능 메트릭을 사용하려면 확장 집합의 VM 인스턴스에 Azure 진단 확장을 설치 및 구성할 수 있습니다. Azure 진단 확장을 사용하여 각 VM 인스턴스 내에서 메모리 사용과 같은 추가 성능 메트릭을 수집할 수 있습니다. 이러한 성능 메트릭은 Azure 저장소 계정으로 스트리밍되고 이 데이터를 사용하도록 자동 크기 조정 규칙을 만듭니다. 자세한 내용은 [Linux VM](../virtual-machines/linux/diagnostic-extension.md) 또는 [Windows VM](../virtual-machines/windows/ps-extensions-diagnostics.md)에서 Azure 진단 확장을 활성화하는 방법에 대한 문서를 참조하세요.

응용 프로그램 성능 자체를 모니터링하기 위해 App Insights용 응용 프로그램에 작은 계측 패키지를 설치 및 구성할 수 있습니다. 그런 다음 응용 프로그램 응답 시간 또는 세션 수에 대한 자세한 성능 메트릭을 앱에서 다시 스트리밍할 수 있습니다. 그런 다음 응용 프로그램 수준 성능 자체에 대해 정의된 임계값을 사용하여 자동 크기 조정 규칙을 만들 수 있습니다. App Insights에 대한 자세한 내용은 [Application Insights란](../application-insights/app-insights-overview.md)을 참조하세요.


## <a name="manually-scaling-a-scale-set-out-and-in"></a>수동으로 확장 집합 확장 및 축소
**설정** 아래 **확장** 섹션을 클릭하여 Azure Portal에서 확장 집합의 용량을 수동으로 변경할 수 있습니다. 

명령줄에서 확장 집합 용량을 변경하려면 **Azure CLI**에서 [scale](https://github.com/Azure/azure-cli) 명령을 사용합니다. 예를 들어 이 명령을 사용하여 확장 집합을 10개의 VM 용량으로 설정합니다.

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

PowerShell을 사용하여 확장 집합에서 VM 수를 설정하려면 **Update-AzureRmVmss** 명령을 사용합니다.

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Azure Resource Manager 템플릿을 사용하여 확장 집합에서 가상 머신의 수를 늘리거나 줄이려면, **용량** 속성을 변경하고 템플릿을 다시 배포합니다. Azure 자동 크기 조정이 지원하지 않는 사용자 지정 크기 조정 이벤트를 정의해야 하는 경우 이러한 단순성을 통해 크기 집합과 Azure 자동 크기 조정을 쉽게 통합하거나 사용자 고유의 사용자 지정 크기 조정 계층을 쉽게 작성할 수 있습니다. 

용량을 변경하기 위해서 Azure Resource Manager 템플릿을 다시 배포하는 경우 업데이트된 용량을 가진 **SKU** 속성 패킷만 포함하는 훨씬 더 작은 템플릿을 정의할 수 있습니다. [예를 들면 다음과 같습니다](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing).


## <a name="monitoring-your-scale-set"></a>확장 집합 모니터링
[Azure Portal](https://portal.azure.com)에 크기 집합 목록과 해당 속성이 표시됩니다. 또한 이 포털은 관리 작업을 지원합니다. 크기 집합과 크기 집합 내 개별 VM에서 관리 작업을 수행할 수 있습니다. 또한 이 포털은 사용자 지정 가능한 리소스 사용량 그래프도 제공합니다. 

Azure 리소스의 기본 JSON 정의를 보거나 편집해야 하는 경우 [Azure 리소스 탐색기](https://resources.azure.com)를 사용할 수도 있습니다. 크기 집합은 Microsoft.Compute Azure 리소스 공급자에서 리소스입니다. 이 사이트에서 다음 링크를 확장하여 이를 확인할 수 있습니다.

**구독** > **사용자의 구독** > **resourceGroups** > **공급자** > **Microsoft.Compute** > **virtualMachineScaleSets** > **사용자의 크기 집합** 등

## <a name="scale-set-scenarios"></a>확장 집합 시나리오
이 섹션에서는 몇 가지 일반적인 확장 집합 시나리오를 나열합니다. 일부 높은 수준의 Azure 서비스(예: Batch, Service Fabric, Container Service)에서 이러한 시나리오를 사용합니다.

* **RDP 또는 SSH를 사용하여 확장 집합 인스턴스에 연결**: 확장 집합은 가상 네트워크 내부에 생성되며, 확장 집합의 개별 VM에는 기본적으로 공용 IP 주소가 할당되지 않습니다. 이 정책을 사용하면 계산 그리드에 있는 모든 노드에 별도의 공용 IP 주소를 할당하는 비용 및 관리 오버헤드가 지양됩니다. 확장 집합 VM에 직접 외부 연결이 필요한 경우, 새 VM에 공용 IP 주소를 자동으로 할당하도록 확장 집합을 구성할 수 있습니다. 아니면 사용자의 가상 네트워크에 있고 공용 IP 주소를 할당할 수 있는 다른 리소스(예: 부하 분산 장치 및 독립 실행형 가상 머신)에서 VM에 연결할 수 있습니다. 
* **NAT 규칙을 사용하여 VM에 연결**: 공용 IP 주소를 만들고, 부하 분산 장치에 할당하고, 인바운드 NAT 풀을 정의할 수 있습니다. 이러한 작업은 IP 주소의 포트를 크기 집합의 VM 포트에 매핑합니다. 예: 
  
  | 원본 | 원본 포트 | 대상 | 대상 포트 |
  | --- | --- | --- | --- |
  |  공용 IP |포트 50000 |vmss\_0 |포트 22 |
  |  공용 IP |포트 50001 |vmss\_1 |포트 22 |
  |  공용 IP |포트 50002 |vmss\_2 |포트 22 |
  
   [이 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)에서 NAT 규칙은 하나의 공용 IP 주소를 사용하여 크기 집합의 모든 VM에 SSH를 사용하기 위해 정의됩니다.
  
   [이 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)는 RDP 및 Windows에 동일하게 적용됩니다.
* **"jumpbox"를 사용하여 VM에 연결**: 동일한 가상 네트워크에 크기 집합과 독립 실행형 VM을 만들면, 독립 실행형 VM과 크기 집합 VM은 가상 네트워크 또는 Subnet에 정의된 대로 내부 IP 주소를 사용하여 서로 연결할 수 있습니다. 공용 IP 주소를 만들어서 독립 실행형 VM에 할당하면 RDP 또는 SSH를 사용하여 독립 실행형 VM에 연결할 수 있습니다. 그런 다음 해당 컴퓨터에서 크기 집합 인스턴스에 연결할 수 있습니다. 본질적으로 간단한 확장 집합이 기본 구성에 공용 IP 주소를 포함하는 간단한 독립 실행형 VM보다 더 안전하다는 것을 알 수 있습니다.
  
   예를 들어 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)은 독립 실행형 VM으로 간단한 확장 집합을 배포합니다. 
* **크기 집합 인스턴스에 부하 분산**: 라운드 로빈 방식을 사용하여 VM의 계산 클러스터에 작업을 전달하려면, 그에 맞게 Azure Load Balancer를 계층 4 부하 분산 규칙으로 구성합니다. 지정된 프로토콜, 간격, 요청 경로로 포트를 Ping하여 응용 프로그램이 실행되는지 확인하는 프로브를 정의할 수 있습니다. 또한 Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/)는 보다 복잡한 계층 7 부하 분산 시나리오와 함께 크기 집합을 지원합니다.
  
   [이 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl)에서는 Apache 웹 서버를 실행하는 확장 집합을 만들고 각 VM이 수신하는 부하를 분산하는 부하 분산 장치를 사용합니다. (Microsoft.Network/loadBalancers 리소스 종류를 살펴보고 virtualMachineScaleSet의 networkProfile 및 extensionProfile을 살펴보세요.)

   [이 Linux 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway)와 [이 Windows 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway)는 Application Gateway를 사용합니다.  

* **크기 집합을 PaaS 클러스터 관리자에서 계산 클러스터로 배포**: 크기 집합을 차세대 작업자 역할이라고 설명하기도 합니다. 이 설명이 타당하기는 하지만 확장 집합 기능을 Azure Cloud Services 기능과 혼동할 위험이 있습니다. 어떤 의미에서 크기 집합은 진정한 작업자 역할 또는 작업자 리소스를 제공합니다. 이들은 플랫폼/런타임이 독립적이고, 사용자 지정이 가능하며 Azure Resource Manager IaaS에 통합되는 일반화된 계산 리소스입니다.
  
   Cloud Services 작업자 역할은 플랫폼/런타임 지원 면에서 제한됩니다(Windows 플랫폼 이미지에만 해당). 하지만 VIP 교환, 구성 가능한 업그레이드 설정 및 런타임/앱 배포별 설정 등의 서비스는 포함됩니다. 이러한 서비스는 크기 집합에서 *아직* 사용할 수 없지만 Azure Service Fabric 등 다른 상위 수준의 PaaS 서비스에서 제공됩니다. 확장 집합을 PaaS를 지원하는 인프라로 주목할 수 있습니다. [Service Fabric](https://azure.microsoft.com/services/service-fabric/)과 같은 PaaS 솔루션이 이 인프라에 구축됩니다.
  
   이 방식의 [이 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)에서 [Azure Container Service](https://azure.microsoft.com/services/container-service/)는 크기 조정 설정에 따라 컨테이너 조정자로 클러스터를 배포합니다.

## <a name="scale-set-performance-and-scale-guidance"></a>확장 집합 성능 및 크기 조정 지침
* 확장 집합은 최대 1,000대의 VM을 지원합니다. 사용자 고유의 사용자 지정 VM 이미지를 만들고 업로드하는 경우 300대로 제한됩니다. 대규모 확장 집합 사용 시 고려 사항은 [대규모 가상 머신 확장 집합으로 작업](virtual-machine-scale-sets-placement-groups.md)을 참조하세요.
* 확장 집합을 사용할 Azure Storage 계정의 집합을 미리 만들 필요가 없습니다. 확장 집합은 저장소 계정당 디스크 수와 관련된 성능 문제를 무효화하는 Azure Managed Disks를 지원합니다. 자세한 내용은 [Azure 가상 머신 크기 집합 및 관리되는 디스크](virtual-machine-scale-sets-managed-disks.md)를 참조하세요.
* 보다 빠르고 예측 가능한 VM 프로비전 시간 및 향상된 I/O 성능을 위해 Azure Storage 대신 Azure Premium Storage를 사용하는 것이 좋습니다.
* 배포하려는 지역의 vCPU 할당량에 따라 만들 수 있는 VM 개수가 제한됩니다. 현재 Azure Cloud Services에 사용할 vCPU 한도가 높더라도, 계산 할당량 한도를 높이려면 고객 지원팀에 문의해야 합니다. 할당량을 쿼리하려면 이 Azure CLI 명령 `az vm list-usage`을 실행합니다. 또는 이 PowerShell 명령 `Get-AzureRmVMUsage`를 실행합니다.

## <a name="frequently-asked-questions-for-scale-sets"></a>크기 집합에 대한 질문과 대답
**Q.** 크기 집합에 포함할 수 있는 VM 수는 몇 개인가요?

**A.** 확장 집합에는 플랫폼 이미지 기준으로 0~1,000대의 VM, 사용자 지정 이미지 기준으로 0~300대의 VM을 포함시킬 수 있습니다. 

**Q.** 크기 집합 내에서 데이터 디스크가 지원되나요?

**A.** 예. 크기 집합은 집합에서 모든 VM에 적용되는 연결된 데이터 디스크 구성을 정의할 수 있습니다. 자세한 내용은 [}Azure 확장 집합 및 연결된 데이터 디스크](virtual-machine-scale-sets-attached-disks.md)를 참조하세요. 데이터를 저장하는 기타 옵션은 다음과 같습니다.

* Azure 파일(SMB 공유 드라이브)
* OS 드라이브
* 임시 드라이브(Azure Storage에서 지원되지 않는 로컬 드라이브)
* Azure 데이터 서비스(예: Azure 테이블, Azure Blob)
* 외부 데이터 서비스(예: 원격 데이터베이스)

**Q.** 크기 집합을 지원하는 Azure 지역은 어디인가요?

**A.** 모든 지역에서 크기 집합을 지원합니다.

**Q.** 사용자 지정 이미지를 사용하여 크기 집합을 어떻게 만드나요?

**A.** 사용자 지정 이미지 VHD를 기반으로 Managed Disk를 만들고 크기 집합 템플릿에서 참조합니다. [예를 들면 다음과 같습니다](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**Q.** 내 크기 집합 용량을 20에서 15로 줄이면, 어떤 VM이 제거되나요?

**A.** 가용성을 최대화하기 위해 업데이트 도메인과 장애 도메인이 균등하도록 가상 머신이 확장 집합에서 제거됩니다. ID가 가장 높은 VM이 먼저 제거됩니다.

**Q.** 그런 다음 용량을 15에서 18로 늘리면 어떻게 되나요?

**A.** 용량을 18로 늘리면 3개의 새로운 VM 생성됩니다. VM 인스턴스 ID는 이전의 가장 큰 값에서 증가합니다(예: 20, 21, 22). VM은 장애 도메인과 업데이트 도메인에 균등하게 분배됩니다.

**Q.** 크기 집합에서 여러 확장을 사용하는 경우, 실행 순서를 강제로 적용할 수 있나요?

**A.** 직접적으로는 불가능하지만 customScript 확장의 경우 다른 확장이 완료될 때까지 사용자의 스크립트를 대기시킬 수 있습니다. 확장 시퀀싱에 대한 추가 지침은 블로그 게시물 [Azure 가상 머신 확장 집합의 확장 시퀀싱](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/)에서 확인할 수 있습니다.

**Q.** 크기 집합은 Azure 가용성 집합과 작업이 가능한가요?

**A.** 예. 확장 집합은 5개의 장애 도메인과 5개의 업데이트 도메인이 있는 암시적인 가용성 집합입니다. 100대 이상인 VM의 확장 집합은 여러 가용성 집합에 해당하는 여러 *배치 그룹*으로 확장됩니다. 배치 그룹에 대한 자세한 내용은 [대규모 가상 머신 크기 집합과 작동](virtual-machine-scale-sets-placement-groups.md)을 참조하세요. VM의 가용성 집합은 동일한 가상 네트워크에 VM의 확장 집합으로 존재할 수 있습니다. 일반적인 구성은 가용성 집합에서 고유한 구성이 필요한 제어 노드 VM을 배치하고 확장 집합에 데이터 노드를 배치하는 것입니다.

확장 집합에 대한 질문의 자세한 답변은 [Azure 가상 머신 확장 집합 FAQ](virtual-machine-scale-sets-faq.md)에서 확인할 수 있습니다.
