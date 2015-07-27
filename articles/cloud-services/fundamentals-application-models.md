<properties 
	pageTitle="Azure에서 제공하는 계산 호스팅 옵션" 
	description="Azure 계산 호스팅 옵션 및 작동 방식에 대해 알아보세요. 가상 컴퓨터, 웹 사이트, 클라우드 서비스 등" 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Azure에서 제공하는 계산 호스팅 옵션

Azure는 응용 프로그램을 실행하기 위한 여러 호스팅 모델을 제공합니다. 각각은 다양한 서비스 세트를 제공하므로 정확히 무엇을 수행하고자 하는지에 따라 선택 항목이 달라집니다. 이 문서는 옵션들을 살펴보면서 각 기술을 설명하고 그 용도의 예제를 제공합니다

| 계산 옵션 | 대상 |
| ------------------ | --------   |
| [앱 서비스] | 모든 장치에 대한 확장 가능한 웹앱, 모바일 앱, API 앱 및 논리 앱 |
| [클라우드 서비스] | 운영 체제의 향상된 제어를 통한 고가용성의 확장 가능한 N 계층 클라우드 앱 |
| [가상 컴퓨터] | OS의 완벽한 제어와 더불어 사용자 지정된 Windows 및 Linux VM |

[AZURE.INCLUDE [콘텐츠](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [콘텐츠](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [콘텐츠](../../includes/virtual-machines-choose-me-content.md)]

## 기타 옵션

Azure에서는 좀 더 특수한 용도로 사용할 수 있는 다음과 같은 기타 계산 호스팅 모델도 제공합니다.

* [모바일 서비스](/services/mobile-services/) 모바일 장치에서 실행되는 앱용 클라우드 백 엔드를 제공하도록 최적화되어 있습니다.
* [일괄 처리](/services/batch/) 많은 양의 비슷한 작업(여러 컴퓨터에서 병렬 작업으로 실행되는 작업이 가장 적합함)을 처리하도록 최적화되어 있습니다.
* [HDInsight (Hadoop)](/services/hdinsight/)Hadoop 클러스터에서 [ MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) 작업을 실행하도록 최적화되어 있습니다. 

## 무엇을 사용해야 합니까? 모델 선택

세 가지 범용 Azure 계산 호스팅 모델은 모두 확장 가능하며 안정적인 응용 프로그램을 클라우드에서 빌드할 수 있는 기능을 제공합니다. 기본적으로 유사하다면 어느 모델을 사용해야 합니까?

대부분의 웹앱에서는 앱 서비스가 가장 적합합니다. Azure 웹 사이트에서는 배포와 관리 기능이 플랫폼에 통합되고, 높은 트래픽 부하를 처리하기 위해 사이트를 빠르게 확장할 수 있으며, 기본 제공 부하 분산 및 트래픽 관리자가 고가용성을 제공합니다. [온라인 마이그레이션 도구](https://www.migratetoazure.net/)를 사용하여 기존 사이트를 앱 서비스로 쉽게 이동하거나, 웹 응용 프로그램 갤러리에서 오픈 소스 앱을 사용하거나, 원하는 프레임워크와 도구를 사용하여 새 사이트를 만들 수 있습니다. [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) 기능을 사용하면 백그라운드 작업 처리 기능을 앱에 쉽게 추가할 수 있으며 웹앱이 아닌 계산 작업도 실행할 수 있습니다.

서버에 원격 접속하거나 서버 시작 작업을 구성하는 기능 등 웹 서버 환경에 대한 추가 제어 기능이 필요한 경우에는 대개 Azure 클라우드 서비스가 가장 적합한 옵션입니다.

Azure 웹 사이트 또는 Azure 클라우드 서비스에서 실행하려면 기존 응용 프로그램을 크게 수정해야 하는 경우에는 클라우드로의 마이그레이션을 간소화하기 위해 Azure 가상 컴퓨터를 선택할 수 있습니다. 그러나 VM을 올바르게 구성, 보호 및 유지 관리하려면 Azure 웹 사이트와 클라우드 서비스에 비해 훨씬 많은 시간과 IT 전문 지식이 필요합니다. Azure 가상 컴퓨터를 고려 중인 경우 VM 환경 패치/업데이트/관리에 필요한 지속적인 유지 관리 작업을 고려해야 합니다. 앱 서비스 또는 Azure 클라우드 서비스에서 실행하려면 기존 응용 프로그램을 크게 수정해야 하는 경우에는 클라우드로의 마이그레이션을 간소화하기 위해 Azure 가상 컴퓨터를 선택할 수 있습니다. 그러나 VM을 올바르게 구성, 보호 및 유지 관리하려면 앱 서비스와 클라우드 서비스에 비해 훨씬 많은 시간과 IT 전문 지식이 필요합니다. Azure 가상 컴퓨터를 고려 중인 경우 VM 환경 패치/업데이트/관리에 필요한 지속적인 유지 관리 작업을 고려해야 합니다.

적합한 옵션이 없는 수도 있습니다. 이런 경우 옵션을 조합하는 것이 가장 좋습니다. 예를 들어 클라우드 서비스 웹 역할의 관리 혜택을 원하는 위치에 응용 프로그램을 구축하고 있으나 호환성 및 성능 상의 이유로 가상 컴퓨터에서 호스트되는 표준 SQL Server를 사용해야 한다고 가정합니다.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[앱 서비스]: #tellmeas
[가상 컴퓨터]: #tellmevm
[클라우드 서비스]: #tellmecs

## 다음 단계

* Azure 앱 서비스, 클라우드 서비스 및 가상 컴퓨터 [비교](../choose-web-site-cloud-service-vm/)
* [앱 서비스](../app-service-web-overview.md)에 대해 자세히 알아보세요.
* [클라우드 서비스](services/cloud-services/)에 대해 자세히 알아보세요.
* [가상 컴퓨터](https://msdn.microsoft.com/library/azure/jj156143.aspx)에 대해 자세히 알아봅니다. 

<!---HONumber=July15_HO3-->