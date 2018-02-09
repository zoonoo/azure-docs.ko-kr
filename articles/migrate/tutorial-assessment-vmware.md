---
title: "Azure Migrate를 사용하여 Azure로의 마이그레이션에 대한 온-프레미스 VMware VM 검색 및 평가 | Microsoft Docs"
description: "Azure Migrate 서비스를 사용하여 Azure로의 마이그레이션에 대한 온-프레미스 VMware VM을 검색하고 평가하는 방법에 설명합니다."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: a5019d3f729f2efbd01fca021b0089c7f99b0014
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Azure로의 마이그레이션에 대한 온-프레미스 VMware VM 검색 및 평가

[Azure Migrate](migrate-overview.md) 서비스는 Azure로의 마이그레이션에 대한 온-프레미스 워크로드를 평가합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate 프로젝트를 만듭니다.
> * 평가를 위해 온-프레미스 VMware VM을 검색하도록 온-프레미스 수집기 가상 머신(VM)을 설정합니다.
> * VM을 그룹화하고 평가를 만듭니다.


Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소

- **VMware**: 마이그레이션하려는 VM은 버전 5.5, 6.0 또는 6.5를 실행하는 vCenter Server에서 관리해야 합니다. 또한 수집기 VM을 배포하려면 5.0 이상을 실행하는 ESXi 호스트가 하나 필요합니다. 
 
> [!NOTE]
> Hyper-V는 준비 중이며 향후 곧 지원될 예정입니다. 

- **vCenter Server 계정**: vCenter Server에 액세스하려면 읽기 전용 계정이 필요합니다. Azure Migrate는 이 계정을 사용하여 온-프레미스 VM을 검색합니다.
- **사용 권한**: vCenter Server에서 .OVA 형식으로 파일을 가져와 VM을 만들려면 사용 권한이 필요합니다. 
- **통계 구성**: vCenter Server에 대한 통계 설정은 배포를 시작하기 전에 수준 3으로 설정되어야 합니다. 수준 3 평가보다 낮게 작동한다면, 저장소 및 네트워크에 대한 성능 데이터는 수집되지 않습니다. 이 경우 권장되는 크기는 CPU 및 메모리의 성능 데이터와 디스크 및 네트워크 어댑터의 구성 데이터를 기반으로 수행됩니다. 

## <a name="log-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure 포털](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. Azure Portal에서 **리소스 만들기**를 클릭합니다.
2. **Azure Migrate**를 검색하고 검색 결과에서 서비스 **Azure Migrate(미리 보기)**를 선택합니다. 그런 다음 **Create**를 클릭합니다.
3. 프로젝트에 대해 프로젝트 이름과 Azure 구독을 지정합니다.
4. 새 리소스 그룹을 만듭니다.
5. 프로젝트를 만들 위치를 지정한 다음 **만들기**를 클릭합니다. 이 미리 보기에 대해 미국 중서부 지역에서는 Azure Migrate 프로젝트만을 만들 수 있습니다. 그러나 대상 Azure 위치에 대한 마이그레이션을 계속 계획할 수 있습니다. 프로젝트에 대해 지정된 위치는 온-프레미스 VM에서 수집된 메타데이터를 저장하는 데 사용됩니다. 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>수집기 어플라이언스를 다운로드 합니다.

Azure Migrate는 수집기 어플라이언스로 알려진 온-프레미스 VM을 만듭니다. 이 VM은 온-프레미스 VMware VM을 검색하고 그것들에 대한 메타데이터를 Azure Migrate 서비스에 보냅니다. 수집기 어플라이언스를 설정하려면 OVA 파일을 다운로드하여 VM을 만들기 위해 온-프레미스 vCenter 서버로 가져옵니다.

1. Azure Migrate 프로젝트에서 **시작** > **검색 및 평가** > **컴퓨터 검색**을 클릭합니다.
2. **컴퓨터 검색**에서 **다운로드**를 클릭하여 OVA 파일을 다운로드합니다.
3. **프로젝트 자격 증명 복사**에서 프로젝트 ID 및 키를 복사합니다. 수집기를 구성할 때 이러한 항목들이 필요합니다.

    ![.ova 파일 다운로드](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>수집기 어플라이언스를 확인합니다.

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 생성된 해시는 이러한 설정에 일치해야 합니다.
    
    OVA 버전 1.0.8.49의 경우
    **알고리즘** | **해시 값**
    --- | ---
    MD5 | 8779eea842a1ac465942295c988ac0c7 
    SHA1 | c136c52a0f785e1fd98865e16479dd103704887d
    SHA256 | 5143b1144836f01dd4eaf84ff94bc1d2c53f51ad04b1ca43ade0d14a527ac3f9

    OVA 버전 1.0.8.40의 경우:

    **알고리즘** | **해시 값**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>수집기 VM을 만듭니다.

다운로드한 파일을 vCenter Server에 가져옵니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

    ![OVF 배포](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 .ova 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 수집기 VM에 대한 익숙한 이름과 VM이 호스트될 인벤토리 개체를 지정합니다.
5. **호스트/클러스터**에서 수집기 VM이 실행될 호스트 또는 클러스터를 지정합니다.
7. 저장소에서 수집기 VM에 대한 저장소 대상을 지정합니다.
8. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
9. **네트워크 매핑**에서 수집기 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure로 전송하려면 네트워크에 인터넷 연결이 필요합니다. 
10. 설정을 검토 및 확인한 다음 **마침**을 클릭합니다.

## <a name="run-the-collector-to-discover-vms"></a>VM을 검색하려면 수집기를 실행합니다.

1. vSphere Client 콘솔에서 VM > **Open Console**을 마우스 오른쪽 단추로 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호 선호도를 제공합니다.
3. 바탕 화면에서 **수집기 실행** 바로 가기를 클릭합니다.
4. Azure Migrate Collector에서 **필수 조건 설정**을 엽니다.
    - 사용 조건에 동의하고 타사 정보를 읽습니다.
    - 수집기는 VM이 인터넷에 액세스를 수 있는지 확인합니다.
    - VM이 프록시를 통해 인터넷에 액세스하는 경우 **프록시 설정**을 클릭하고, 프록시 주소 및 수신 대기 포트를 지정합니다. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.

    > [!NOTE]
    > 프록시 주소는 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 입력되어야 합니다. HTTP 프록시만 지원됩니다.

    - 수집기는 collectorservice가 실행 중인지 확인합니다. 서비스는 수집기 VM에 기본적으로 설치됩니다.
    - VMware PowerCLI를 다운로드하여 설치합니다.

5. **vCenter Server 세부 정보 지정**에서 다음을 수행합니다.
    - vCenter 서버의 이름(FQDN) 또는 IP 주소를 지정합니다.
    - **사용자 이름** 및 **암호**에서, 수집기가 vCenter 서버에서 VM을 검색하기 위해 사용할 읽기 전용 계정 자격 증명을 지정합니다.
    - **컬렉션 범위**에서 VM 검색에 대한 범위를 선택합니다. 수집기는 지정된 범위 내의 VM만 검색할 수 있습니다. 범위를 특정 폴더, 데이터 센터 또는 클러스터로 설정할 수 있습니다. VM 수는 1000개 미만이어야 합니다. 

6. **마이그레이션 프로젝트 지정**에서 Azure Migrate 프로젝트 ID를 지정하고 포털에서 복사한 키를 지정합니다. 해당 항목을 복사하지 않은 경우 수집기 VM에서 Azure Portal을 엽니다. 프로젝트 **개요** 페이지에서 **컴퓨터 검색**을 클릭하고 값을 복사합니다.  
7. **컬렉션 진행률 보기**에서 검색을 모니터링하고 VM에서 수집한 메타데이터가 범위 내에 있는지 확인합니다. 수집기는 대략적인 검색 시간을 제공합니다.

> [!NOTE]
> 수집기는 "영어(미국)"만을 운영 체제 언어와 수집기 인터페이스 언어로 지원합니다. 더 많은 언어에 대한 지원이 곧 제공될 예정입니다.


### <a name="verify-vms-in-the-portal"></a>포털에서 VM을 확인합니다.

검색 시간은 검색 중인 VM이 얼마나 많은지에 달려 있습니다. 일반적으로 VM 100대의 경우, 수집기가 실행을 완료한 후 검색을 완료하기까지 약 1시간이 걸립니다. 

1. Migration Planner 프로젝트에서 **관리** > **컴퓨터**를 클릭합니다.
2. 검색하려는 VM이 포털에 나타나는지 확인합니다.


## <a name="create-and-view-an-assessment"></a>평가 만들기 및 보기

VM을 검색한 후 그룹화하여 평가를 만듭니다. 

1. 프로젝트 **개요** 페이지에서 **+평가 만들기**를 클릭합니다.
2. 평가 속성을 검토하려면 **모두 보기**를 클릭합니다.
3. 그룹을 만들고 그룹 이름을 지정합니다.
4. 그룹에 추가하려는 컴퓨터를 선택합니다.
5. **평가 만들기**를 클릭하고, 그룹 및 평가를 만듭니다.
6. 평가를 만든 후 **개요** > **대시보드**에서 봅니다.
7. **평가 내보내기**를 클릭하고, Excel 파일로 다운로드합니다.

### <a name="sample-assessment"></a>샘플 평가

예제 평가 보고서는 다음과 같습니다. VM이 Azure에 대해 호환되는지 여부 및 예상되는 월별 비용에 관한 정보가 포함되어 있습니다. 

![평가 보고서](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 준비 상태

이 보기는 각 컴퓨터에 대한 준비 상태를 보여줍니다.

- 준비된 VM의 경우 Azure Migrate는 Azure에서의 VM 크기를 권장합니다.
- 준비되지 않은 VM의 경우 Azure Migrate 는 그 이유를 설명하고 수정 단계를 제공합니다.
- Azure Migrate는 마이그레이션에 사용할 수 있는 도구를 제안합니다. 컴퓨터가 신속한 마이그레이션 전환에 적합한 경우 Azure Site Recovery 서비스를 사용하는 것이 좋습니다. 데이터베이스 컴퓨터인 경우 Azure Database Migration Service를 사용하는 것이 좋습니다.

  ![평가 준비 상태](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>월별 예상 비용

이 보기는 각 컴퓨터의 세부 사항과 함께 Azure에서 VM을 실행하는 데 필요한 총 계산 및 저장소 비용을 보여 줍니다. 예상 비용은 컴퓨터 및 해당 디스크에 대한 성능 기반 권장 크기 사항과 평가 속성을 사용하여 계산됩니다. 

> [!NOTE]
> Azure Migrate가 제공하는 비용 예측은 온-프레미스 VM을 Azure IaaS(Infrastructure as a service) VM으로 실행하기 위한 것입니다. Azure Migrate는 PaaS(Platform as a Service) 또는 SaaS(Software as a Service) 비용을 고려하지 않습니다. 

계산 및 저장소에 대한 월별 예상 비용은 그룹의 모든 VM에 대해 집계됩니다. 

![VM 비용 평가](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

드릴다운하여 특정 컴퓨터에 대한 세부 정보를 확인할 수 있습니다.

![VM 비용 평가](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>다음 단계

- 대규모 VMware 환경을 검색하고 평가하는 방법을 [알아봅니다](how-to-scale-assessment.md).
- [컴퓨터 종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 정확도 높은 평가 그룹을 만드는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
