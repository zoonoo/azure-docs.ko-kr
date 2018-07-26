---
title: Contoso가 Azure로 마이그레이션하기 위한 온-프레미스 워크로드 평가 | Microsoft Docs
description: Contoso가 Azure Migration 및 Database MigratIon을 사용하여 Azure로 마이그레이션할 온-프레미스 머신을 평가하는 방법을 알아보세요.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006569"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso 마이그레이션: Azure로 마이그레이션하기 위한 온-프레미스 워크로드 평가

이 문서에서는 Contoso가 Azure로 마이그레이션하기 위한 준비 과정에서 온-프레미스 SmartHotel 앱을 평가하는 방법을 보여 줍니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 설명하는 문서 시리즈의 세 번째 문서입니다. 이 문서 시리즈에는 배경 정보와 마이그레이션 인프라를 설정하고, 마이그레이션을 위한 온-프레미스 리소스의 적합성을 평가하고, 다양한 유형의 마이그레이션을 실행하는 방법을 설명하는 일련의 배포 시나리오가 담겨 있습니다. 시나리오가 점점 더 복잡해지고 시간이 지남에 따라 문서도 계속 추가될 것입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso가 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법을 설명합니다. 모든 마이그레이션 문서에 동일한 인프라가 사용됩니다. | 사용 가능
문서 3: Azure로 마이그레이션할 온-프레미스 리소스 평가  | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱 평가를 실행하는 방법을 보여 줍니다. Contoso가 [Azure Migrate](migrate-overview.md) 서비스로 앱 VM을 평가하고 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)로 앱 SQL Server 데이터베이스를 평가합니다. | 이 문서의 내용:
[문서 4: 앱을 Azure VM 및 SQL Managed Instance에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel 앱에 대해 Azure로의 리프트 앤 시프트 마이그레이션을 실행하는 방법을 보여줍니다. Contoso는 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 SQL 관리되는 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 5: 앱을 Azure VM에 다시 호스트](contoso-migration-rehost-vm.md) | Contoso가 Site Recovery 서비스를 사용하여 SmartHotel 앱 VM을 Azure VM으로 마이그레이션하는 방법을 보여줍니다. | 사용 가능
[문서 6: 앱을 Azure VM 및 SQL Server Always On 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 SmartHotel 앱을 마이그레이션하는 방법을 보여 줍니다. Contoso가 Site Recovery를 사용하여 앱 VM을 마이그레이션하고 Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호하는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고 MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 9: Azure Web Apps 및 Azure SQL 데이터베이스에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso가 SmartHotel 앱을 Azure 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션하는 방법 시연 | 사용 가능
[문서 10: Azure Web Apps 및 Azure MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso가 지속적인 업데이트를 위해 GitHub와 통합된, 여러 사이트의 Azure Web Apps로 Linux osTicket 앱을 마이그레이션하는 방법을 보여줍니다. Azure MySQL 인스턴스로 앱 데이터베이스를 마이그레이션합니다. | 사용 가능
[문서 11: VSTS에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso가 해당 온-프레미스 TFS(Team Foundation Server) 배포를 Azure에서 VSTS(Visual Studio Team Services)로 마이그레이션하는 방법을 보여줍니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 아키텍처 변경](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso가 App Services, Azure Kubernetes, Azure Functions, Cognitive services 및 Cosmos DB를 포함한 다양한 Azure 기능 및 서비스를 사용하여 해당 SmartHotel 앱을 다시 빌드하는 방법을 보여줍니다. | 사용 가능


## <a name="overview"></a>개요

Azure로 마이그레이션을 고려 중인 Contoso는 기술 및 재무 평가를 통해 온-프레미스 워크로드가 클라우드로 마이그레이션하기에 적합한지 알아보려 합니다. 특히, Contoso 팀은 머신 및 데이터베이스의 마이그레이션 호환성을 평가하고, Azure에서 리소스를 실행하기 위해 필요한 용량과 비용을 예측해 보려 합니다.

마이그레이션을 시작하고 관련 기술을 정확하게 이해하기 위해 다음 표에 요약된 대로 두 개의 온-프레미스 앱을 평가할 것입니다. 마이그레이션을 위해 앱을 다시 호스트하고 리팩터링하는 마이그레이션 시나리오에 대해 평가할 것입니다. [Contoso 마이그레이션 개요](contoso-migration-overview.md)에서 다시 호스트 및 리팩터링에 대해 자세히 알아보세요.

**앱 이름** | **플랫폼** | **앱 계층** | **세부 정보**
--- | --- | --- | ---
SmartHotel<br/><br/> Contoso 여행 요구 사항 관리 | SQL Server 데이터베이스를 사용하는 Windows에서 실행 | 하나의 VM(WEBVM)에서 실행되는 프런트 엔드 ASP.NET 웹 사이트와 또 하나의 VM(SQLVM)에서 실행되는 SQL Server를 사용하는 2계층 앱 | VM은 vCenter Server를 통해 관리되는 ESXi 호스트에서 실행되는 VMware입니다.<br/><br/> 샘플 앱은 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.
OSTicket<br/><br/> Contoso 서비스 데스크 앱 | MySQL PHP(LAMP)를 사용하는 Linux/Apache에서 실행 | 하나의 VM(OSTICKETWEB)에 있는 프런트 엔드 PHP 웹 사이트와 또 하나의 VM(OSTICKETMYSQL)에서 실행되는 MySQL 데이터베이스를 사용하는 2계층 앱 | 이 앱은 고객 서비스 앱에서 내부 직원 및 외부 고객의 문제를 추적하는 데 사용됩니다.<br/><br/> 샘플 앱은 [GitHub](https://github.com/osTicket/osTicket)에서 다운로드할 수 있습니다.

## <a name="current-architecture"></a>현재 아키텍처


다음은 현재의 Contoso 온-프레미스 인프라를 보여 주는 다이어그램입니다.

![Contoso 아키텍처](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso에는 미국 동부의 뉴욕 시에 위치한 하나의 주 데이터 센터가 있습니다.
- 미국 전역에 세 개의 지점이 추가로 있습니다.
- 주 데이터 센터는 파이버 메트로 이더넷 연결(500mbps)을 통해 인터넷에 연결됩니다.
- 각 지점은 IPSec VPN 터널을 통해 주 데이터 센터로 다시 연결되는 비즈니스 클래스 연결을 통해 인터넷에 로컬로 연결됩니다. 이렇게 하면 전체 네트워크가 영구적으로 연결되고 인터넷 연결이 최적화됩니다.
- 주 데이터 센터는 VMware를 통해 완벽하게 가상화됩니다. vCenter Server 6.5에서 관리되는 두 개의 ESXi 6.5 가상화 호스트가 있습니다.
- Contoso에서 ID 관리에는 Active Directory를 사용하고, 내부 네트워크에는 DNS 서버를 사용합니다.
- 데이터 센터의 도메인 컨트롤러는 VMware VM에서 실행됩니다. 지점의 도메인 컨트롤러는 실제 서버에서 실행됩니다.





## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와 긴밀히 협력하여 회사에서 이러한 마이그레이션을 통해 달성하려는 목표와 관련하여 다음과 같이 파악했습니다.

- **비즈니스 성장 대응**: Contoso는 성장하고 있으며, 이에 따라 온-프레미스 시스템과 인프라에 부담을 주고 있습니다.
- **효율성 증대**: Contoso는 불필요한 프로시저를 제거하고 개발자와 사용자를 위한 프로세스를 간소화해야 합니다.  회사에서 IT 속도를 높이고 시간이나 비용을 낭비하지 않도록 하여 고객 요구 사항을 더 빠르게 제공해야 합니다.
- **민첩성 향상**: Contoso IT에서 비즈니스 요구 사항에 대해 더 빠르게 응답해야 합니다. 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다.  사업상의 걸림돌이 되지 않아야 합니다.
- **확장**: Contoso IT는 성공적인 비즈니스 성장에 따라 동일한 속도로 확장할 수 있는 시스템을 제공해야 합니다.

## <a name="assessment-goals"></a>평가 목표

Contoso 클라우드 팀은 마이그레이션 평가의 목표를 확실하게 정했습니다.

- 마이그레이션 후 Azure의 앱은 현재 온-프레미스 VMWare 환경에서와 동일한 성능 기능이 있어야 합니다.  클라우드로 이동해도 앱 성능의 중요성이 작아지는 것은 아닙니다.
- Contoso는 Azure 요구 사항에 대한 응용 프로그램과 데이터베이스의 호환성 그리고 Azure의 호스팅 옵션을 이해해야 합니다.
- 앱이 클라우드로 이동된 후에는 Contoso의 데이터베이스 관리가 최소화되어야 합니다.  
- Contoso는 마이그레이션 옵션뿐만 아니라 클라우드로 이동된 후의 인프라 관련 비용에 대해서도 이해하려고 합니다.

## <a name="assessment-tools"></a>평가 도구
Contoso는 평가를 위해 Microsoft 도구를 사용합니다. 이러한 도구는 목표에 맞춰져 있으며 필요한 모든 정보를 제공합니다.

**Technology** | **설명** | **비용**
--- | --- | ---
[DMA(Database Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA를 사용하여 Azure의 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 평가하고 검색합니다. DMA는 SQL 원본과 대상 간의 기능 패리티를 평가하고 성능 및 안정성을 향상하기 위한 방안을 제시합니다. | DMA는 무료로 다운로드할 수 있는 도구입니다.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso는 이 서비스를 사용하여 VMware VM을 평가합니다. 이 서비스는 컴퓨터의 마이그레이션 적합성을 평가하고, Azure에서 실행하기 위한 크기 및 비용 추정치를 제공합니다.  | 이 서비스는 현재(2018년 5월) 무료로 제공됩니다.
[서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate는 서비스 맵을 사용하여 마이그레이션하려는 컴퓨터 간의 종속성을 보여줍니다. |  서비스 맵은 Azure Log Analytics에 포함되어 있습니다. 현재 180일 동안 무료로 사용할 수 있습니다.

이 시나리오에서는 Contoso가 DMA를 다운로드하고 실행하여 여행 앱에 사용할 온-프레미스 SQL Server 데이터베이스를 평가합니다. Azure Migrate와 종속성 매핑을 사용하여 앱 VM을 평가한 후 Azure로 마이그레이션합니다.



## <a name="assessment-architecture"></a>평가 아키텍처


![마이그레이션 평가 아키텍처를 설치합니다.](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso는 일반적인 엔터프라이즈 조직을 나타내는 가상 이름입니다.
- Contoso는 온-프레미스 데이터 센터(**contoso-datacenter**)와 온-프레미스 도메인 컨트롤러(CONTOSODC1, CONTOSODC2)가 있습니다.
- VMware VM은 6.5 버전을 실행하는 VMware ESXI 호스트에 있습니다. 호스트: **contosohost1**, **contosohost2**
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**venter**)를 통해 관리됩니다.
- SmartHotel 여행 앱:
    - 앱은 두 개의 VMware VM인 **WEBVM** 및 **SQLVM**에 계층화되어 있습니다.
    - VM은 VMware ESXi 호스트 **contosohost1.contoso.com**에 있습니다.
    - VM은 Windows Server 2008 R2 Datacenter SP1을 실행하고 있습니다.
- VMware 환경은 VM에서 실행되는 VCenter Server(**vcenter.contoso.com**)를 통해 관리됩니다.
- OSTicket 서비스 데스크 앱:
    - 앱은 두 개의 VM인 **OSTICKETWEB** 및 **OSTICKETMYSQL**에 계층화되어 있습니다.
    - VM은 Ubuntu Linux Server 16.04-LTS에서 실행되고 있습니다.
    - OSTICKETWEB VM은 Apache 2 및 PHP 7.0을 실행하고 있습니다.
    - OSTICKETMYSQL VM은 MySQL 5.7.22를 실행하고 있습니다.

![아키텍처](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>필수 조건

Contoso(및 귀하)가 평가하는 데 필요한 것은 다음과 같습니다.

- Azure 구독 또는 Azure 구독의 리소스 그룹에 대한 소유자 또는 기여자의 액세스 권한.
- 5.5, 6.0 또는 6.5 버전을 실행하는 온-프레미스 vCenter 서버.
- VCenter 서버의 읽기 전용 계정 또는 이러한 계정을 만들 수 있는 권한.
- .OVA 템플릿을 사용하여 VCenter 서버에 VM을 만들 수 있는 권한.
- 5.0 이상을 실행하는 ESXi 호스트 하나 이상.
- 온-프레미스 VMware VM 두 대 이상(그 중 하나는 SQL Server 데이터베이스 실행).
- 각 VM에 Azure Migrate 에이전트를 설치할 권한.
- VM이 인터넷에 직접 연결되어야 합니다.
        - 인터넷 액세스를 [필요한 URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites)로 제한할 수 있습니다.
        - 인터넷에 연결되지 않은 머신의 경우, [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 설치해야 합니다.
- SQL Server 인스턴스를 실행하는 VM의 FQDN(데이터베이스 평가용).
- SQL Server VM에서 실행되는 Windows 방화벽이 TCP 포트 1433(기본 포트)에서 외부 연결을 허용해야만 DMA가 연결할 수 있습니다.


## <a name="assessment-overview"></a>평가 개요

Contoso가 평가를 수행하는 방법은 다음과 같습니다.


> [!div class="checklist"]
> * **1단계: DMA 다운로드 및 설치**: 온-프레미스 SQL Server 데이터베이스를 평가할 DMA를 준비합니다.
> * **2단계: DMA로 데이터베이스 평가**: 데이터베이스 평가를 실행하고 분석합니다.
> * **3단계: Azure Migrate를 사용하여 VM 평가 준비**: 온-프레미스 계정을 설정하고 VMware 설정을 조정합니다.
> * **4단계: Azure Migrate를 사용하여 온-프레미스 VM 검색**: Azure Migrate 수집기 VM을 만듭니다. 그런 다음, 수집기를 실행하여 평가할 VM을 검색합니다.
> * **5단계: Azure Migrate로 종속성 분석 준비**: VM 간의 종속성 매핑을 볼 수 있도록 VM에 Azure Migrate 에이전트를 설치합니다.
> * **6단계: Azure Migrate를 사용하여 VM 평가**: 종속성을 확인하고, VM을 그룹화하고, 평가를 실행합니다. 평가 준비가 완료되면 마이그레이션을 위한 준비 과정으로 평가를 분석합니다.


## <a name="step-1-download-and-install-the-dma"></a>1단계: DMA 다운로드 및 설치

1. Contoso는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 DMA를 다운로드합니다.
    - SQL 인스턴스에 연결할 수 있는 모든 머신에 DMA를 설치할 수 있습니다. 반드시 SQL Server 컴퓨터에서 DMA를 실행할 필요는 없습니다.
    - SQL Server 호스트 머신에서는 실행하면 안 됩니다.
2. 다운로드한 설치 파일(DownloadMigrationAssistant.msi)을 실행하여 설치를 시작합니다.
3. **마침** 페이지에서 마법사를 마치기 전에 **Microsoft Data Migration Assistant 시작**을 선택합니다.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>2단계: SmartHotel에 대한 데이터베이스 평가 실행 및 분석

이제 Contoso는 평가를 실행하여 SmartHotel 앱에 사용되는 온-프레미스 SQL Server를 분석할 수 있습니다.

1. Database Migration Assistant에서 **새로 만들기**를 클릭하고, **평가**를 선택하고, 평가에 프로젝트 이름 **SmartHotel**을 지정합니다.
2. **원본 서버 유형**을 **Azure Virtual Machines의 SQL Server**로 선택합니다.

    ![원본 선택](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      현재 DMA는 SQL 관리되는 인스턴스로의 마이그레이션 평가를 지원하지 않습니다. 이 문제를 해결하기 위해 Contoso는 Azure VM의 SQL Server를 평가의 대상으로 사용합니다.

3. **대상 버전 선택**에서 SQL Server 2017을 대상 버전으로 선택합니다. SQL 관리되는 인스턴스에서 사용하는 버전이므로 이 버전을 선택해야 합니다.
4. 다음과 같은 호환성 및 새 기능에 대한 정보를 검색하도록 선택합니다.
    - **호환성 문제**는 마이그레이션을 중단시킬 수 있는 변경 내용 또는 마이그레이션 전에 사소한 조정이 필요한 변경 내용에 대해 알려줍니다. 현재 사용 중이지만 더 이상 사용되지 않은 기능에 대해서도 알려줍니다. 문제는 호환성 수준에 따라 구성됩니다.
    - **새 권장 기능**은 마이그레이션 후 데이터베이스에 사용할 수 있는 대상 SQL Server 플랫폼의 새 기능에 대해 알려줍니다. 이러한 권장 기능은 성능, 보안 및 저장소를 기준으로 구성됩니다.

    ![대상 선택](./media/contoso-migration-assessment/dma-assessment-2.png)

2. **Connect to a server**(서버에 연결)에서 데이터베이스를 실행하는 VM의 이름과 액세스할 수 있는 자격 증명을 입력합니다. SQL Server에 접속할 수 있도록 하려면 **서버 인증서 신뢰**를 사용하도록 설정해야 합니다. 그런 다음, **연결**을 클릭합니다.

    ![대상 선택](./media/contoso-migration-assessment/dma-assessment-3.png)

3. **원본 추가**에서 평가하려는 데이터베이스를 추가하고 **다음**을 클릭하여 평가를 시작합니다.
4. 평가가 만들어집니다.

    ![평가 만들기](./media/contoso-migration-assessment/dma-assessment-4.png)

5. **결과 검토**에서 평가 결과를 볼 수 있습니다.


### <a name="analyze-the-database-assessment"></a>데이터베이스 평가 분석

결과를 사용할 수 있게 되는 즉시 결과가 표시됩니다. 문제가 해결되면 **평가 다시 시작**을 클릭하여 평가를 다시 실행해야 합니다.

1. **호환성 문제** 보고서에서는 각 호환성 수준의 문제를 확인합니다. 호환성 수준은 다음과 같이 SQL Server 버전에 매핑됩니다.

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![호환성 문제](./media/contoso-migration-assessment/dma-assessment-5.png)

2. Contoso는 **권장 기능** 보고서에서 마이그레이션 후 평가에서 권장하는 성능, 보안 및 저장소 기능을 볼 수 있습니다. 메모리 내 OLTP 및 Columnstore, Stretch Database, Always Encrypted, 동적 데이터 마스킹, TDE(투명한 데이터 암호화)를 포함하여 다양한 권장 기능이 있습니다.

    ![권장 기능](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso는 모든 SQL Server 데이터베이스에 대해 [TDE를 사용](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)하도록 설정하는 것이 좋으며 데이터베이스가 클라우드에 있는 경우에는 이렇게 설정하는 것이 더욱 중요합니다. TDE는 마이그레이션 후에만 사용하도록 설정해야 합니다. TDE를 이미 사용하도록 설정한 경우에는 인증서 또는 비대칭 키를 대상 서버의 마스터 데이터베이스로 이동해야 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. 평가를 JSON 또는 CSV 형식으로 내보낼 수 있습니다.

규모가 큰 평가를 실행하는 경우, 다음을 수행할 수 있습니다.

- 여러 평가를 동시에 실행하고 **모든 평가** 페이지를 열어서 평가 상태를 볼 수 있습니다.
- [평가를 SQL Server 데이터베이스에 통합](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)할 수 있습니다.
- [평가를 PowerBI 보고서에 통합](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)할 수 있습니다.


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>3단계: Azure Migrate를 사용하여 VM 평가 준비

Contoso는 Azure Migrate가 평가할 VM을 자동으로 검색하는 데 사용할 VMware 계정을 만들고, VM을 만들 권한이 있는지 확인하고, 열어야 하는 포트를 메모하고, 통계 설정 수준을 설정해야 합니다.

### <a name="set-up-a-vmware-account"></a>VMware 계정 설정

 VM 검색에는 다음 속성이 있는, vCenter의 읽기 전용 계정이 필요합니다.

- 사용자 유형: 읽기 전용 사용자(최소).
- 권한: 데이터 센터 개체 –> 자식 개체에 전파, role=Read-only.
- 세부 정보: 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.
- 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

### <a name="verify-permissions-to-create-a-vm"></a>VM을 만들 권한이 있는지 확인

Contoso는 .OVA 형식으로 파일을 가져와 VM을 만들 수 있는 권한이 있는지 확인합니다. [자세히 알아보기](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>포트 확인

Contoso 평가에서는 종속성 매핑을 사용합니다. 이 기능을 사용하려면 평가하려는 VM에 에이전트가 설치되어 있어야 합니다. 에이전트는 각 VM의 TCP 포트 443에서 Azure에 연결할 수 있어야 합니다. 연결 요구 사항에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).


### <a name="set-statistics-settings"></a>통계 설정 지정

Contoso는 배포를 시작하기 전에 vCenter Server에 대한 통계 설정 수준을 3으로 설정해야 합니다. 다음 사항에 유의하세요.

- 수준을 설정한 후 평가를 실행하려면 하루 이상 기다려야 합니다. 그렇지 않으면 예상대로 작동하지 않을 수 있습니다.
- 수준이 3보다 높으면 평가가 작동하지는 하지만 다음과 같은 문제가 있습니다.
    - 디스크 및 네트워킹에 대한 성능 데이터가 수집되지 않습니다.
    - 저장소의 경우 Azure Migrate는 Azure에서 온-프레미스 디스크와 크기가 같은 표준 디스크를 권장합니다.
    - 네트워킹의 각 온-프레미스 네트워크 어댑터의 경우 Azure에서 네트워크 어댑터를 권장합니다.
    - 계산의 경우 Azure Migrate는 VM 코어 및 메모리 크기를 살펴본 후 동일한 구성의 Azure VM을 권장합니다. 적합한 Azure VM 크기가 여러 개인 경우 비용이 가장 낮은 크기가 권장됩니다.
- 수준 3의 크기 조정에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

다음과 같이 수준을 설정합니다.

1. vSphere 웹 클라이언트에서 vCenter 서버 인스턴스를 엽니다.
2. **관리** > **설정** > **일반**에서 **편집**을 클릭합니다.
3. **통계**에서 통계 수준 설정을 **수준 3**으로 설정합니다.

    ![vCenter 통계 수준](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>4단계: VM 검색

VM을 검색하기 위해 Contoso는 Azure Migrate 프로젝트를 만듭니다. 수집기 VM을 다운로드하여 설정하고 수집기를 실행하여 온-프레미스 VM을 검색합니다.

### <a name="create-a-project"></a>프로젝트 만들기

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색하고 프로젝트(ContosoMigration)를 만듭니다.
2. 프로젝트 이름, Azure 구독을 지정하고 새 Azure 리소스 그룹, **ContosoFailoverRG**를 만듭니다. 다음 사항에 유의하세요.

    - Azure Migrate 프로젝트는 미국 중서부 또는 미국 동부 지역에서만 만들 수 있습니다.
    - 모든 대상 위치에 대해 마이그레이션을 계획할 수 있습니다.
    - 프로젝트 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

Azure Migrate는 수집기 어플라이언스로 알려진 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 그것들에 대한 메타데이터를 Azure Migrate 서비스에 보냅니다. Contoso는 수집기 어플라이언스를 설정하기 위해 OVA 템플릿을 다운로드하고 온-프레미스 vCenter 서버로 가져와서 VM을 만듭니다.

1. Azure Migrate 프로젝트 > **시작** > **검색 및 평가** > **머신 검색**에서 .OVA 템플릿 파일을 다운로드합니다.
2. 프로젝트 ID 및 키를 복사합니다. 수집기를 구성하는 데 필요합니다.

    ![.ova 파일 다운로드](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

VM을 배포하기 전에 Contoso는 .OVA 파일이 안전한지 확인합니다.

1. 파일이 다운로드된 머신에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 이러한 설정과 일치해야 합니다(버전 1.0.9.12).

**알고리즘** | **해시 값**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>수집기 어플라이언스 만들기

이제 Contoso는 다운로드한 파일을 vCenter Server로 가져와서 구성 서버 VM을 프로비전할 수 있습니다.

1. vSphere 클라이언트 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/contoso-migration-assessment/vcenter-wizard.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 .OVA 파일의 위치를 지정합니다.
3. **이름 및 위치**에서 수집기 VM의 이름과 VM이 호스트될 인벤토리 위치를 지정합니다. 수집기 어플라이언스가 실행될 호스트 또는 클러스터도 지정합니다.
5. **저장소**에서 저장소 위치를 지정하고 **디스크 형식**에서 저장소 프로비전 방법을 지정합니다.
7. **네트워크 매핑**에서 수집기 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다.
8. 설정을 검토하고 **배포 후 전원 켜기**> **마침**을 선택합니다. 어플라이언스가 만들어진 후 완료 확인 메시지가 표시됩니다.

### <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

이제 수집기를 실행하여 VM을 검색합니다. 수집기는 현재 운영 체제 언어 및 수집기 인터페이스 언어로 “영어(미국)”만 지원합니다.

1. vSphere 클라이언트 콘솔 > **콘솔 열기**에서 수집기 VM의 언어, 표준 시간대 및 암호 기본 설정을 지정합니다.
2. 바탕 화면에서 **수집기 실행** 바로 가기를 클릭합니다.

    ![수집기 바로 가기](./media/contoso-migration-assessment/collector-shortcut.png)

4. Azure Migrate Collector > **필수 구성 요소 설정**에서 사용 조건에 동의하고 타사 정보를 읽습니다.
5. 수집기는 VM이 인터넷에 액세스할 수 있는지, 시간이 동기화되는지, 수집기 서비스가 실행 중인지(기본적으로 VM에 설치됨) 확인합니다. VMWare PowerCLI도 설치합니다.

    > [!NOTE]
    > 여기서는 VM이 프록시 없이 인터넷에 직접 액세스할 수 있다고 가정합니다.

    ![필수 조건 확인](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. **vCenter Server 세부 정보 지정**에서 vCenter 서버의 이름(FQDN) 또는 IP 주소와 검색에 사용되는 읽기 전용 자격 증명을 지정합니다.
7. VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내의 VM만 검색할 수 있습니다. 범위를 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM은 1500대 미만이어야 합니다.

    ![VCenter에 연결](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. **마이그레이션 프로젝트 지정**에서 Azure Migrate 프로젝트 ID를 지정하고 포털에서 복사한 키를 지정합니다. 프로젝트 **개요** 페이지 > **머신 검색**에서 다시 가져올 수 있습니다.  

    ![Azure에 연결](./media/contoso-migration-assessment/collector-connect-azure.png)

7. Contoso는 **수집 진행 상황 보기**에서 검색을 모니터링하고 VM에서 수집한 메타데이터가 범위 내에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다.

    ![수집 진행 중](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>포털에서 VM을 확인합니다.

수집이 완료되면 Contoso는 포털에 VM이 표시되는지 확인합니다.

1. Azure Migrate 프로젝트 > **관리** > **머신**에서 검색하려는 VM이 표시되는지 확인합니다.

    ![검색된 컴퓨터](./media/contoso-migration-assessment/discovery-complete.png)

3. 현재 컴퓨터에 Azure Migrate 에이전트가 설치되지 않았습니다. 종속성을 보려면 Contoso가 해당 에이전트를 설치해야 합니다.

    ![검색된 컴퓨터](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>5단계: 종속성 분석 준비

Contoso가 평가하려는 VM 간의 종속성을 보려면 앱 VM에 에이전트를 다운로드하여 설치합니다. Contoso는 Windows 및 Linux 둘 다의 앱에 대해 모든 VM에서 이 작업을 수행합니다.

### <a name="take-a-snapshot"></a>스냅숏 만들기

에이전트가 설치되기 전에 스냅숏을 작성하여 수정하기 전의 VM 사본을 보존합니다.

![컴퓨터 스냅숏](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

1. **머신** 페이지에서 머신을 선택하고 **종속성** 열에서 **설치 필요**를 선택합니다.
2. **머신 검색** 페이지에서 다음을 수행합니다.
    - 각 Windows VM에 대한 MMA 및 Dependency Agent 다운로드
    - 각 Linux VM에 대한 MMA 및 Dependency Agent 다운로드
3. 이제 작업 영역 ID와 키를 복사합니다. MMA를 설치할 때 이러한 정보가 필요합니다.

    ![에이전트 다운로드](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Windows VM에 에이전트 설치

각 VM에서 설치를 실행합니다.

#### <a name="install-the-mma-on-windows-vms"></a>Windows VM에 MMA 설치

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **대상 폴더**에서 기본 설치 폴더를 유지하고 **다음**을 클릭합니다.
2. **에이전트 설치 옵션**에서 **Azure Log Analytics에 에이전트 연결** > **다음**을 선택합니다.

    ![MMA 설치](./media/contoso-migration-assessment/mma-install.png)

5. 포털에서 복사한 작업 영역 ID와 키를 **Azure Log Analytics**에 붙여넣습니다.

    ![MMA 설치](./media/contoso-migration-assessment/mma-install2.png)

6. 이제 **설치 준비 완료**에서 MMA를 설치할 수 있습니다.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Windows VM에 Dependency Agent 설치

1. 다운로드한 Dependency Agent를 두 번 클릭합니다.
2. 사용 조건에 동의하고 설치가 완료될 때까지 기다립니다.

    ![종속성 에이전트](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Linux VM에 에이전트 설치

각 VM에서 설치를 실행합니다.

#### <a name="install-the-mma-on-linux-vms"></a>Linux VM에 MMA 설치

1. **sudo apt-get install python-ctypeslib**를 사용하여 각 VM에 python ctypes 라이브러리를 설치합니다.
2. 명령을 실행하여 MMA 에이전트를 루트로 설치해야 합니다.  루트가 되도록 하려면 **sudo -i** 명령을 실행하고 루트 암호를 입력합니다.
3. 이제 MMA 에이전트를 설치합니다.
    - 올바른 작업 영역 ID 및 키를 명령에 삽입합니다.
    - 명령은 64비트용입니다.
    - **작업 영역 ID** 및 **기본 키**는 OMS 포털 > **설정**의 **연결된 원본** 탭에 있습니다.
    - 다음 명령을 실행하여 OMS 에이전트를 다운로드하고, 체크섬의 유효성을 검사하고, 에이전트를 설치/등록합니다.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Linux VM에 Dependency Agent 설치

MMA가 설치되면 Contoso가 Linux VM에 Dependency Agent를 설치할 수 있습니다.

1. Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 InstallDependencyAgent-Linux64.bin을 사용하여 Linux 컴퓨터에 설치됩니다. sh를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

2. 다음과 같이 Linux Dependency Agent를 루트로 설치합니다.

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>6단계: VM 평가 실행 및 분석

이제 Contoso는 머신 종속성을 확인하고 그룹을 만들 수 있습니다. 그런 다음, 그룹에 대한 평가를 실행합니다.

### <a name="verify-dependencies-and-create-a-group"></a>종속성 확인 및 그룹 만들기


1. 머신에서 분석하려면 **종속성 보기**를 클릭합니다.

    ![컴퓨터 종속성 보기](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. SQLVM의 경우 종속성 맵에 다음과 같은 세부 정보가 표시됩니다.

    - 지정된 시간(기본적으로 1시간) 동안 SQLVM에서 실행되는 활성 네트워크 연결이 있는 프로세스 그룹/프로세스
    - 모든 종속 컴퓨터와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결.
    - Azure Migrate 에이전트가 설치된 종속 컴퓨터는 별도의 상자에 표시됩니다.
    - 에이전트가 설치되지 않은 컴퓨터는 포트 및 IP 주소 정보를 표시합니다.

3. 에이전트가 설치된 머신(WEBVM)의 경우, 머신 상자를 클릭하면 FQDN, 운영 체제, MAC 주소를 포함한 추가 정보를 볼 수 있습니다.

    ![그룹 종속성 보기](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. 이제 그룹에 추가할 VM(SQLVM 및 WEBVM)을 선택합니다.  Ctrl 키를 누른 상태로 클릭하면 여러 VM을 선택할 수 있습니다.
5. **그룹 만들기**를 클릭하고, 이름(smarthotelapp)을 지정합니다.

> [!NOTE]
    > 시간 범위를 확장하면 보다 세부적인 종속성을 볼 수 있습니다. 특정 기간을 선택하거나 시작 및 종료 날짜를 선택할 수 있습니다.


### <a name="run-an-assessment"></a>평가 실행


1. **그룹**  페이지에서 그룹(smarthotelapp)을 열고 **평가 만들기**를 클릭합니다.

    ![평가 만들기](./media/contoso-migration-assessment/run-vm-assessment.png)

2. 평가는 **관리** > **평가** 페이지에 나타납니다.

Contoso는 기본 평가 설정을 사용했지만 설정을 사용자 지정할 수 있습니다. [자세히 알아보기](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>VM 평가 분석

Azure Migrate 평가에는 온-프레미스 VM과 Azure의 호환성, Azure VM에 적합한 권장 크기, 예상 월별 Azure 비용에 대한 정보가 포함됩니다.

![평가 보고서](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>신뢰 등급 검토

![평가 표시](./media/contoso-migration-assessment/assessment-display.png)

평가에 별 1개부터 5개까지 신뢰 등급이 부여됩니다(별 1개가 가장 낮고 별 5개가 가장 높음).
- 신뢰 등급은 평가 계산에 필요한 데이터 요소의 가용성에 따라 평가에 할당됩니다.
- 등급은 Azure Migrate에서 제공하는 권장 크기의 신뢰성을 추정하는 데 도움이 됩니다.
- 신뢰 등급은 *성능 기반 크기 조정*을 수행할 때 유용합니다. Azure Migrate에 사용량 기반 크기 조정을 수행할 데이터 요소가 충분하지 않기 때문입니다. *온-프레미스로 크기 조정*의 경우 신뢰 등급이 항상 별 5개입니다. Azure Migrate에 VM 크기를 조정하는 데 필요한 모든 데이터 요소가 있기 때문입니다.
- 사용 가능한 데이터 요소의 백분율에 따라 평가의 신뢰 등급이 제공됩니다.

   **데이터 요소 가용성** | **신뢰 등급**
   --- | ---
   0%-20% | 별 1개
   21%-40% | 별 2개
   41%-60% | 별 3개
   61%-80% | 별 4개
   81%-100% | 별 5개

#### <a name="verify-readiness"></a>준비 상태 확인

![평가 준비 상태](./media/contoso-migration-assessment/azure-readiness.png)  

평가 보고서는 표에 요약된 정보를 보여줍니다. 성능 기반 크기 조정을 표시하려면 Azure Migrate에 다음 정보가 필요합니다. 이 정보를 수집할 수 없는 경우 평가 크기 조정이 정확하지 않을 수 있습니다.

- CPU 및 메모리 사용률 데이터.
- VM에 연결된 각 디스크의 읽기/쓰기 IOPS 및 처리량.
- VM에 연결된 각 네트워크 어댑터의 네트워크 입/출력 정보.


**설정** | **표시** | **세부 정보**
--- | --- | ---
**Azure VM 준비 상태** | VM을 마이그레이션에 사용할 수 있는지 여부를 나타냅니다. | 가능한 상태:</br><br/>- Azure 준비 완료<br/><br/>- 조건부 준비 완료 <br/><br/>- Azure를 사용할 준비 안 됨<br/><br/>- 준비 상태 알 수 없음<br/><br/> VM이 준비되지 않은 경우 수정 단계가 표시됩니다.
**Azure VM 크기** | 준비된 VM의 경우 Azure VM 권장 크기가 제공됩니다. | 권장 크기는 평가 속성에 따라 달라집니다.<br/><br/>- 성능 기반 크기 조정을 사용한 경우 크기 조정 시 VM의 성능 기록을 고려합니다.<br/><br/>- '온-프레미스'를 사용한 경우 크기 조정이 온-프레미스 VM 크기를 기반으로 하며 사용률 데이터가 사용되지 않습니다.
**추천 도구** | 컴퓨터에서 에이전트가 실행 중이므로 Azure Migrate는 컴퓨터 내부에서 실행 중인 프로세스를 확인하고 컴퓨터가 데이터베이스 컴퓨터인지 여부를 식별합니다.
**VM 정보** | 이 보고서에는 운영 체제, 부팅 유형, 디스크 및 저장소 정보를 포함하여 온-프레미스 VM의 설정이 표시됩니다.


#### <a name="review-monthly-cost-estimates"></a>월별 예상 비용 검토

이 보기는 각 컴퓨터의 세부 사항과 함께 Azure에서 VM을 실행하는 데 필요한 총 계산 및 저장소 비용을 보여줍니다.

![평가 준비 상태](./media/contoso-migration-assessment/azure-costs.png)

- 예상 비용은 컴퓨터에 권장된 크기를 사용하여 계산됩니다.
- 계산 및 저장소에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다.


## <a name="clean-up-after-assessment"></a>평가 후 정리

- 평가를 마친 후 Contoso는 향후 평가를 위해 Azure Migration 어플라이언스를 유지합니다.
- VM VMware를 끕니다. 추가 VM을 평가할 때 다시 시작합니다.
- Contoso 마이그레이션 프로젝트를 Azure에 유지합니다.  현재 미국 동부 Azure 지역의 ContosoFailoverRG 리소스 그룹에 배포되어 있습니다.
-  수집기 VM에는 180일 평가 라이선스가 있습니다. 이 기한이 만료되면 수집기를 다시 다운로드하고 설정해야 합니다.


## <a name="conclusion"></a>결론

이 시나리오에서는 Contoso가 DMA 도구를 사용하여 SmartHotel 앱 데이터베이스를 평가하고 Azure Migrate 서비스를 사용하여 온-프레미스 VM을 평가했습니다. 그런 다음, 온-프레미스 리소스를 Azure로 마이그레이션할 준비가 완료되었는지 확인하기 위해 평가를 검토했습니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서에서는 Contoso가 리프트 앤 시프트 마이그레이션을 사용하여 Azure의 SmartHotel 앱을 다시 호스트합니다. Contoso는 Azure Site Recovery를 사용하여 앱 프런트 엔드 WEBVM을 마이그레이션하고 Database Migration Service를 사용하여 Azure SQL 관리되는 인스턴스로 앱 데이터베이스를 마이그레이션합니다. 이 배포로 [시작](contoso-migration-rehost-vm-sql-managed-instance.md)하세요.
