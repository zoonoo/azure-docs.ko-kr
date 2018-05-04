---
title: DMA 및 Azure Migrate를 사용하여 Azure로 마이그레이션하기 위한 온-프레미스 워크로드 평가 | Microsoft Docs
description: DMA(Data Migration Assistant) 및 Azure Migrate 서비스를 사용하여 온-프레미스 컴퓨터를 마이그레이션할 수 있도록 Azure를 준비하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 33e31c47a6125ac363410a9a78e9c9310c74d51e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="scenario-1-assess-on-premises-workloads-for-migration-to-azure"></a>시나리오 1: Azure로 마이그레이션하기 위한 온-프레미스 워크로드 평가

Azure로 마이그레이션을 고려 중인 Contoso는 기술 및 재무 평가를 통해 온-프레미스 워크로드가 클라우드로 마이그레이션하기에 적합한지 알아보려 합니다. 특히, 컴퓨터 및 데이터베이스의 마이그레이션 호환성을 평가하고, Azure에서 리소스를 실행하기 위해 필요한 용량과 비용을 예측해 보려 합니다.

마이그레이션을 시작하고 관련 기술을 정확하게 이해하기 위해, Contoso는 소규모 온-프레미스 여행 앱을 평가하고 마이그레이션하기로 합니다. 이 앱은 웹앱이 한 VM에서 실행되고 SQL Server 데이터베이스가 두 번째 VM에서 실행되는 2계층 앱입니다. 응용 프로그램은 VMware에 배포되고, 환경은 vCenter Server를 통해 관리됩니다. Contoso는 DMA(Data Migration Assistant) 및 Azure Migrate 서비스를 사용하여 평가를 수행할 것입니다.

**Technology** | **설명** | **비용**
--- | --- | ---
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA는 Azure에서 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 평가 및 검색합니다. 또한 SQL Server 원본과 대상 간의 기능 패리티를 평가하고, 대상 환경의 성능 및 안정성을 향상하기 위한 방안을 제시합니다. | DMA는 무료로 다운로드할 수 있는 도구입니다. 
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | 이 서비스를 사용하면 Azure로 마이그레이션하기 위한 온-프레미스 컴퓨터를 간편하게 평가할 수 있습니다. 이 서비스는 컴퓨터의 마이그레이션 적합성을 평가하고, Azure에서 실행하기 위한 크기 및 비용 추정치를 제공합니다. 현재 Azure Migrate Azure로 마이그레이션하기 위한 온-프레미스 VMware VM을 평가할 수 있습니다. | 이 서비스는 현재(2018년 4월) 무료로 제공됩니다.
[서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate는 서비스 맵을 사용하여 마이그레이션하려는 컴퓨터 간의 종속성을 보여줍니다. |  서비스 맵은 Azure Log Analytics에 포함되어 있습니다. 현재 180일 동안 무료로 사용할 수 있습니다. 

이 시나리오에서는 DMA를 다운로드 및 실행하여 여행 앱에 사용할 온-프레미스 SQL Server 데이터베이스를 평가할 것입니다. Azure Migrate와 종속성 매핑을 사용하여 앱 VM을 평가한 후 Azure로 마이그레이션할 것입니다.

> [!NOTE]
> 이 시나리오에서 데이터베이스의 평가 대상은 "Azure VM의 SQL Server"입니다. 그러나 그 다음 시나리오 문서에서는 Azure SQL 관리되는 인스턴스로 마이그레이션을 실행할 것입니다. 이 방법을 사용하는 이유는 DMA가 현재 Azure SQL 관리되는 인스턴스 대상 평가를 지원하지 않기 때문입니다.

## <a name="architecture"></a>아키텍처

이 시나리오에서는 

 ![마이그레이션 평가 아키텍처를 설치합니다.](./media/migrate-scenarios-assessment/migration-assessment-architecture.png)

이 시나리오에서는
- Contoso는 온-프레미스 데이터 센터(**contoso-datacenter**)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.
- 내부 여행 앱은 두 VM **WEBVM** 및 **SQLVM**에 계층화되어 있으며, VMware ESXi 호스트 **contosohost1.contoso.com**에 있습니다.
- VMware 환경은 VM에서 실행되는 VCenter Server(**vcenter.contoso.com**)를 통해 관리됩니다.




## <a name="prerequisites"></a>필수 조건

이 시나리오를 배포하려면 다음과 같은 것들이 필요합니다.

- 5.5, 6.0 또는 6.5 버전을 실행하는 온-프레미스 vCenter 서버.
- VCenter 서버의 읽기 전용 계정 또는 이러한 계정을 만들 수 있는 권한. 
- .OVA 템플릿을 사용하여 VCenter 서버에 VM을 만들 수 있는 권한.
- 5.0 이상을 실행하는 ESXi 호스트 하나 이상.
- 온-프레미스 VMware VM 두 대 이상(그 중 하나는 SQL Server 데이터베이스 실행).
- 각 VM에 Azure Migrate 에이전트를 설치할 권한이 있어야 합니다.
- VM이 인터넷에 직접 연결되어야 합니다.
        - 인터넷 액세스를 [필요한 URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites)로 제한할 수 있습니다.
        - 인터넷에 연결되지 않은 컴퓨터가 있으면 해당 컴퓨터에 [OMS 게이트웨이](../log-analytics/log-analytics-oms-gateway.md)를 다운로드하여 설치해야 합니다.
- SQL Server 인스턴스를 실행하는 VM의 FQDN(데이터베이스 평가용).
- SQL Server VM에서 실행되는 Windows 방화벽이 TCP 포트 1433(기본 포트)에서 외부 연결을 허용해야만 DMA가 연결할 수 있습니다.


## <a name="scenario-overview"></a>시나리오 개요

우리가 할 일은 다음과 같습니다.


> [!div class="checklist"]
> * **1단계: Azure 준비**. Azure 구독만 있으면 됩니다.
> * **2단계: DMA 다운로드 및 설치**: 온-프레미스 SQL Server 데이터베이스를 평가할 DMA를 준비합니다.
> * **3단계: 데이터베이스 평가**: 데이터베이스 평가를 실행하고 분석합니다.
> * **4단계: Azure Migrate를 사용하여 VM 평가 준비**: 온-프레미스 계정을 설정하고 VMware 설정을 조정합니다.
> * **5단계: 온-프레미스 VM 검색**: Azure Migrate 수집기 VM을 만듭니다. 그런 다음, 수집기를 실행하여 평가할 VM을 검색합니다.
> * **6단계: 종속성 분석 준비**: VM 간의 종속성 매핑을 볼 수 있도록 VM에 Azure Migrate 에이전트를 설치합니다.
> * **7단계: VM 평가**: 종속성을 확인하고, VM을 그룹화하고, 평가를 실행합니다. 평가 준비가 완료되면 마이그레이션을 위한 준비 과정으로 평가를 분석합니다.


## <a name="step-1-prepare-azure"></a>1단계: Azure 준비

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

- 무료 계정을 만드는 경우 구독 관리자로서 모든 작업을 수행할 수 있습니다.
- 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 구독 또는 이 시나리오에 사용하는 리소스 그룹의 소유자 또는 참가자 권한을 할당받아야 합니다.


## <a name="step-2-download-and-install-the-dma"></a>2단계: DMA 다운로드 및 설치

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 DMA를 다운로드합니다.
    - SQL 인스턴스에 연결할 수 있는 모든 컴퓨터에 DMA를 설치할 수 있습니다. 반드시 SQL Server 컴퓨터에서 DMA를 실행할 필요는 없습니다.
    - SQL Server 호스트 컴퓨터에서 DMA를 실행하면 안 됩니다.
2. 다운로드한 설치 파일(DownloadMigrationAssistant.msi)을 두 번 클릭하여 설치를 시작합니다.
3. **마침** 페이지에서 **Microsoft Data Migration Assistant 시작**이 선택되었는지 확인하고 **마침**을 클릭합니다.

## <a name="step-3-run-and-analyze-the-database-assessment"></a>3단계: 데이터베이스 평가를 실행 및 분석

지정된 대상에 대해 원본 SQL Server 인스턴스를 분석하는 평가를 실행합니다.

1. **새로 만들기**에서 **평가**를 선택하고 평가에 프로젝트 이름을 지정합니다.
2. **원본 서버 유형**에서 **SQL Server**를 선택합니다. **대상 서버 유형**에서 **Azure Virtual Machines의 SQL Server**를 선택합니다.

    ![원본 선택](./media/migrate-scenarios-assessment/dma-assessment-1.png)

    > [!NOTE]
      현재 DMA는 SQL 관리되는 인스턴스로의 마이그레이션 평가를 지원하지 않습니다. 이 문제를 해결하기 위해 Azure VM의 SQL Server를 평가의 대상으로 사용합니다.

1.  **대상 버전 선택**에서는 Azure에서 실행하려는 SQL Server 대상 버전, 그리고 평가에서 검색할 항목을 지정합니다.
    - **호환성 문제**는 마이그레이션을 중단시킬 수 있는 변경 내용 또는 마이그레이션 전에 사소한 조정이 필요한 변경 내용에 대해 알려줍니다. 사용자가 현재 사용 중이지만 더 이상 사용되지 않은 기능에 대해서도 알려줍니다. 문제는 호환성 수준에 따라 구성됩니다. 
    - **새 권장 기능**은 마이그레이션 후 데이터베이스에 사용할 수 있는 대상 SQL Server 플랫폼의 새 기능에 대해 알려줍니다. 이러한 권장 기능은 성능, 보안 및 저장소를 기준으로 구성됩니다. 

    ![대상 선택](./media/migrate-scenarios-assessment/dma-assessment-2.png)

2. **서버에 연결**에서는 SQL Server 인스턴스를 실행하는 컴퓨터 이름, 인증 유형 및 연결 정보를 지정합니다. 그런 다음 **연결**을 클릭합니다.

    ![대상 선택](./media/migrate-scenarios-assessment/dma-assessment-3.png)
    
3. **원본 추가**에서는 평가할 데이터베이스를 선택하고 **추가**를 클릭합니다.
4. 지정한 이름을 사용하는 평가가 만들어집니다.

    ![평가 만들기](./media/migrate-scenarios-assessment/dma-assessment-4.png)

5.  **다음으로** 평가를 시작합니다.
6. **결과 검토**에서는 설정한 평가 테스트의 결과가 표시됩니다.


### <a name="analyze-the-database-assessment"></a>데이터베이스 평가 분석

결과를 사용할 수 있게 되는 즉시 DMA에 결과가 표시됩니다. 

1. **호환성 문제** 보고서에서는 데이터베이스에 각 호환성 수준의 문제가 있는지 확인하고, 문제가 있으면 해결 방법도 확인합니다. 호환성 수준은 다음과 같이 SQL Server 버전에 매핑됩니다.
    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![호환성 문제](./media/migrate-scenarios-assessment/dma-assessment-5.png)

2. **권장 기능** 보고서에는 평가를 마친 결과, 마이그레이션 후 구성하면 좋을 것으로 판명된 성능, 보안 및 저장소 기능을 확인합니다. 메모리 내 OLTP 및 Columnstore, Stretch Database, Always Encrypted, 동적 데이터 마스킹, 투명한 데이터 암호화를 포함하여 다양한 권장 기능이 있습니다.

    ![권장 기능](./media/migrate-scenarios-assessment/dma-assessment-6.png)

3. 문제를 해결한 후에는 **평가 다시 시작**을 클릭하여 평가를 다시 실행합니다. 
4. 평가 보고서를 JSON 또는 CSV 형식으로 받아 보려면 **보고서 내보내기**를 클릭합니다.

규모가 큰 평가를 실행하는 경우:

- 여러 평가를 동시에 실행하고 **모든 평가** 페이지를 열어서 평가 상태를 볼 수 있습니다.
- [평가를 SQL Server 데이터베이스에 통합](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database)할 수 있습니다.
- [평가를 PowerBI 보고서에 통합](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)할 수 있습니다.


## <a name="step-4-prepare-for-vm-assessment-with-azure-migrate"></a>4단계: Azure Migrate를 사용하여 VM 평가 준비

Azure Migrate가 평가할 VM을 자동으로 검색하는 데 사용할 VM 계정을 만들고, VM을 만들 권한이 있는지 확인하고, 열어야 하는 포트를 메모하고, 통계 설정 수준을 설정합니다.

### <a name="set-up-a-vmware-account"></a>VMware 계정 설정

 VCenter에서 읽기 전용 계정이 필요합니다. 계정이 없으면 다음 속성으로 VMware 계정을 만듭니다.

- 사용자 유형: 읽기 전용 사용자(최소).
- 권한: 데이터 센터 개체 –> 자식 개체에 전파, role=Read-only.
- 세부 정보: 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.
- 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

### <a name="verify-permissions-to-create-a-vm"></a>VM을 만들 권한이 있는지 확인

.OVA 형식으로 파일을 가져와 VM을 만들 수 있는 권한이 있는지 확인합니다. [자세히 알아보기](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>포트 확인

이 시나리오에서는 종속성 매핑을 구성할 것입니다. 이 기능을 사용하려면 평가하려는 VM에 에이전트를 설치해야 합니다. 해당 에이전트는 각 VM의 TCP 포트 443에서 Azure에 연결할 수 있어야 합니다. 연결 요구 사항에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid).


### <a name="set-statistics-settings"></a>통계 설정 지정

배포를 시작하기 전에 vCenter Server에 대한 통계 설정 수준을 3으로 설정해야 합니다. 다음 사항에 유의하세요.
- 수준을 설정한 후 평가를 실행하려면 하루 이상 기다려야 합니다. 그렇지 않으면 예상대로 작동하지 않을 수 있습니다.
- 수준이 3보다 높으면 평가가 작동하지는 하지만 다음과 같은 문제가 있습니다.
    - 디스크 및 네트워킹에 대한 성능 데이터가 수집되지 않습니다.
    - 저장소의 경우 Azure Migrate는 Azure에서 온-프레미스 디스크와 크기가 같은 표준 디스크를 권장합니다.
    - 네트워킹의 각 온-프레미스 네트워크 어댑터의 경우 Azure에서 네트워크 어댑터를 권장합니다.
    - 계산의 경우 Azure Migrate는 VM 코어 및 메모리 크기를 살펴본 후 동일한 구성의 Azure VM을 권장합니다. 적합한 Azure VM 크기가 여러 개인 경우 비용이 가장 낮은 크기가 권장됩니다.
   
    
수준 3의 크기 조정에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

다음과 같이 수준을 설정합니다.

1. VSphere 웹 클라이언트에서 vCenter 서버 인스턴스를 엽니다.
2. **관리** 탭을 선택하고, **설정** 아래에서 **일반**을 클릭합니다.
3. **편집**을 클릭하고, **통계**에서 통계 수준 설정을 **수준 3**으로 설정합니다.

    ![vCenter 통계 수준](./media/migrate-scenarios-assessment/vcenter-statistics-level.png)



## <a name="step-5-discover-vms"></a>5단계: VM 검색

Azure Migrate 프로젝트를 만들고, 수집기 VM을 다운로드 및 설치합니다. 그런 다음, 수집기를 실행하여 VM을 검색합니다.

### <a name="create-a-project"></a>프로젝트 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하여 **리소스 만들기**를 클릭합니다.
2. **Azure Migrate**를 검색합니다. 검색 결과에서 **Azure Migrate**를 선택하고 **만들기**를 클릭합니다.
3. 프로젝트 이름 및 Azure 구독을 지정합니다.
4. 새 리소스 그룹을 만듭니다.
5. 프로젝트 위치를 지정한 다음, **만들기**를 클릭합니다.

    - Azure Migrate 프로젝트는 미국 중서부 또는 미국 동부 지역에서만 만들 수 있습니다.
    - 모든 대상 위치에 대해 마이그레이션을 계획할 수 있습니다.
    - 프로젝트 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다.

    ![Azure Migrate](./media/migrate-scenarios-assessment/project-1.png)


    

### <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

Azure Migrate는 수집기 어플라이언스로 알려진 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 그것들에 대한 메타데이터를 Azure Migrate 서비스에 보냅니다. 수집기 어플라이언스를 설정하려면 OVA 파일을 다운로드하여 VM을 만들기 위해 온-프레미스 vCenter 서버로 가져옵니다.

1. Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 클릭합니다.
2. **컴퓨터 검색**에서 **다운로드**를 클릭하여 OVA 파일을 다운로드합니다.
3. **프로젝트 자격 증명 복사**에서 프로젝트 ID 및 키를 복사합니다. 수집기를 구성할 때 이러한 항목들이 필요합니다.

    ![.ova 파일 다운로드](./media/migrate-scenarios-assessment/download-ova.png) 

### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 이러한 설정과 일치해야 합니다(버전 1.0.9.7).
    
    **알고리즘** | **해시 값**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c
    

### <a name="create-the-collector-appliance"></a>수집기 어플라이언스 만들기

다운로드한 파일을 vCenter Server에 가져옵니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/migrate-scenarios-assessment/vcenter-wizard.png) 

2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정하고 **다음**을 클릭합니다.
3. **OVF 템플릿 세부 정보**에서 **다음**을 클릭합니다. **최종 사용자 사용권 계약**에서 **동의**를 클릭하여 사용권 계약에 동의하고 **다음**을 클릭합니다.
4. **이름 및 위치**에서 수집기 VM의 표시 이름과 VM이 호스트될 인벤토리 위치를 지정하고 **다음**을 클릭합니다. 수집기 어플라이언스가 실행될 호스트 또는 클러스터를 지정합니다.
5. **저장소**에서 어플라이언스에 대한 파일을 저장할 위치를 지정하고 **다음**을 클릭합니다.
6. **디스크 형식**에서 저장소를 프로비전할 방법을 지정합니다.
7. **네트워크 매핑**에서 수집기 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다. 
8. **완료 준비**에서 설정을 검토하고, **배포 후 전원 켜기**를 선택한 다음, **마침**을 클릭합니다.

어플라이언스가 만들어진 후 완료 확인 메시지가 표시됩니다.

### <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

시작하기 전에 알아둘 사항으로, 수집기는 운영 체제 언어 및 수집기 인터페이스 언어로 "영어(미국)"만 지원합니다.

1. vSphere Client 콘솔에서 VM > **Open Console**을 마우스 오른쪽 단추로 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 기본 암호를 제공합니다.
3. 바탕 화면에서 **수집기 실행** 바로 가기를 클릭합니다.

    ![수집기 바로 가기](./media/migrate-scenarios-assessment/collector-shortcut.png) 
    
4. Azure Migrate Collector에서 **필수 조건 설정**을 엽니다.
    - 사용 조건에 동의하고 타사 정보를 읽습니다.
    - 수집기는 VM이 인터넷에 액세스할 수 있는지, 시간이 동기화되는지, 수집기 서비스가 실행 중인지(기본적으로 VM에 설치됨) 확인합니다. 또한 VMWare PowerCLI를 설치합니다. 
    
    > [!NOTE]
    > 여기서는 VM이 프록시 없이 인터넷에 직접 액세스할 수 있다고 가정합니다.

    ![필수 조건 확인](./media/migrate-scenarios-assessment/collector-verify-prereqs.png)
    

5. **vCenter Server 세부 정보 지정**에서 다음을 수행합니다.
    - vCenter 서버의 이름(FQDN) 또는 IP 주소를 지정합니다.
    - **사용자 이름** 및 **암호**에서, 수집기가 vCenter 서버에서 VM을 검색하기 위해 사용할 읽기 전용 계정 자격 증명을 지정합니다.
    - **범위 선택**에서 VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내의 VM만 검색할 수 있습니다. 범위를 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM은 1500대 미만이어야 합니다. 

    ![VCenter에 연결](./media/migrate-scenarios-assessment/collector-connect-vcenter.png)

6. **마이그레이션 프로젝트 지정**에서 Azure Migrate 프로젝트 ID를 지정하고 포털에서 복사한 키를 지정합니다. 해당 항목을 복사하지 않은 경우 수집기 VM에서 Azure Portal을 엽니다. 프로젝트 **개요** 페이지에서 **컴퓨터 검색**을 클릭하고 값을 복사합니다.  

    ![Azure에 연결](./media/migrate-scenarios-assessment/collector-connect-azure.png)

7. **컬렉션 진행률 보기**에서 검색을 모니터링하고 VM에서 수집한 메타데이터가 범위 내에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다.

    ![수집 진행 중](./media/migrate-scenarios-assessment/collector-collection-process.png)
   


### <a name="verify-vms-in-the-portal"></a>포털에서 VM을 확인합니다.

수집이 완료되면 포털에 VM이 표시되는지 확인합니다.

1. Azure Migrate 프로젝트에서 **관리** > **컴퓨터**를 클릭합니다.
2. 검색하려는 VM이 페이지에 나타나는지 확인합니다.

    ![검색된 컴퓨터](./media/migrate-scenarios-assessment/discovery-complete.png)

3. 현재 컴퓨터에 Azure Migrate 에이전트가 설치되지 않았습니다. 종속성을 보려면 에이전트를 설치해야 합니다.
    
    ![검색된 컴퓨터](./media/migrate-scenarios-assessment/machines-no-agent.png)



## <a name="step-6-prepare-for-dependency-analysis"></a>6단계: 종속성 분석 준비

평가하려는 VM 간의 종속성을 보기 위해 웹앱 VM(WEBVM 및 SQLVM)에 에이전트를 다운로드하여 설치합니다.

### <a name="take-a-snapshot"></a>스냅숏 만들기

VM을 수정하기 전에 복사본을 만들려면 에이전트를 설치하기 전에 스냅숏을 만듭니다.

![컴퓨터 스냅숏](./media/migrate-scenarios-assessment/snapshot-vm.png) 


### <a name="download-and-install-the-vm-agents"></a>VM 에이전트 다운로드 및 설치

1.  Azure Migrate 프로젝트 > **컴퓨터** 페이지에서 컴퓨터를 선택하고, **종속성** 열에서 **설치 필요**를 클릭합니다.
2.  **컴퓨터 검색** 페이지에서 각 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 다운로드하여 설치합니다.
3.  작업 영역 ID와 키를 복사합니다. MMA를 설치할 때 필요합니다.

    ![에이전트 다운로드](./media/migrate-scenarios-assessment/download-agents.png) 



#### <a name="install-the-mma"></a>MMA 설치

1. 다운로드한 에이전트를 두 번 클릭합니다.
2. **Welcome** 페이지에서 **다음**을 클릭합니다. **사용 조건** 페이지에서 **동의함**을 클릭하여 라이선스에 동의합니다.
3. **대상 폴더**에서 기본 설치 폴더를 유지하고 **다음**을 클릭합니다. 
4. **에이전트 설치 옵션**에서 **Azure Log Analytics에 에이전트 연결** > **다음**을 선택합니다. 

    ![MMA 설치](./media/migrate-scenarios-assessment/mma-install.png) 
5. 포털에서 복사한 작업 영역 ID와 키를 **Azure Log Analytics**에 붙여넣습니다. **다음**을 클릭합니다.
    ![MMA 설치](./media/migrate-scenarios-assessment/mma-install2.png) 

6. **설치 준비 완료**에서 MMA를 설치합니다.



#### <a name="install-the-dependency-agent"></a>종속성 에이전트 설치

1.  다운로드한 종속성 에이전트를 두 번 클릭합니다.
2.  **사용 조건** 페이지에서 **사용 조건에 동의함**을 클릭합니다.
3.  **설치**에서 설치가 완료될 때까지 기다립니다. 그런 후 **Next** 를 클릭합니다.

    ![종속성 에이전트](./media/migrate-scenarios-assessment/dependency-agent.png) 


       
## <a name="step-7-run-and-analyze-the-vm-assessment"></a>7단계: VM 평가를 실행 및 분석

컴퓨터 종속성을 확인하고 그룹을 만듭니다. 그런 다음, 평가를 실행합니다.

### <a name="verify-dependencies-and-create-a-group"></a>종속성을 확인하고 그룹을 만듭니다.

1.  **컴퓨터** 페이지에서 분석하려는 VM의 **종속성 보기**를 클릭합니다.

    ![컴퓨터 종속성 보기](./media/migrate-scenarios-assessment/view-machine-dependencies.png) 

2. SQLVM의 경우 종속성 맵에 다음과 같은 세부 정보가 표시됩니다.

    - 지정된 시간(기본적으로 1시간) 동안 SQLVM에서 실행되는 활성 네트워크 연결이 있는 프로세스 그룹/프로세스
    - 모든 종속 컴퓨터와 주고 받는 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결.
    - Azure Migrate 에이전트가 설치된 종속 컴퓨터는 별도의 상자에 표시됩니다.
    - 에이전트가 설치되지 않은 컴퓨터는 포트 및 IP 주소 정보를 표시합니다.
    
 3. 에이전트가 설치된 컴퓨터(WEBVM)의 경우 컴퓨터 상자를 클릭하면 FQDN, 운영 체제, MAC 주소를 포함한 추가 정보를 볼 수 있습니다. 

    ![그룹 종속성 보기](./media/migrate-scenarios-assessment/sqlvm-dependencies.png)

4. 이제 그룹에 추가할 VM(SQLVM 및 WEBVM)을 선택합니다.  Ctrl 키를 누른 상태로 클릭하면 여러 VM을 선택할 수 있습니다.
5. **그룹 만들기**를 클릭하고, 이름(smarthotelapp)을 지정합니다.

> [!NOTE]
    > 시간 범위를 확장하면 보다 세부적인 종속성을 볼 수 있습니다. 특정 기간을 선택하거나 시작 및 종료 날짜를 선택할 수 있습니다. 


### <a name="run-an-assessment"></a>평가 실행


1. **그룹** 페이지에서 그룹(smarthotelapp)을 엽니다.
2. **평가 만들기**를 클릭합니다.

    ![평가 만들기](./media/migrate-scenarios-assessment/run-vm-assessment.png)

3. 평가는 **관리** > **평가** 페이지에 나타납니다.


### <a name="modify-assessment-settings"></a>평가 설정 수정

이 자습서에서는 기본 평가 설정을 사용했지만, 다음과 같이 설정을 사용자 지정할 수 있습니다.

1. 마이그레이션 프로젝트의 **평가** 페이지에서 평가를 선택하고 **속성 편집**을 클릭합니다.
2. 다음 표에 따라 속성을 수정합니다.

    **설정** | **세부 정보** | **기본값**
    --- | --- | ---
    **대상 위치** | 마이그레이션할 Azure 위치입니다. | 기본값은 없습니다.
    **저장소 이중화** | 마이그레이션 후 Azure VM이 사용하게 될 저장소 중복 유형입니다. | 기본값은 [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md)입니다. Azure Migrate는 관리 디스크 기반 평가만 지원하고 관리 디스크는 LRS만 지원하므로 LRS 옵션을 사용합니다. 
    **크기 조정 기준** | Azure Migrate가 Azure에 사용할 VM의 적정 크기를 산정하는 데 사용되는 기준입니다. *성능 기반* 크기 조정을 수행하거나, 성능 기록을 고려하지 않고 *온-프레미스로* VM 크기를 조정할 수 있습니다. | 기본 옵션은 성능 기반 크기 조정입니다.
    **성능 기록** | VM의 성능을 평가하는 데 고려할 기간입니다. 이 속성은 크기 조정 기준이 *성능 기반 크기 조정*인 경우에만 적용됩니다. | 기본값은 1일입니다.
    **백분위 수 사용률** | 적정 크기를 산정하는 데 고려되는 성능 샘플 집합의 백분위수 값입니다. 이 속성은 크기 조정 기준이 *성능 기반 크기 조정*인 경우에만 적용됩니다.  | 기본값은 95번째 백분위수입니다.
    **가격 책정 계층** | 대상 Azure VM의 [가격 책정 계층(기본/표준)](../virtual-machines/windows/sizes-general.md)을 지정할 수 있습니다. 예를 들어 프로덕션 환경을 마이그레이션할 계획이라면 VM 대기 시간이 짧은 대신 더 많은 비용이 발생할 수 있는 표준 계층을 고려하는 것이 좋습니다. 반면, 개발-테스트 환경을 사용하는 경우 VM 대기 시간이 긴 대신 비용이 저렴한 기본 계층을 고려하는 것이 좋습니다. | 기본적으로 [표준](../virtual-machines/windows/sizes-general.md) 계층이 사용됩니다.
    **쾌적 인자** | Azure Migrate는 평가 중에 버퍼(쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다. | 기본 설정은 1.3x입니다.
    **제안** | 등록된 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)가 기본값입니다.
    **통화** | 청구 통화입니다. | 기본값은 미국 달러입니다.
    **할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. | 기본 설정은 0%입니다.
    **Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 받을 수 있는지 여부를 지정합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. | 기본값은 [예]입니다.

3. **저장**을 클릭하여 평가 설정을 업데이트합니다.


### <a name="analyze-the-vm-assessment"></a>VM 평가 분석

Azure Migrate 평가에는 온-프레미스 VM과 Azure의 호환성, Azure VM에 적합한 권장 크기, 예상 월별 Azure 비용에 대한 정보가 포함됩니다. 

![평가 보고서](./media/migrate-scenarios-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>신뢰 등급 검토

![평가 표시](./media/migrate-scenarios-assessment/assessment-display.png)

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

![평가 준비 상태](./media/migrate-scenarios-assessment/azure-readiness.png)  

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

![평가 준비 상태](./media/migrate-scenarios-assessment/azure-costs.png) 

- 예상 비용은 컴퓨터에 권장된 크기를 사용하여 계산됩니다.
- 계산 및 저장소에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다. 


## <a name="conclusion"></a>결론

이 시나리오에서는 다음과 같은 일을 했습니다.

> [!div class="checklist"]
> * DMA 도구를 사용하여 온-프레미스 데이터베이스를 평가했습니다.
> * Azure Migrate 서비스와 함께 종속성 매핑을 사용하여 온-프레미스 VM을 평가했습니다.
> * 온-프레미스 리소스를 Azure로 마이그레이션할 준비가 완료되었는지 확인하기 위해 평가를 검토했습니다.

## <a name="next-steps"></a>다음 단계

그 다음 시나리오를 계속 진행하여 온-프레미스 VM을 Azure로 리프트 앤 시프트 마이그레이션합니다.



