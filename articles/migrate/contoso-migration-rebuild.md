---
title: Azure에 Contoso 온-프레미스 앱 다시 빌드 | Microsoft Docs
description: Contoso가 Azure App Services, Kubernetes 서비스, CosmosDB, Azure Functions 및 Cognitive Services를 사용하여 Azure에 앱을 다시 빌드하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 74c33d73f15c4edf63a02ea5c9a0cdcad88bb68c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049748"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso 마이그레이션: Azure에 온-프레미스 앱 다시 빌드

이 문서에서는 Contoso가 Azure에서 SmartHotel360 앱을 마이그레이션하고 다시 빌드하는 방법을 설명합니다. Contoso는 앱의 프런트 엔드 VM을 Azure App Services 웹앱으로 마이그레이션합니다. 앱 백 엔드는 AKS(Azure Kubernetes Service)에서 관리하는 컨테이너에 배포된 마이크로 서비스를 사용하여 빌드됩니다. 사이트는 Azure Functions와 상호 작용하여 애완 동물 사진 기능을 제공합니다. 

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여주는 문서 시리즈 중 하나입니다. 이 문서 시리즈에는 배경 정보와 마이그레이션 인프라 설정, 마이그레이션을 위한 온-프레미스 리소스 평가, 다양한 유형의 마이그레이션 실행을 설명하는 시나리오가 담겨 있습니다. 시나리오가 복잡해지고 있으며, 앞으로 계속해서 문서가 추가될 것입니다.


**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법에 대해 설명합니다. 모든 마이그레이션 문서에 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel360 앱 평가를 실행하는 방법을 보여 줍니다. Contoso가 [Azure Migrate](migrate-overview.md) 서비스로 앱 VM을 평가하고 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)로 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: 앱을 Azure VM 및 SQL Managed Instance에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 SmartHotel360 앱에 대해 Azure로의 리프트 앤 시프트 마이그레이션을 실행하는 방법을 보여 줍니다. Contoso는 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. | 사용 가능
[문서 5: Azure VM에서 앱 다시 호스트](contoso-migration-rehost-vm.md) | Contoso가 Site Recovery만 사용하여 SmartHotel360 앱 VM을 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 6: 앱을 Azure VM 및 SQL Server Always On 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 SmartHotel360 앱을 마이그레이션하는 방법을 보여 줍니다. Contoso가 Site Recovery를 사용하여 앱 VM을 마이그레이션하고 Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호하는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL 서버에 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고 MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 9: Azure Web Apps 및 Azure SQL Database에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso가 SmartHotel360 앱을 Azure 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션하는 방법 시연 | 사용 가능
[문서 10: Azure Web Apps 및 Azure MySQL에 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso가 지속적인 업데이트를 위해 GitHub와 통합된, 여러 사이트의 Azure Web Apps로 Linux osTicket 앱을 마이그레이션하는 방법을 보여줍니다. Azure MySQL 인스턴스로 앱 데이터베이스를 마이그레이션합니다. | 사용 가능
[문서 11: Azure DevOps Services에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso가 온-프레미스 TFS(Team Foundation Server) 배포를 Azure의 Azure DevOps Services로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 12: Azure 컨테이너 및 SQL Database에서 앱 아키텍처 변경](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 앱 웹 계층의 아키텍처를 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 변경합니다. | 사용 가능
문서 13: Azure에 앱 다시 빌드 | Contoso가 App Services, Azure Kubernetes, Azure Functions, Cognitive services 및 Cosmos DB를 포함한 다양한 Azure 기능 및 서비스를 사용하여 자체 SmartHotel 앱을 다시 빌드하는 방법을 보여줍니다. | 이 문서의 내용
[문서 14: Azure로의 마이그레이션 확장](contoso-migration-scale.md) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. | 사용 가능

이 문서에서 Contoso는 2계층 Windows를 마이그레이션합니다. VMware VM에서 실행되는 NET SmartHotel360 앱을 Azure로 마이그레이션합니다. 이 앱을 사용하려는 경우 오픈 소스로 제공되므로 [GitHub](https://github.com/Microsoft/SmartHotel360-Backend)에서 다운로드할 수 있습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 이러한 마이그레이션으로 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 해결**: Contoso는 성장하고 있으며 Contoso 웹 사이트에서 고객에게 차별화된 환경을 제공하고자 합니다.
- **민첩성**: Contoso가 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다. 
- **크기 조정**: 비즈니스가 성공적으로 성장하면 Contoso IT 팀은 그와 동일한 속도로 확장 가능한 시스템을 제공해야 합니다.
- **비용**: Contoso는 라이선스 비용을 최소화하려고 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션에 대한 앱 요구 사항을 확정했습니다. 이러한 요구 사항은 최상의 마이그레이션 방법을 결정하는 데 사용되었습니다.
 - Azure의 앱은 미래에도 지금처럼 중요한 역할을 할 것입니다. 잘 수행되고 간편하게 확장할 수 있어야 합니다.
 - 앱은 IaaS 구성 요소를 사용하지 않아야 합니다. 모든 것은 PaaS 또는 무서버 서비스를 사용하도록 빌드되어야 합니다.
 - 앱 빌드는 클라우드 서비스에서 실행되어야 하고, 컨테이너는 클라우드의 사기업 수준 컨테이너 레지스트리에 있어야 합니다.
 - 앱에서 내린 결정이 호텔에도 적용되어야 하므로 애완 동물 사진에 사용되는 API 서비스는 실제 환경에서 정확하고 신뢰할 수 있어야 합니다. 액세스가 부여된 애완 동물은 호텔에 머물 수 있습니다.
 - DevOps 파이프라인 요구 사항을 충족하기 위해 Contoso는 Git 리포지토리와 함께 SCM(소스 코드 관리)용 Azure DevOps를 사용할 예정입니다.  자동화된 빌드 및 릴리스를 사용하여 코드를 작성하고 Azure Web Apps, Azure Functions 및 AKS에 배포합니다.
 - 백 엔드의 마이크로 서비스와 프런트 엔드의 웹 사이트용으로 여러 CI/CD 파이프라인이 필요합니다.
 - 백 엔드 서비스의 릴리스 주기는 프런트 엔드 웹앱과는 다릅니다.  이 요구 사항을 충족하기 위해 Contoso에서는 각기 다른 두 DevOps 파이프라인을 배포합니다.
 - Contoso에서는 모든 프런트 엔드 웹 사이트 배포를 관리자가 승인해야 하며, CI/CD 파이프라인에서 이 승인 기능을 제공해야 합니다.


## <a name="solution-design"></a>솔루션 디자인

목표 및 요구 사항을 확정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.

### <a name="current-app"></a>현재 앱

- SmartHotel360 온-프레미스 앱은 두 개의 VM(WEBVM 및 SQLVM)에 계층화되어 있습니다.
- VM은 VMware ESXi 호스트 **contosohost1.contoso.com**(버전 6.5)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM은 서비스 해제됩니다.


### <a name="proposed-architecture"></a>제안된 아키텍처

- 앱의 프런트 엔드는 기본 Azure 지역에 Azure App Services 웹앱으로 배포됩니다.
- Azure 함수가 애완 동물 사진 업로드 기능을 제공하며, 사이트는 이 기능과 상호 작용합니다.
- 애완 동물 사진 함수는 Cognitive Services Vision API 및 CosmosDB를 활용합니다.
- 사이트의 백 엔드는 마이크로 서비스를 사용하여 빌드됩니다. 그리고 AKS(Azure Kubernetes Service)에서 관리하는 컨테이너에 배포됩니다.
- 컨테이너는 Azure DevOps를 사용하여 빌드되어 ACR(Azure Container Registry)에 푸시됩니다.
- 당분간 Contoso는 Visual Studio를 사용하여 웹앱 및 함수 코드를 수동으로 배포할 예정입니다.
- 마이크로 서비스는 Kubernetes 명령줄 도구를 호출하는 PowerShell 스크립트를 사용하여 배포됩니다.

    ![시나리오 아키텍처](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>솔루션 검토

Contoso는 장점 및 단점 목록을 함께 조합하여 제안된 디자인을 평가합니다.

**고려 사항** | **세부 정보**
--- | ---
**장점** | 종단 간 배포에 PaaS 및 무서버 솔루션을 사용하면 Contoso가 제공해야 하는 관리 시간이 크게 줄어듭니다.<br/><br/> 마이크로 서비스 아키텍처로 이동하면 Contoso는 장기적으로 솔루션을 쉽게 확장할 수 있습니다.<br/><br/> 기존 솔루션 코드 베이스를 중단하지 않고 새 기능을 온라인으로 전환할 수 있습니다.<br/><br/> 웹앱은 단일 실패 지점이 없도록 여러 인스턴스를 사용하여 구성됩니다.<br/><br/> 앱이 달라지는 트래픽 볼륨을 처리할 수 있도록 자동 크기 조정이 사용됩니다.<br/><br/> PaaS 서비스로 이동한 Contoso는 Windows Server 2008 R2 운영 체제에서 실행되는 오래된 솔루션을 사용 중지할 수 있습니다.<br/><br/> CosmosDB는 Contoso에서 구성할 필요가 없는 기본 내결함성을 갖추고 있습니다. 따라서 데이터 계층은 더 이상 단일 장애 조치(failover) 지점이 아닙니다.
**단점** | 컨테이너는 다른 마이그레이션 옵션보다 복잡합니다. 학습 곡선이 Contoso에는 문제가 될 수 있습니다.  학습 곡선에도 불구하고 여러 가치를 제공하는 새로운 복잡성이 추가됩니다.<br/><br/> Contoso의 운영 팀은 Azure, 컨테이너 및 앱용 마이크로 서비스를 이해하고 지원하기 위해 노력해야 합니다.<br/><br/> Contoso는 전체 솔루션에 대한 DevOps를 완전히 구현하지 않았습니다. 그러므로 AKS, Functions 및 App Services에 서비스를 배포할 수 있도록 완전한 DevOps 구현을 고려해야 합니다.



### <a name="migration-process"></a>마이그레이션 프로세스

1. Contoso는 ACR, AKS 및 CosmosDB를 프로비전합니다.
2. Azure 웹앱, 저장소 계정, 함수 및 API를 포함하여 배포용 인프라를 프로비전합니다. 
3. 인프라가 준비되면 마이크로 서비스 컨테이너 이미지를 ACR에 푸시하는 Azure DevOps를 사용하여 마이크로 서비스 컨테이너 이미지를 빌드합니다.
4. Contoso는 PowerShell 스크립트를 사용하여 이러한 마이크로 서비스를 ASK에 배포합니다.
5. 마지막으로, Azure 함수 및 웹앱을 배포합니다.

    ![마이그레이션 프로세스](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure 서비스

**서비스** | **설명** | **비용**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Kubernetes 관리, 배포 및 운영을 간소화합니다. 완전히 관리되는 Kubernetes 컨테이너 오케스트레이션 서비스를 제공합니다.  | AKS는 무료 서비스입니다.  가상 머신, 연결된 저장소, 사용한 네트워킹 리소스에 대한 비용만 지불하면 됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | 서버리스 이벤트 구동 컴퓨팅 환경으로 개발을 가속화합니다. 필요할 때 확장할 수 있습니다.  | 사용한 리소스 비용만 지불하면 됩니다. 초당 리소스 사용량과 실행 횟수에 따라 요금이 청구됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/functions/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | 모든 유형의 컨테이너 배포용 이미지를 저장합니다. | 기능, 저장소 및 사용 기간에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | 모든 플랫폼에서 실행되는 엔터프라이즈급 웹, 모바일 및 API 앱을 빠르게 빌드, 배포 및 확장하세요. | App Service 계획은 초 단위로 요금이 청구됩니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>필수 조건

이 시나리오를 위해 Contoso에 필요한 항목은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독** | Contoso는 이전 문서에서 구독을 만들었습니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 체험 계정을 만들면 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.
**Azure 인프라** | Contoso가 Azure 인프라를 설정하는 [방법에 대해 알아보세요](contoso-migration-infrastructure.md).
**개발자 필수 구성 요소** | Contoso는 개발자 워크스테이션에서 다음 도구가 필요합니다.<br/><br/> - [Visual Studio 2017 Community Edition: 버전 15.5](https://www.visualstudio.com/)<br/><br/> .NET 워크로드를 사용하도록 설정해야 합니다.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> Windows 컨테이너를 사용하도록 설정된 [Docker CE(Windows 10) 또는 Docker EE(Windows Server)](https://docs.docker.com/docker-for-windows/install/).



## <a name="scenario-steps"></a>시나리오 단계

Contoso가 마이그레이션을 실행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: AKS 및 ACR 프로비전**: Contoso는 PowerShell을 사용하여 관리되는 AKS 클러스터 및 Azure 컨테이너 레지스트리를 프로비전합니다.
> * **2단계: Docker 컨테이너 빌드**: Azure DevOps를 사용하여 Docker 컨테이너용 CI를 설정하고 ACR에 컨테이너를 푸시합니다.
> * **3단계: 백 엔드 마이크로서비스 배포**: 백 엔드 마이크로서비스에서 활용할 나머지 인프라를 배포합니다.
> * **4단계: 프런트 엔드 인프라 배포**: 애완 동물 휴대폰, Cosmos DB 및 Vision API에 대한 Blob 스토리지를 포함하여 프런트 엔드 인프라를 배포합니다.
> * **5단계: 백 엔드 마이그레이션**: AKS에 마이크로서비스를 배포하며 실행하고, 백 엔드를 마이그레이션합니다.
> * **6단계: 프런트 엔드 게시**: Azure App Service에 SmartHotel360 앱 및 애완 동물 서비스에 의해 호출되는 Function App을 게시합니다.



## <a name="step-1-provision-back-end-resources"></a>1단계: 백 엔드 리소스 프로비전

Contoso 관리자는 AKS 및 ACR(Azure Container Registry)을 사용하여 관리되는 Kubernetes 클러스터를 만드는 배포 스크립트를 실행합니다.

- 이 섹션의 명령은 **SmartHotel360-Azure-backend** 리포지토리를 사용합니다.
- **SmartHotel360-Azure-backend** GitHub 리포지토리는 배포의 이 부분에 대한 모든 소프트웨어를 포함하고 있습니다.

### <a name="prerequisites"></a>필수 조건

1. 시작 하기 전에 Contoso 관리자 배포에 사용 되는 개발 컴퓨터에서 모든 필수 구성 요소 소프트웨어가 설치 되어 있는지 확인 합니다.
2. 그리고 Git을 사용(**git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**)하여 개발 머신에 로컬로 리포지토리를 복제합니다.


### <a name="provision-aks-and-acr"></a>AKS 및 ACR 프로비전

Contoso 관리자는 다음 단계를 통해 프로비전을 진행합니다.

1.  Visual Studio Code를 사용하여 폴더를 열고 **gen-aks-env.ps1** 스크립트가 포함된 **/deploy/k8s** 디렉터리로 이동합니다.
2. 그런 다음 AKS 및 ACR을 사용하여 관리되는 Kubernetes 클러스터를 만드는 스크립트를 실행합니다.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  파일이 열린 상태에서 $location 매개 변수를 **eastus2**로 업데이트하고 파일을 저장합니다.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. **보기** > **통합 터미널**을 클릭하여 Code에서 통합 터미널을 엽니다.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. PowerShell 통합 터미널에서 Connect AzAccount 명령을 사용 하 여 Azure에 로그인 합니다. PowerShell을 시작하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/powershell/azure/get-started-azureps).

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. **az login** 명령을 실행한 후 웹 브라우저에서 인증 지침을 수행하여 Azure CLI를 인증합니다. Azure CLI를 사용하여 로그인하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. 다음 명령을 실행하여 ContosoRG의 리소스 그룹 이름, AKS 클러스터 smarthotel-aks-eus2의 이름 및 새 레지스트리 이름을 전달합니다.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure는 AKS 클러스터에 사용할 리소스가 포함된 또 다른 리소스 그룹을 만듭니다.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. 배포가 완료되면 **kubectl** 명령줄 도구를 설치합니다. 이 도구는 Azure CloudShell에 이미 설치되어 있습니다.

    **az aks install-cli**

10. **kubectl get nodes** 명령을 실행하여 클러스터에 대한 연결을 확인합니다. 노드 이름은 자동으로 만들어진 리소스 그룹의 VM과 동일합니다.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. 다음 명령을 실행하여 Kubernetes 대시보드를 시작합니다. 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

12. 브라우저 탭이 대시보드로 열립니다. Azure CLI를 사용하는 터널링된 연결입니다. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>2단계: 백 엔드 파이프라인 구성

### <a name="create-an-azure-devops-project-and-build"></a>Azure DevOps 프로젝트 및 빌드 만들기

Contoso는 Azure DevOps 프로젝트를 만들고, 컨테이너를 만들어서 ACR에 푸시하도록 CI 빌드를 구성합니다. 이 섹션의 명령은 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 리포지토리를 사용합니다.

1. visualstudio.com에서 새 조직(**contosodevops360.visualstudio.com**)을 만든 다음 Git을 사용하도록 해당 조직을 구성합니다.

2. 버전 제어용 Git과 워크플로용 Agile을 사용하여 새 프로젝트(**SmartHotelBackend**)를 만듭니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. [GitHub 리포지토리](https://github.com/Microsoft/SmartHotel360-Backend)를 가져옵니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. **파이프라인**에서 **빌드**를 클릭한 다음 리포지토리에서 Azure Repos Git을 원본으로 사용하여 새 파이프라인을 만듭니다. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. 빈 작업으로 시작하도록 선택합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. 빌드 파이프라인으로 **호스팅되는 Linux 미리 보기**를 선택합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. **1단계**에서 **Docker Compose** 작업을 추가합니다. 이 작업은 Docker Compose를 빌드합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. 같은 프로세스를 반복하여 또 다른 **Docker Compose** 작업을 추가합니다. 이 작업은 컨테이너를 ACR로 푸시합니다.

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. 빌드할 첫 번째 작업을 선택하고 Azure 구독, 권한 부여 및 ACR을 사용하여 빌드를 구성합니다. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. 리포지토리의 **src** 폴더에서 **docker-compose.yaml** 파일 경로를 지정합니다. 서비스 이미지를 빌드하기로 선택하고 최신 태그를 포함합니다. 작업이 **서비스 이미지 빌드**로 변경되면 Azure DevOps 작업의 이름이 **자동으로 서비스 빌드**로 변경됩니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. 이제 푸시할 두 번째 Docker 작업을 구성합니다. 구독을 선택하고 **smarthotelacreus2** ACR을 선택합니다. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. 다시 한 번 docker-compose.yaml 파일에 파일을 입력하고 **서비스 이미지 푸시**를 선택한 후 최신 태그를 포함합니다. 작업이 **서비스 이미지 푸시**로 변경되면 Azure DevOps 작업의 이름이 **자동으로 서비스 푸시**로 변경됩니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. Azure DevOps 작업이 구성되면 Contoso는 빌드 파이프라인을 저장하고 빌드 프로세스를 시작합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. 빌드 작업을 클릭하여 진행 상태를 확인합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. 빌드가 완료되면 ACR은 마이크로 서비스에서 사용하는 컨테이너로 채워진 새 리포지토리를 표시합니다.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>백 엔드 인프라 배포

AKS 클러스터가 만들어지고 Docker 이미지가 빌드되어, 이제 Contoso 관리자는 백 엔드 마이크로 서비스에서 활용할 나머지 인프라를 배포합니다.

- 이 섹션의 명령은 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 리포지토리를 사용합니다.
- **/deploy/k8s/arm** 폴더에는 모든 항목을 만드는 단일 스크립트가 있습니다. 

Contoso는 다음과 같이 배포합니다.

1. 개발자 명령 프롬프트를 열고 Azure 구독에 대해 az login 명령을 사용합니다.
2. 다음 명령을 입력하고 deploy.cmd 파일을 사용하여 ContosoRG 리소스 그룹 및 EUS2 지역에 Azure 리소스를 배포합니다.

    **.\deploy.cmd azuredeploy ContosoRG -c eastus2**

    ![백 엔드 배포](./media/contoso-migration-rebuild/backend1.png)

2. Azure Portal에서 나중에 사용할 수 있도록 각 데이터베이스의 연결 문자열을 캡처합니다.

    ![백 엔드 배포](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>백 엔드 릴리스 파이프라인 만들기

이제 Contoso 관리자는 다음과 같은 작업을 수행합니다.

- 서비스에 대한 인바운드 트래픽을 허용하도록 NGINX 수신 컨트롤러를 배포합니다.
- AKS 클러스터에 마이크로 서비스를 배포합니다.
- 첫 단계에서는 Azure DevOps를 사용하여 마이크로 서비스에 대한 연결 문자열을 업데이트합니다. 그런 다음 마이크로 서비스를 배포하도록 새 Azure DevOps 릴리스 파이프라인을 구성합니다.
- 이 섹션의 명령은 [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) 리포지토리를 사용합니다.
- 구성 설정 중 일부는(예: Active Directory B2C) 이 문서에서 다루지 않습니다. 이러한 설정에 대한 추가 정보는 리포지토리에서 확인하세요.

Contoso 관리자는 다음 단계에 따라 파이프라인을 만듭니다.

1. Visual Studio를 사용하여 앞에서 적어 두었던 데이터베이스 연결 정보로 **/deploy/k8s/config_local.yml** 파일을 업데이트합니다.

    ![DB 연결](./media/contoso-migration-rebuild/back-pipe1.png)

2. Azure DevOps을 열고 SmartHotel360 프로젝트의 **릴리스**에서 **+새 파이프라인**을 클릭합니다.

    ![새 파이프라인](./media/contoso-migration-rebuild/back-pipe2.png)

3. **빈 작업**을 클릭하여 템플릿 없이 파이프라인을 시작합니다.
4. 스테이지 및 파이프라인 이름을 입력합니다.

      ![단계 이름](./media/contoso-migration-rebuild/back-pipe4.png)

      ![파이프라인 이름](./media/contoso-migration-rebuild/back-pipe5.png)

5. 아티팩트를 추가합니다.

     ![아티팩트 추가](./media/contoso-migration-rebuild/back-pipe6.png)

6. 원본 유형으로 **Git**을 선택하고 SmartHotel360 앱의 프로젝트, 원본 및 마스터 분기를 지정합니다.

    ![아티팩트 설정](./media/contoso-migration-rebuild/back-pipe7.png)

7. 작업 링크를 클릭합니다.

    ![작업 링크](./media/contoso-migration-rebuild/back-pipe8.png)

8. Azure 환경에서 PowerShell 스크립트를 실행할 수 있도록 새 Azure PowerShell 작업을 추가합니다.

    ![Azure의 PowerShell](./media/contoso-migration-rebuild/back-pipe9.png)

9. 작업에 해당하는 Azure 구독을 선택하고 Git 리포지토리에서 **deploy.ps1** 스크립트를 선택합니다.

    ![스크립트 실행 ](./media/contoso-migration-rebuild/back-pipe10.png)


10. 스크립트에 인수를 추가합니다. 이 스크립트는 **수신** 및 **수신 컨트롤러**를 제외한 모든 클러스터 콘텐츠를 삭제하고 마이크로 서비스를 배포합니다.

    ![스크립트 인수](./media/contoso-migration-rebuild/back-pipe11.png)

11. 기본 설정 Azure PowerShell 버전을 최신 버전으로 설정하고 파이프라인을 저장합니다.
12. **릴리스** 페이지로 돌아가 새 릴리스를 수동으로 만듭니다.

    ![새 릴리스](./media/contoso-migration-rebuild/back-pipe12.png)

13. 릴리스를 만든 다음 클릭하고 **작업**에서 **배포**를 클릭합니다.

      ![릴리스 배포](./media/contoso-migration-rebuild/back-pipe13.png)  

14. 배포가 완료되면 **kubectl get services** 명령을 실행하여 Azure Cloud Shell을 사용해 서비스 상태를 확인합니다.


## <a name="step-3-provision-front-end-services"></a>3단계: 프런트 엔드 서비스 프로비전

Contoso 관리자는 프런트 엔드 앱에서 사용할 인프라를 배포해야 합니다. 이를 위해 애완 동물 이미지를 저장할 BLOB Storage 컨테이너, 애완 동물 정보가 포함된 문서를 저장할 Cosmos 데이터베이스 및 웹 사이트용 Vision API를 만듭니다. 

이 섹션의 명령은 [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) 리포지토리를 사용합니다.

### <a name="create-blob-storage-containers"></a>Blob Storage 컨테이너 만들기

1.  앞에서 만든 저장소 계정을 Azure Portal에서 열고 **Blob**을 클릭합니다.
2.  공용 액세스 수준이 컨테이너로 설정된 새 컨테이너(**Pets**)를 만듭니다. 사용자는 애완 동물 사진을 이 컨테이너에 업로드할 것입니다.

    ![Storage Blob](./media/contoso-migration-rebuild/blob1.png)

3. **settings**라는 두 번째 새 컨테이너를 만듭니다. 모든 프런트 엔드 앱 설정이 포함된 파일이 이 컨테이너에 배치됩니다.

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

4. 나중에 참조할 수 있도록 텍스트 파일에서 저장소 계정에 대한 액세스 세부 정보를 캡처합니다.

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Cosmos 데이터베이스 프로비전

Contoso 관리자는 애완 동물 정보에 사용할 Cosmos 데이터베이스를 프로비전합니다.

1. Azure Marketplace에서 **Azure Cosmos DB**를 만듭니다.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 이름(**contosomarthotel**)을 지정하고, SQL API를 선택하고, 기본 지역인 미국 동부 2의 프로덕션 리소스 그룹 ContosoRG에 배치합니다.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 데이터베이스에 새 컬렉션을 추가하고 용량 및 처리량을 기본값으로 설정합니다.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 나중에 참조할 수 있도록 데이터베이스의 연결 정보를 적어 둡니다. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Computer Vision 프로비전

Contoso 관리자는 Computer Vision API를 프로비전합니다. 이 API는 함수에 의해 호출되며, 사용자가 업로드한 사진을 평가하는 용도로 사용됩니다.

1. Azure Marketplace에서 **Computer Vision** 인스턴스를 만듭니다.

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. 기본 지역인 미국 동부 2의 프로덕션 리소스 그룹 ContosoRG에 API(**smarthotelpets**)를 프로비전합니다.

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. 나중에 참조할 수 있도록 API의 연결 설정을 텍스트 파일에 저장합니다.

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Azure 웹앱 프로비전

Contoso 관리자는 Azure Portal을 사용하여 웹앱을 프로비전합니다.


1. 포털에서 **웹앱**을 선택합니다.

    ![웹 앱](media/contoso-migration-rebuild/web-app1.png)

2. 앱 이름(**smarthotelcontoso**)을 입력하고 앱을 Windows에서 실행한 다음 프로덕션 리소스 그룹 **ContosoRG**에 배치합니다. 앱 모니터링용으로 새 Application Insights 인스턴스를 만듭니다.

    ![웹앱 이름](media/contoso-migration-rebuild/web-app2.png)

3. 작업이 완료되면 앱의 주소로 이동하여 앱이 정상적으로 작성되었는지 확인합니다.

4. 이제 Azure Portal에서 코드의 스테이징 슬롯을 만듭니다. 파이프라인은 이 슬롯에 배포됩니다. 따라서 관리자가 릴리스를 수행할 때까지는 코드가 프로덕션 환경에 추가되지 않습니다.

    ![웹 앱 스테이징 슬롯](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Azure 함수 앱 프로비전

Contoso 관리자는 Azure Portal에서 Function App을 프로비전합니다.

1. **Function App**을 선택합니다.

    ![함수 앱 만들기](./media/contoso-migration-rebuild/function-app1.png)

2. 앱 이름(**smarthotelpetchecker**)을 입력합니다. 프로덕션 리소스 그룹 **ContosoRG**에 앱을 배치하고 호스팅 위치를 **사용 계획**으로 설정한 다음 미국 동부 2 지역에 앱을 배치합니다. 새 저장소 계정과 모니터링용 Application Insights 인스턴스가 생성됩니다.

    ![함수 앱 설정](./media/contoso-migration-rebuild/function-app2.png)


3. 앱이 배포되면 앱 주소로 이동하여 앱이 정상적으로 작성되었는지 확인합니다.


## <a name="step-4-set-up-the-front-end-pipeline"></a>4단계: 프런트 엔드 파이프라인 설정

Contoso 관리자는 프런트 엔드 사이트용으로 서로 다른 두 프로젝트를 만듭니다. 

1. Azure DevOps에서 **SmartHotelFrontend** 프로젝트를 만듭니다.

    ![프런트 엔드 프로젝트](./media/contoso-migration-rebuild/function-app1.png)

2. [SmartHotel360 front-end](https://github.com/Microsoft/SmartHotel360-public-web.git) Git 리포지토리를 새 프로젝트로 가져옵니다.
3. Function App용으로는 다른 Azure DevOps 프로젝트(SmartHotelPetChecker)를 만들고 [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) Git 리포지토리를 이 프로젝트로 가져옵니다.

### <a name="configure-the-web-app"></a>웹앱 구성

이제 Contoso 관리자는 Contoso 리소스를 사용하도록 웹앱을 구성합니다.

1. Azure DevOps 프로젝트에 연결한 다음 로컬에서 리포지토리를 개발 머신에 복제합니다.
2. Visual Studio에서 폴더를 열어 리포지토리의 모든 파일을 표시합니다.

    ![리포지토리 파일](./media/contoso-migration-rebuild/configure-webapp1.png)

3. 필요에 따라 구성 변경 내용을 업데이트합니다.

    - 웹앱은 시작되면 **SettingsUrl** 앱 설정을 찾습니다.
    - 이 변수는 구성 파일을 가리키는 URL을 포함해야 합니다.
    - 기본적으로 사용되는 설정은 공용 엔드포인트입니다.

4.  /config-sample.json/sample.json 파일을 업데이트합니다.

    - 이 파일은 공용 엔드포인트를 사용할 때 웹의 구성 파일입니다.
    - AKS API 엔드포인트, 저장소 계정, Cosmos 데이터베이스에 대한 값을 사용하여 **urls** 및 **pets_config** 섹션을 편집합니다.
    - URL은 Contoso에서 만드는 새 웹앱의 DNS 이름과 일치해야 합니다.
    - Contoso의 경우 **smarthotelcontoso.eastus2.cloudapp.azure.com**입니다.

    ![Json 설정](./media/contoso-migration-rebuild/configure-webapp2.png)

5. 파일이 업데이트되면 파일 이름을 **smarthotelsettingsurl**로 바꾼 후 이전에 만든 BLOB 스토리지에 업로드합니다.

    ![이름 바꾸기 및 업로드](./media/contoso-migration-rebuild/configure-webapp3.png)

6. Contoso는 파일을 클릭하여 URL을 가져옵니다. 앱은 구성 파일을 가져올 때 이 URL을 사용합니다.

    ![앱 URL](./media/contoso-migration-rebuild/configure-webapp4.png)

7. **appsettings.Production.json** 파일의 **SettingsURL**을 새 파일의 URL로 업데이트합니다.

    ![URL 업데이트](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Azure App Service에 웹 사이트 배포

이제 Contoso 관리자는 웹 사이트를 게시할 수 있습니다.


1. Azure DevOps를 열고 **SmartHotelFrontend** 프로젝트의 **빌드 및 릴리스**에서 **+새 파이프라인**을 클릭합니다.
2. 원본으로 **Azure DevOps Git**을 선택합니다.
3. **ASP.NET Core** 템플릿을 선택합니다.
4. 파이프라인을 검토하여 **웹 프로젝트 게시** 및 **게시된 프로젝트 압축**이 선택되어 있는지 확인합니다.

    ![파이프라인 설정](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. **트리거**에서 지속적인 통합을 사용하도록 설정하고, 마스터 분기를 추가합니다. 이렇게 하면 솔루션이 새 코드를 마스터 분기로 커밋할 때마다 빌드 파이프라인이 시작됩니다.

    ![연속 통합](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. **저장 및 큐**를 클릭하여 빌드를 시작합니다.
7. 빌드가 완료되면 **Azure App Service 배포**를 사용하여 릴리스 파이프라인을 구성합니다.
8. 스테이지 이름으로는 **스테이징**을 입력합니다.

    ![환경 이름](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. 아티팩트를 추가하고 방금 구성한 빌드를 선택합니다.

     ![아티팩트 추가](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. 아티팩트에서 번개 모양 아이콘을 클릭하여 연속 배포를 사용하도록 설정합니다.

    ![지속적인 배포](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. **환경**에서 **스테이징** 아래의 **1개 작업, 1개 태스크**를 클릭합니다.
12. 구독과 앱 이름을 선택한 후 **Azure App Service 배포** 작업을 엽니다. 배포는 **staging** 배포 슬롯을 사용하도록 구성됩니다. 이렇게 하면 이 슬롯에서 검토 및 승인할 수 있도록 코드가 자동으로 작성됩니다.

     ![슬롯](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. **파이프라인**에서 새 스테이지를 추가합니다.

    ![새 환경](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. **Azure App Service 배포 및 슬롯**을 선택하고 환경 이름을 **Prod**로 지정합니다.
15. **1개 작업, 2개 태스크**를 클릭하고 구독, 앱 서비스 이름 및 **staging** 슬롯을 선택합니다.

    ![환경 이름](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. 파이프라인에서 **Azure App Service 배포 대상 슬롯**을 제거합니다. 이 슬롯은 이전 단계에서 파이프라인에 배치된 것입니다.

    ![파이프라인에서 제거](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. 파이프라인을 저장하고 파이프라인에서 **배포 후 조건**을 클릭합니다.

    ![배포 후](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. **배포 후 승인**을 사용하도록 설정하고 승인자로 개발 리드를 추가합니다.

    ![배포 후 승인](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. 빌드 파이프라인에서 빌드를 수동으로 시작합니다. 그러면 staging 슬롯에 사이트를 배포하는 새 릴리스 파이프라인이 트리거됩니다. Contoso에서 해당 슬롯의 URL은 **https://smarthotelcontoso-staging.azurewebsites.net/** 입니다.
16. 빌드가 완료되고 릴리스가 슬롯에 배포되면 Azure DevOps에서 개발 리드에게 승인을 위한 전자 메일을 보냅니다.
17. 개발 리드는 **승인 보기**를 클릭하여 Azure DevOps 포털에서 요청을 승인하거나 거부할 수 있습니다.

    ![승인 메일](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. 리드가 메모를 작성하고 요청을 승인합니다. 그러면 **staging** 슬롯과 **prod** 슬롯의 교환이 시작되며 빌드가 프로덕션 환경으로 이동됩니다.

    ![승인 및 교환](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. 파이프라인에서 교환이 완료됩니다.

    ![교환 완료](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. 팀이 **prod** 슬롯을 검사해 웹앱이 **https://smarthotelcontoso.azurewebsites.net/** 의 프로덕션 환경에 있는지 확인합니다.


### <a name="deploy-the-petchecker-function-app"></a>PetChecker Function App 배포

Contoso 관리자는 다음과 같이 앱을 배포합니다.

1. Azure DevOps 프로젝트에 연결하여 로컬에서 리포지토리를 개발 머신에 복제합니다.
2. Visual Studio에서 폴더를 열어 리포지토리의 모든 파일을 표시합니다.
3. **src/PetCheckerFunction/local.settings.json** 파일을 연 다음 저장소, Cosmos 데이터베이스 및 Computer Vision API용 앱 설정을 추가합니다.

    ![함수 배포](./media/contoso-migration-rebuild/function5.png)

4. 코드를 커밋한 다음 Azure DevOps에 다시 동기화하여 변경 내용을 푸시합니다.
5. 새 빌드 파이프라인을 추가하고 원본으로 **Azure DevOps Git**을 선택합니다.
6. **ASP.NET Core(.NET Framework)** 템플릿을 선택합니다.
7. 템플릿의 기본값을 적용합니다.
8. **트리거**에서 **연속 통합 사용**을 선택하고 **저장 및 큐**를 클릭하여 빌드를 시작합니다.
9. 빌드가 정상적으로 완료되면 **Azure App Service 배포 및 슬롯**을 추가하여 릴리스 파이프라인을 빌드합니다.
10. 환경 이름을 **Prod**로 지정하고 구독을 선택합니다. **앱 유형**은 **Function App**으로 설정하고 앱 서비스 이름은 **smarthotelpetchecker**로 설정합니다.

    ![함수 앱](./media/contoso-migration-rebuild/petchecker2.png)

11. **빌드** 아티팩트를 추가합니다.

    ![아티팩트](./media/contoso-migration-rebuild/petchecker3.png)

12. **연속 배포 트리거**를 사용하도록 설정하고 **저장**을 클릭합니다.
13. **새 빌드 큐 대기**를 클릭하여 전체 CI/CD 파이프라인을 실행합니다.
14. 배포된 함수는 Azure Portal에 **실행 중** 상태로 표시됩니다.

    ![함수 배포](./media/contoso-migration-rebuild/function6.png)


7. [http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets)에서 Pet Checker AI가 예상대로 작동하는지 테스트하기 위한 앱을 찾습니다.
8. 그림을 업로드하려면 아바타를 클릭합니다.

    ![함수 배포](./media/contoso-migration-rebuild/function7.png)

9. 검사할 첫 번째 사진은 작은 개입니다.

    ![함수 배포](./media/contoso-migration-rebuild/function8.png)

10. 앱에서 승인 메시지를 반환합니다.

    ![함수 배포](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>배포 검토

이제 Contoso는 Azure에서 마이그레이션된 리소스를 사용하여 새 인프라를 완전하게 운영하고 보호해야 합니다.

### <a name="security"></a>보안

- Contoso는 새 데이터베이스가 안전한지 확인해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- 인증서에 SSL을 사용하도록 앱을 업데이트해야 합니다. 443에서 응답하도록 컨테이너 인스턴스를 다시 배포해야 합니다.
- Contoso는 Key Vault를 사용하여 Service Fabric 앱의 비밀을 보호하는 방안을 고려해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>백업 및 재해 복구

- Contoso는 Azure SQL Database에 대한 백업 요구 사항을 검토해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso는 데이터베이스에 지역별 장애 조치(failover)를 제공하는 SQL 장애 조치(failover) 그룹을 구현하는 방안을 고려해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso는 ACR 프리미엄 SKU에 지역 복제를 활용할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Cosmos DB는 자동으로 백업합니다. Contoso는 이 프로세스에 대해 [자세히 알아볼 수 있습니다](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore).

### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

- 모든 리소스가 배포된 후 Contoso는 해당 [인프라 계획](contoso-migration-infrastructure.md#set-up-tagging)을 따라 Azure 태그를 할당해야 합니다.
- 모든 라이선스는 Contoso가 사용하는 PaaS 서비스의 비용으로 빌드됩니다. 이 요금은 EA에서 공제됩니다.
- Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Azure 및 기타 클라우드 리소스를 활용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.  Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview).

## <a name="conclusion"></a>결론

이 문서에서, Contoso는 Azure에서 SmartHotel360 앱을 다시 빌드했습니다. 온-프레미스 앱 프런트 엔드 VM을 Azure App Services 웹앱에 다시 빌드했습니다. 앱 백 엔드는 AKS(Azure Kubernetes Service)에서 관리하는 컨테이너에 배포된 마이크로 서비스를 사용하여 빌드됩니다. Contoso는 애완 동물 사진 앱을 사용하여 앱 기능을 개선했습니다.




