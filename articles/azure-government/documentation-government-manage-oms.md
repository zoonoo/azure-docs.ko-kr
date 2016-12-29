---
title: "Azure Government OMS | Microsoft 문서"
description: "이 문서에서는 미국 정부 기관 및 솔루션 공급자에 적용할 수 있는 OMS 시나리오에 대해 설명합니다."
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: ccbcf15eaf9b13625338ea1d66554eae7dc7d2eb
ms.openlocfilehash: 0d010bb21d807239da37172bc2885ddadc5c8028

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Azure Government 사이버 보안: OMS(Operations Management Suite)로 자산 모니터링 및 보안 유지

## <a name="cybersecurity-in-the-cloud"></a>클라우드의 사이버 보안
클라우드로 전환하는 고객의 중요한 측면은 클라우드에 배포한 Azure Government 서비스의 자산을 관리하고 보안을 유지하는 것입니다. Virtual Machines 방화벽을 올바르게 구성해야 합니다. Virtual Networks에는 올바른 네트워크 보안 그룹이 적용되어 있어야 합니다. 자산에 대한 액세스는 적시에 적절한 사람들로 제한해야 합니다. 이러한 모든 것은 기관에서 보안 인프라를 사용할 수 있도록 계획, 설계 및 제공해야 하는 필수적인 작업 흐름입니다.

이러한 환경을 설정하는 것이 어려운 과제일 수 있습니다. 서버의 플릿을 모니터링 서비스에 탑재하면 확장 및 업데이트하기가 어렵습니다. 다양한 클라우드 공급자와 클라우드 및 온-프레미스에 있는 인프라를 모니터링하는 것도 어렵습니다. 마지막으로 모니터링을 최신 상태로 유지하고 사이버 보안 위협에 대응하는 모니터링, 탐지, 경고 및 조치를 통찰력 있게 수행하려면 시간, 리소스 및 계산 성능이 필요합니다.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft OMS(Operations Management Suite)
현재 Azure Government에서 제공하는 Microsoft OMS는 Map Reduce 및 기계 학습 기능을 서비스로 활용하여 이러한 모든 작업을 수행할 수 있게 해주는 서비스입니다. OMS는 다음 작업을 수행할 수 있습니다.
* Azure, 다른 클라우드 공급자 및/또는 온-프레미스에서 개별 VMS(Linux 및 Windows)에 에이전트를 배포합니다.
* 기존 로깅 데이터가 있는 Azure Government 저장소 계정 또는 SCOM 끝점을 통해 기존 로그를 연결합니다.
* 환경에서 위협 요소가 노출되지 않도록 초대형 로그 검색에서 제공하는 에버그린 기계 학습 및 Map Reduce 서비스를 실행합니다.

OMS를 플릿에 통합할 수 있는 방법과 위에서 언급한 과제를 즉시 해결할 수 있는 독창적인 솔루션을 살펴보겠습니다.

## <a name="onboarding-servers-to-oms"></a>OMS에 서버 탑재
클라우드 자산과 OMS를 통합하는 첫 번째 작업은 로그 원본에 OMS 에이전트를 설치하는 것입니다.
Virtual Machines에서는 이러한 에이전트 설치가 매우 간단합니다. 즉 OMS 작업 영역에서 에이전트를 수동으로 다운로드하면 됩니다.

![그림 1: OMS에 연결된 Windows Server](./media/documentation-government-oms-figure1.png)
<p align="center">그림 1: OMS에 연결된 Windows Server</p>

Azure VM을 포털을 통해 OMS에 직접 연결할 수 있습니다. 지침에 대해서는 [여기](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/)를 참조하세요.

또한 프로그래밍 방식으로 연결하거나 Azure Resource Manager 템플릿에 OMS 확장을 직접 구성할 수도 있습니다. Windows 기반 컴퓨터에 대한 지침은 [여기](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents)를 참조하고, Linux에 대한 지침은 [여기](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents)를 참조하세요.

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>OMS에 저장소 계정 및 SCOM 탑재
OMS는 저장소 계정 및/또는 기존 SCOM 2013 배포에 연결하여 하이브리드 시나리오에서(클라우드 공급자 또는 클라우드/온-프레미스 인프라 전반에서) 운영 관리를 제공할 수 있습니다.

![그림 2: OMS에 Azure Storage 및 SCOM 연결](./media/documentation-government-oms-figure2.png)
<p align="center">그림 2: OMS에 Azure Storage 및 SCOM 연결</p>

OMS는 Chef 및/또는 Puppet과 같은 다른 모니터링 서비스의 정보 로깅도 지원합니다. 또한 Azure 배포의 경우 OMS가 활성화된 Azure Resource Manager 템플릿을 포함한 VM도 사용하므로 Compute와 Onboard를 OMS 작업 영역에 동시에 배포할 수 있습니다. 

![그림 3: OMS 확장이 있는 Azure VM용 Azure Resource Manager 템플릿](./media/documentation-government-oms-figure3a.png)
![그림 3: OMS 확장이 있는 Azure VM용 Azure Resource Manager 템플릿](./media/documentation-government-oms-figure3b.png)
<p align="center">그림 3: OMS 확장이 있는 Azure VM용 Azure Resource Manager 템플릿</p>

온-프레미스에 있는 기존 SCOM 구현을 사용하여 OMS를 설정하는 방법에 대한 정보는 [여기](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents)에 있습니다.

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>OMS 솔루션 팩을 통해 인텔리전스 활용
이제 로깅 데이터의 다양한 원본이 있으므로 별도의 문제가 발생합니다. 즉 이 모든 데이터를 이해해야 합니다.
OMS는 '핵심' 요소로서 수천 또는 수백만 개의 로그를 매우 신속하게 검색할 수 있는 강력한 쿼리를 작성할 수 있는 로그 검색 서비스입니다. 그러나 쿼리를 작성하는 데 필요한 문제를 검색하는 것은 매우 어렵습니다.

OMS 솔루션 시작 - OMS Map Reduce 및 기계 학습 기술이 기본적으로 통합된 쿼리 팩으로 OMS 관리 플릿에 대한 지식을 사전에 제공합니다.

사이버 보안 - OMS에서 사용자를 위해 즉시 해결할 수 있는 세 가지 사이버 보안 시나리오에 대해 간략히 살펴보겠습니다.

###<a name="antimalware-assessment"></a>맬웨어 방지 평가
맬웨어 방지 평가는 쿼리, 알림 및 모니터링 대시보드에 대해 미리 준비된 집합을 제공하여 플릿이 맬웨어로부터 얼마나 잘 보호받고 있는지를 즉시 파악할 수 있도록 합니다.

이 대시보드는 다음 네 가지를 제공합니다.
* 활성 및/또는 수정된 위협이 있는 서버
* 현재 검색된 위협
* 충분히 보호되지 않는 컴퓨터 - OMS는 컴퓨터의 로그를 크롤링하여 이 작업을 수행하고, 열려 있는 FW 사이트를 확인하거나 일반적인 웹 브라우저에서 규칙을 잘못 구성하도록 합니다.
* 보호되는 서버를 보호하는 방법 분석. 예를 들어 네이티브 Windows OS 바이러스 방지 및/또는 System Center Endpoint Protection과 같은 것이 있습니다.

예를 들어 아래와 같이 위협이 발견되어 Systems Center에서 이러한 위협을 자동으로 우선 순위에 따라 분류했음을 확인할 수 있습니다.

![그림 4: OMS 맬웨어 방지 평가 솔루션](./media/documentation-government-oms-figure4.png)
<p align="center">그림 4: OMS 맬웨어 방지 평가 솔루션</p>

맬웨어 방지 평가에 대한 자세한 내용은 [https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)에서 찾을 수 있습니다.

### <a name="identity-and-access"></a>ID 및 액세스
클라우드의 또 다른 일반적인 사이버 보안 시나리오는 손상된 자격 증명입니다. 클라우드 구독에는 자격 증명이 있을 뿐만 아니라 개별 VM마다 연결된 사용자 및/또는 비밀(대개 인증서 및/또는 암호)이 있습니다.

OMS는 플릿의 모든 로그인 시도를 합계하고 구성하며, 유형(원격, 로컬, 사용된 사용자 이름 등)에 따라 이러한 시도를 버킷에 넣습니다. 예를 들어 아래와 같이 대다수 임의의 문자열에서 사용자 이름으로 실패한 대량의 로그인 시도를 확인할 수 있습니다. 이는 컴퓨터가 노출되고 방화벽 및 액세스 제어 목록으로 제대로 보호되지 않을 가능성이 높습니다.

![그림 5: 지난 24시간 동안 실패한 로그온: 97.3%](./media/documentation-government-oms-figure5.png)
<p align="center">그림 5: 지난 24시간 동안 실패한 로그온: 97.3%</p>

### <a name="threat-intelligence"></a>위협 인텔리전스
OMS는 악의적인 내부자 시나리오, 즉 조직 내부에서 보안이 손상되어 악의적인 사용자가 데이터를 사전 필터링하려고 시도하는 경우로부터 보호합니다.

OMS 위협 인텔리전스는 컴퓨터의 모든 네트워크 로그를 검토하고 알려진 악의적 IP(예: 인덱싱되지 않은 다크넷의 IP 주소)에 대한 인바운드/아웃바운드 네트워크 연결을 자동으로 검색하고 알려줍니다.
예를 들어 아래와 같이 중국에 대한 인바운드 및 아웃바운드 네트워크 연결이 있음을 확인할 수 있습니다. 

인바운드 태그를 두 번 클릭 - OMS에서 관리하는 Linux VM이 중국의 알려진 다크넷 IP에 아웃바운드 연결을 하고 있음을 알 수 있습니다.

위협 인텔리전스와 같은 OMS 솔루션에 경고를 설정할 수도 있습니다. 아래와 같이 OMS는 알려진 악성 IP에 대해 10개가 넘는 아웃바운드 연결을 감지하여 전자 메일을 통해 경고를 보내도록 경고를 설정했습니다. 그런 다음 Azure Automation 작업을 시작하기 위해 해당 경고를 구성하여 해당 VM을 자동으로 종료합니다.

![그림 6: OMS 경고 및 자동화](./media/documentation-government-oms-figure6.png)
<p align="center">그림 6: OMS 경고 및 자동화</p>

이는 Azure, 다른 클라우드 서비스 공급자 또는 온-프레미스에서 실행 중인지 여부에 관계없이 플릿에 즉시 적용할 수 있는 독창적인 OMS 솔루션의 한 예입니다.
OMS는 최신 위협에 대해 기계 학습을 자동으로 업데이트하고 OMS 솔루션 갤러리에도 새로운 솔루션을 지속적으로 롤아웃할 것입니다.

OMS에 대한 자세한 내용은 [https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/)에 있는 문서 페이지를 참조하세요. 



<!--HONumber=Nov16_HO4-->


