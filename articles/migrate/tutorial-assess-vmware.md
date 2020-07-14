---
title: Azure Migrate 서버 평가를 사용하여 VMware VM 평가
description: Azure Migrate Server Assessment를 사용하여 Azure로 마이그레이션할 온-프레미스 VMware VM을 평가하는 방법을 설명합니다.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 6c395d7e2be151e97808fa9601ff6001801d243b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110354"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>서버 평가를 사용하여 VMware VM 평가

이 문서에서는 [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용하여 온-프레미스 VMware VM(가상 머신)을 평가하는 방법을 보여 줍니다.


이 자습서는 VMware VM을 평가하고 Azure로 마이그레이션하는 방법을 보여 주는 시리즈의 두 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure Migrate 프로젝트를 설정합니다.
> * 온-프레미스에서 실행되는 Azure Migrate 어플라이언스에서 VM을 평가하도록 설정합니다.
> * 온-프레미스 VM의 연속 검색을 시작합니다. 어플라이언스는 검색된 VM의 구성 및 성능 데이터를 Azure로 보냅니다.
> * 검색된 VM을 그룹화하고 해당 VM 그룹을 평가합니다.
> * 평가를 검토합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 방법 문서를 검토하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- 이 시리즈의 [첫 번째 자습서를 완료](tutorial-prepare-vmware.md)합니다. 그렇지 않으면 이 자습서의 지침이 작동하지 않습니다.
- 첫 번째 자습서에서 수행해야 하는 작업은 다음과 같습니다.
    - Azure Migrate를 사용할 수 있도록 [Azure를 준비](tutorial-prepare-vmware.md#prepare-azure)합니다.
    - [평가할 VMware를 준비](tutorial-prepare-vmware.md#prepare-for-assessment)합니다. 여기에는 VMware 설정 확인, Azure Migrate에서 vCenter Server에 액세스하는 데 사용할 수 있는 계정 설정이 포함됩니다.
    - VMware를 평가할 Azure Migrate 어플라이언스를 배포하는 데 필요한 항목을 [확인](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment)합니다.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate 프로젝트 설정

새 Azure Migrate 프로젝트를 설정하는 방법은 다음과 같습니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**의 **서버 검색, 평가 및 마이그레이션** 아래에서 **서버 평가 및 마이그레이션**을 선택합니다.

   ![서버 평가 및 마이그레이션 단추](./media/tutorial-assess-vmware/assess-migrate.png)

4. **시작**에서**도구 추가**를 선택합니다.
5. **프로젝트 마이그레이션**에서 Azure 구독을 선택하고, 아직 없는 경우 리소스 그룹을 만듭니다.     
6. **프로젝트 세부 정보**에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

   ![프로젝트 이름 및 지역 상자](./media/tutorial-assess-vmware/migrate-project.png)

7. **다음**을 선택합니다.
8. **평가 도구 선택**에서 **Azure Migrate: 서버 평가** > **다음**을 차례로 선택합니다.

   ![서버 평가 도구 선택](./media/tutorial-assess-vmware/assessment-tool.png)

9. **마이그레이션 도구 선택**에서 **마이그레이션 도구 추가 건너뛰기** > **다음**을 차례로 선택합니다.
10. **검토 + 도구 추가**에서 설정을 검토하고, **도구 추가**를 선택합니다.
11. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. 프로젝트 페이지로 이동합니다. 프로젝트가 표시되지 않으면 Azure Migrate 대시보드의 **서버**에서 액세스할 수 있습니다.

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 설정

Azure Migrate:Server Assessment는 간단한 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 VM 검색을 수행하고, VM 메타데이터와 성능 데이터를 Azure Migrate에 보냅니다. 어플라이언스는 여러 가지 방법으로 설정할 수 있습니다.

- 다운로드한 OVA 템플릿을 사용하여 VMware VM에 설정합니다. 이는 이 자습서에서 사용하는 방법입니다.
- PowerShell 설치 관리자 스크립트를 사용하여 VMware VM 또는 물리적 머신에 설정합니다. OVA 템플릿을 사용하여 VM을 설정할 수 없거나 Azure Government에 있는 경우 [이 방법](deploy-appliance-script.md)을 사용해야 합니다.

어플라이언스를 만든 후 Azure Migrate:Server Assessment에 연결하여, 처음으로 구성하고, Azure Migrate 프로젝트에 등록할 수 있는지 확인합니다.


### <a name="download-the-ova-template"></a>OVA 템플릿 다운로드

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 선택합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, VMWare vSphere 하이퍼바이저 사용**을 선택합니다.
3. **다운로드**를 선택하여 OVA 템플릿 파일을 다운로드합니다.

   ![OVA 파일 다운로드 선택](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>보안 확인

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA 파일에 대한 해시를 생성합니다.
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    - Azure 퍼블릭 클라우드의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(10.9GB) | [최신 버전](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Azure Government의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(63.1MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.

   ![OVF 템플릿을 배포하는 메뉴 명령](./media/tutorial-assess-vmware/deploy-ovf.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 친숙한 VM 이름을 지정합니다. VM을 호스팅할 인벤토리 개체를 선택합니다.
4. **호스트/클러스터**에서 VM이 실행될 호스트 또는 클러스터를 지정합니다.
5. **스토리지**에서 VM에 대한 스토리지 대상을 지정합니다.
6. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
7. **네트워크 매핑**에서 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure Migrate 서버 평가에 보내려면 네트워크에 인터넷 연결이 필요합니다.
8. 설정을 검토하고 확인한 다음 **마침**을 선택합니다.

## <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 OVA 대신 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 관련이 없습니다.

1. vSphere Client 콘솔에서 마우스 오른쪽 단추로 VM을 클릭한 다음, **콘솔 열기**를 선택합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. VM에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 선택하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
   - **라이선스**: 사용 조건에 동의하고 타사 정보를 읽습니다.
   - **연결**: 앱에서 VM이 인터넷에 액세스할 수 있는지 확인합니다. VM에서 프록시를 사용하는 경우:
     - **프록시 설정**을 선택하고, 프록시 주소와 수신 포트를 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 지정합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     - HTTP 프록시만 지원됩니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다.
   - **VDDK 설치**: 어플라이언스에서 VMWare vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우 VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

     Azure Migrate 서버 마이그레이션에서 VDDK를 사용하여 Azure로 마이그레이션하는 동안 머신을 복제합니다.       

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. **로그인**을 선택합니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
2. 새 탭에서 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다.
4. Azure Migrate 프로젝트가 만들어진 구독을 선택한 다음, 해당 프로젝트를 선택합니다.
5. 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
6. **등록**을 선택합니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스는 VM의 구성 및 성능 데이터를 검색하기 위해 vCenter Server에 연결해야 합니다.

### <a name="specify-vcenter-server-details"></a>vCenter Server 세부 정보 지정
1. **vCenter Server 세부 정보 지정**에서 vCenter Server 인스턴스의 이름(FQDN) 또는 IP 주소를 지정합니다. 기본 포트를 그대로 유지하거나 vCenter Server에서 수신 대기하는 사용자 지정 포트를 지정할 수 있습니다.
2. **사용자 이름** 및 **암호**에서 어플라이언스가 vCenter Server 인스턴스에서 VM을 검색하는 데 사용할 vCenter Server 계정 자격 증명을 지정합니다. 

    - [이전 자습서](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)에서 필요한 권한이 있는 계정을 설정했어야 합니다.
    - 검색 범위를 특정 VMware 개체(vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 VM)로 지정하려면 [이 문서](set-discovery-scope.md)의 지침을 검토하여 Azure Migrate에서 사용하는 계정을 제한합니다.

3. **연결 유효성 검사**를 선택하여 어플라이언스에서 vCenter Server에 연결할 수 있는지 확인합니다.
4. **VM에서 애플리케이션 및 종속성 검색**에서 필요에 따라 **자격 증명 추가**를 클릭하고, 자격 증명과 관련된 운영 체제와 자격 증명 사용자 이름 및 암호를 지정합니다. 그런 다음, **추가**를 클릭합니다.

    - [애플리케이션 검색 기능](how-to-discover-applications.md) 또는 [에이전트 없는 종속성 분석 기능](how-to-create-group-machine-dependencies-agentless.md)에 사용할 계정을 만든 경우 필요에 따라 자격 증명을 여기에 추가해야 합니다.
    - 이러한 기능을 사용하지 않는 경우 이 설정을 건너뛸 수 있습니다.
    - [앱 검색](migrate-support-matrix-vmware.md#application-discovery-requirements) 또는 [에이전트 없는 분석](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)에 필요한 자격 증명을 검토합니다.

5. **저장 및 검색 시작**을 선택하여 VM 검색을 시작합니다.

검색은 다음과 같이 작동합니다.
- 검색된 VM 메타데이터가 포털에 표시되는 데 약 15분이 걸립니다.
- 설치된 애플리케이션, 역할 및 기능을 검색하는 데 시간이 걸립니다. 기간은 검색되는 VM 수에 따라 달라집니다. 500개 VM의 경우 애플리케이션 인벤토리가 Azure Migrate 포털에 표시되는 데 약 1시간이 걸립니다.

### <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색되면 Azure Portal에서 해당 VM이 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가**에서 **검색된 서버**의 수를 표시하는 아이콘을 선택합니다.

## <a name="set-up-an-assessment"></a>평가 설정

Azure Migrate 서버 평가를 사용하여 두 가지 유형의 평가를 만들 수 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 Azure로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md), [Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 및 [ 물리적 서버](how-to-set-up-appliance-physical.md)를 평가할 수 있습니다. [자세히 알아보기](concepts-assessment-calculation.md)
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

서버 평가는 다음과 같은 두 가지 크기 조정 기준 옵션을 제공합니다.

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 권장 사항을 만드는 평가 | **Azure VM 평가**: VM 크기 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.<br/><br/> 디스크 유형 권장 사항(표준 HDD/SSD 또는 프리미엄 관리 디스크)은 온-프레미스 디스크의 IOPS 및 처리량을 기반으로 합니다.<br/><br/> **AVS(Azure VMware 솔루션) 평가**: AVS 노드 권장 사항은 CPU 및 메모리 사용률 데이터를 기반으로 합니다.
**온-프레미스인 경우** | 권장 사항을 적용하기 위해 성능 데이터를 사용하지 않는 평가. | **Azure VM 평가**: VM 크기 권장 사항은 온-프레미스 VM 크기를 기반으로 합니다.<br/><br> 권장 디스크 유형은 평가를 위해 스토리지 유형 설정에서 선택한 항목을 기반으로 합니다.<br/><br/> **AVS(Azure VMware 솔루션) 평가**: AVS 노드 권장 사항은 온-프레미스 VM 크기를 기반으로 합니다.

## <a name="run-an-assessment"></a>평가 실행

다음과 같이 *Azure VM 평가*를 실행합니다.

1. 평가를 만드는 방법에 대한 [모범 사례](best-practices-assessment.md)를 검토합니다.
2. **서버** 탭의 **Azure Migrate: 서버 평가** 타일에서 **평가**를 선택합니다.

   ![평가 단추의 위치](./media/tutorial-assess-vmware/assess.png)

3. **서버 평가**에서 평가 유형을 "Azure VM"으로 선택하고, 검색 원본을 선택하고, 평가 이름을 지정합니다.

    ![평가 기본 사항](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. **모두 보기**를 선택한 다음, 평가 속성을 검토합니다.

   ![평가 속성](./media/tutorial-assess-vmware/view-all.png)

5. **다음**을 클릭하여 **평가할 머신을 선택**합니다. **그룹 선택 또는 만들기**에서 **새로 만들기**를 선택하고 그룹 이름을 지정합니다. 그룹은 평가를 위해 하나 이상의 VM을 수집합니다.
6. **그룹에 머신 추가**에서 그룹에 추가할 VM을 선택합니다.
7. **다음**에서 **검토 + 평가 만들기**를 클릭하여 평가 세부 정보를 검토합니다.
8. **평가 만들기**를 선택하여 그룹을 만들고, 평가를 실행합니다.

   ![서버 평가](./media/tutorial-assess-vmware/assessment-create.png)

8. 평가가 만들어지면 **서버** > **Azure Migrate: 서버 평가** > **평가**에서 해당 평가를 확인합니다.
9. **평가 내보내기**를 선택하여 Excel 파일로 다운로드합니다.

**AVS(Azure VMware 솔루션) 평가**를 실행하려면 [여기](how-to-create-azure-vmware-solution-assessment.md)에서 설명된 단계를 수행합니다.


## <a name="review-an-assessment"></a>평가 검토

평가에서 설명하는 항목은 다음과 같습니다.

- **Azure 준비 상태**: VM이 Azure로 마이그레이션하는 데 적합한지 여부입니다.
- **월간 예상 비용**: Azure에서 VM을 실행하는 데 들어가는 월간 예상 컴퓨팅 및 스토리지 비용입니다.
- **월간 예상 스토리지 비용**: 마이그레이션 후 디스크 스토리지에 대한 예상 비용입니다.

평가를 보려면 다음을 수행합니다.

1. **마이그레이션 목표** > **서버**의 **Azure Migrate: 서버 평가**에서 **평가**를 클릭합니다.
2. **평가**에서 평가를 선택하여 엽니다.

   ![평가 요약](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 준비 상태 검토

1. **Azure 준비 상태**에서 VM이 Azure로 마이그레이션할 준비가 되었는지 확인합니다.
2. VM 상태를 검토합니다.
    - **Azure 준비 완료**: Azure Migrate가 평가에서 VM의 크기 및 예상 비용을 추천할 때 사용됩니다.
    - **조건과 함께 준비 완료**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **Azure를 사용할 준비 안 됨**: 문제 및 제안된 수정 사항을 보여 줍니다.
    - **알 수 없는 준비**: 데이터 가용성 문제로 인해 Azure Migrate에서 준비 상태를 평가할 수 없을 때 사용됩니다.

3. **Azure 준비 상태** 상태를 선택합니다. VM 준비 상태 세부 정보를 볼 수 있습니다. 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수도 있습니다.

### <a name="review-cost-details"></a>비용 세부 정보 검토

평가 요약에서는 Azure에서 실행되는 VM에 대한 예상 컴퓨팅 및 스토리지 비용을 보여 줍니다. 비용은 평가된 그룹의 모든 VM에 대해 집계됩니다. 드릴다운하여 특정 VM에 대한 비용 세부 정보를 확인할 수 있습니다.

> [!NOTE]
> 예상 비용은 머신, 해당 디스크 및 해당 속성에 추천되는 크기를 기반으로 하며, 온-프레미스 VM을 IaaS VM으로 실행하는 데 추정되는 비용입니다. Azure Migrate 서버 평가에서는 PaaS 또는 SaaS 비용을 고려하지 않습니다.

평가된 그룹에 대해 집계된 스토리지 비용은 여러 유형의 스토리지 디스크로 분할되어 있습니다. 

### <a name="review-confidence-rating"></a>신뢰 등급 검토

Azure Migrate Server Assessment는 1개(가장 낮음)에서 5개(가장 높음)까지의 별을 사용하는 신뢰 등급을 성능 기반 평가에 할당합니다.

![신뢰 등급](./media/tutorial-assess-vmware/confidence-rating.png)

신뢰 등급은 평가의 크기 추천 사항에 대한 안정성을 추정하는 데 도움이 됩니다. 이 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

신뢰 등급의 모범 사례에 대해 [자세히 알아보세요](best-practices-assessment.md#best-practices-for-confidence-ratings).

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Migrate 어플라이언스를 설정했습니다. 또한 평가를 만들고 검토했습니다.

Azure Migrate 서버 마이그레이션을 사용하여 VMware VM을 Azure로 마이그레이션하는 방법을 알아보려면 이 시리즈의 세 번째 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [VMware VM 마이그레이션](./tutorial-migrate-vmware.md)
