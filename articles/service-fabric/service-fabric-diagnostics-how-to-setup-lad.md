---
title: Linux Azure 진단을 사용하여 로그 수집 | Microsoft Docs
description: 이 문서는 Azure에서 실행 중인 Service Fabric Linux 클러스터에서 로그를 수집하도록 Azure 진단을 설정하는 방법을 설명합니다.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: subramar

---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Azure 진단을 사용하여 로그 수집
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Azure 서비스 패브릭 클러스터를 실행할 때 모든 노드의 로그를 중앙 위치에 수집하는 것이 좋습니다. 중앙 위치에 로그를 두면 문제가 서비스에 있든, 응용 프로그램에 있든, 클러스터 자체에 있든 상관없이 손쉽게 분석하고 해결할 수 있습니다. 로그를 업로드 및 수집하는 방법 중 하나는 로그를 Azure 저장소에 업로드하는 Azure 진단 확장을 사용하는 것입니다. 저장소의 이벤트를 읽고 [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 또는 다른 로그 구문 분석 솔루션과 같은 제품에 배치할 수 있습니다.

## <a name="log-sources-that-you-might-want-to-collect"></a>수집하려는 로그 원본
* **Service Fabric 로그:** [LTTng](http://lttng.org)를 통해 플랫폼에서 내보내고 저장소 계정에 업로드됩니다. 로그는 플랫폼에서 내보내는 작업 이벤트 또는 런타임 이벤트일 수 있습니다. 이러한 로그는 클러스터 매니페스트에서 지정하는 위치에 저장됩니다. (저장소 계정 세부 정보를 가져오려면 **AzureTableWinFabETWQueryable** 태그를 찾아서 **StoreConnectionString**을 검색합니다.)
* **응용 프로그램 이벤트:** 서비스 코드에서 내보낸 이벤트입니다. 텍스트 기반 로그 파일을 작성하는 모든 로깅 솔루션을 사용할 수 있습니다(예: LTTng). 자세한 내용은 응용 프로그램 추적에 대한 LTTng 설명서를 참조하세요.  

## <a name="deploy-the-diagnostics-extension"></a>진단 확장 배포
로그를 수집하는 첫 단계는 서비스 패브릭 클러스터의 각 VM에 진단 확장을 배포하는 것입니다. 진단 확장은 각 VM에서 로그를 수집하여 사용자가 지정하는 저장소 계정에 업로드합니다. 단계는 Azure Portal 또는 Azure Resource Manager 사용 여부에 따라 달라집니다.

클러스터 만들기의 일환으로 클러스터 내의 VM에 진단 확장을 배포하려면 **진단**을 **켜기**로 설정합니다. 클러스터를 만든 후에는 포털을 사용하여 이 설정을 변경할 수 없습니다.

그런 다음 파일을 수집하여 저장소 계정에 배치하도록 LAD(Linux Azure 진단)를 구성합니다. 이 과정은 [LAD를 사용하여 Linux VM 모니터링 및 진단](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md) 문서의 시나리오 3("고유한 로그 파일 업로드")에 설명되어 있습니다. 이 과정을 따라 하면 추적에 액세스할 수 있습니다. 추적을 원하는 시각화 도우미에 업로드할 수 있습니다.

Azure Resource Manager를 사용하여 진단 확장을 배포할 수도 있습니다. Windows 및 Linux의 프로세스는 유사하며 Windows 클러스터는 [Azure 진단을 사용하여 로그를 수집하는 방법](service-fabric-diagnostics-how-to-setup-wad.md)에 설명되어 있습니다.

또한 [Linux와 함께 사용하는 Operations Management Suite Log Analytics](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/)에 설명된 대로 Operations Management Suite를 사용할 수 있습니다.

이 구성을 완료하면 LAD 에이전트는 지정된 로그 파일을 모니터링합니다. 새 줄이 파일에 추가될 때마다 이 에이전트는 사용자가 지정한 저장소로 전송되는 syslog 항목을 만듭니다.

## <a name="next-steps"></a>다음 단계
문제를 해결하는 동안 살펴봐야 하는 이벤트에 대해 자세히 알아보려면 [LTTng 설명서](http://lttng.org/docs) 및 [LAD 사용](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md)을 참조하세요.

<!--HONumber=Oct16_HO2-->


