---
title: Azure Migrate 검색 및 평가를 사용하여 VMware 환경에서 실행되는 서버 검색
description: Azure Migrate 검색 및 평가 도구를 사용하여 VMware 환경에서 실행되는 온-프레미스 서버를 검색하는 방법을 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/17/2021
ms.custom: mvc
ms.openlocfilehash: f394fd4b1b4124c259489580fb5dc320fedf73fa
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863652"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>자습서: Azure Migrate: 검색 및 평가를 사용하여 VMware 환경에서 실행되는 서버 검색

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 인벤토리 및 워크로드를 검색합니다.

이 자습서에서는 간단한 Azure Migrate 어플라이언스를 사용하여 Azure Migrate: 검색 및 평가 도구를 통해 VMware 환경에서 실행되는 서버를 검색하는 방법을 보여 줍니다. 이 어플라이언스를 vCenter Server에서 실행되는 서버로 배포하여 서버와 서버의 성능 메타데이터, 서버에서 실행 중인 애플리케이션, 서버 종속성, SQL Server 인스턴스 및 데이터베이스를 지속적으로 검색합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정을 설정합니다.
> * 검색을 위해 VMware 환경을 준비합니다.
> * 프로젝트를 만듭니다.
> * Azure Migrate 어플라이언스를 설정합니다.
> * 연속 검색을 시작합니다.

> [!NOTE]
> 이 자습서에서는 시나리오를 시도할 수 있는 가장 빠른 경로를 보여 주며, 가능한 경우 기본 옵션을 사용합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 다음과 같은 사전 요구 사항이 충족되는지 확인합니다.


**요구 사항** | **세부 정보**
--- | ---
**vCenter Server/ESXi 호스트** | 버전 5.5, 6.0, 6.5 또는 6.7을 실행하는 vCenter Server가 필요합니다.<br/><br/> 서버는 버전 5.5 이상을 실행하는 ESXi 호스트에 호스트되어야 합니다.<br/><br/> 어플라이언스에서 구성 및 성능 메타데이터를 수집할 수 있도록 vCenter Server에서 443 TCP 포트의 인바운드 연결을 허용합니다.<br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter Server에 연결합니다. vCenter Server가 다른 포트에서 수신 대기하는 경우 어플라이언스 구성 관리자에서 vCenter Server 세부 정보를 제공할 때 포트를 수정할 수 있습니다.<br/><br/> 서버에 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행할 수 있도록 ESXi 호스트에서 TCP 포트 443을 통한 인바운드 액세스를 허용해야 합니다.
**어플라이언스** | vCenter Server에는 Azure Migrate 어플라이언스에 대한 서버를 할당하기 위해 다음 리소스가 필요합니다.<br/><br/> - 32GB RAM, 8개 vCPU 및 약 80GB 디스크 스토리지<br/><br/> - 외부 가상 스위치, 그리고 어플라이언스 서버에서 직접 또는 프록시를 통한 인터넷 액세스
**서버** | 모든 Windows 및 Linux OS 버전이 구성 및 성능 메타데이터 검색을 지원합니다. <br/><br/> 서버에서 애플리케이션 검색을 수행할 수 있도록 모든 Windows 및 Linux OS 버전이 지원됩니다. 에이전트 없는 종속성 분석에 지원되는 OS 버전은 [여기](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)서 확인하세요.<br/><br/> 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행하려면 VMware Tools(10.2.0 이상)를 서버에 설치하고 실행해야 합니다. Windows 서버에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.<br/><br/> SQL Server 인스턴스 및 데이터베이스를 검색하려면 [여기](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases)서 지원되는 SQL Server 버전과 에디션, 지원되는 Windows OS 버전 및 인증 메커니즘을 확인하세요.

## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

프로젝트를 만들고 Azure Migrate 어플라이언스를 등록하려면 다음 권한이 있는 계정이 필요합니다.

- Azure 구독에 대한 기여자 또는 소유자 권한
- AAD(Azure Active Directory) 앱을 등록할 수 있는 권한
- 에이전트 없는 서버 마이그레이션 중에 사용되는 Key Vault를 만들기 위한 Azure 구독의 소유자 또는 기여자 및 사용자 액세스 관리자 권한

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 다음과 같이 소유자와 협력하여 권한을 할당합니다.

1. Azure Portal에서 "구독"을 검색하여 **서비스** 에서 **구독** 을 선택합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Azure 구독을 검색하는 검색 상자":::


2. **구독** 페이지에서 프로젝트를 만들려는 구독을 선택합니다.
3. 구독에서 **액세스 제어(IAM)**  > **액세스 확인** 을 선택합니다.
4. **액세스 확인** 에서 관련 사용자 계정을 검색합니다.
5. **역할 할당 추가** 에서 **추가** 를 클릭합니다.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="사용자 계정을 검색하여 액세스를 확인하고 역할을 할당합니다.":::
    
6. **역할 할당 추가** 에서 기여자 또는 소유자 역할을 선택하고 계정(이 예제에서는 azmigrateuser)을 선택합니다. 그런 다음 **Save** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="계정에 역할을 할당하는 역할 할당 추가 페이지를 엽니다.":::

1. 어플라이언스를 등록하려면 Azure 계정에 **AAD 앱을 등록할 수 있는 권한** 이 필요합니다.
1. Azure Portal에서 **Azure Active Directory** > **사용자** > **사용자 설정** 으로 차례로 이동합니다.
1. **사용자 설정** 에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예** 로 설정됨).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="사용자 설정에서 사용자가 Active Directory 앱을 등록할 수 있는지 확인":::

9. '앱 등록' 설정이 '아니요'로 설정된 경우 테넌트/전역 관리자에게 필요한 권한을 할당하도록 요청합니다. 또는 테넌트/전역 관리자가 **애플리케이션 개발자** 역할을 계정에 할당하여 AAD 앱 등록을 허용할 수 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>VMware 준비

vCenter Server에서 계정에 OVA 파일을 사용하여 VM을 만들 수 있는 권한이 있는지 확인합니다. OVA 파일을 사용하여 Azure Migrate 어플라이언스를 VMware VM으로 배포할 때 필요합니다.

VMware 환경에서 실행 중인 서버를 검색하고 평가하려면 Azure Migrate에서 vCenter Server 읽기 전용 계정이 필요합니다. 또한 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행하려면 이 계정에 **가상 머신 > 게스트 작업** 이 가능하도록 설정된 권한이 있어야 합니다.

### <a name="create-an-account-to-access-vcenter"></a>vCenter에 액세스하는 계정 만들기

vSphere Web Client에서 계정을 다음과 같이 설정합니다.

1. 관리자 권한이 있는 계정을 사용하여 vSphere Web Client에서 **관리** 를 선택합니다.
2. **액세스** 에서 **SSO 사용자 및 그룹** 을 선택합니다.
3. **사용자** 에서 새 사용자를 추가합니다.
4. **새 사용자** 에서 계정 세부 정보를 입력합니다. 그런 후 **OK** 를 클릭합니다.
5. **전역 권한** 에서 사용자 계정을 선택하고, **읽기 전용** 역할을 계정에 할당합니다. 그런 후 **OK** 를 클릭합니다.
6. 설치된 애플리케이션 검색 및 에이전트 없는 종속성 분석을 수행하려면 **역할** 로 이동하여 **읽기 전용** 역할을 선택하고, **권한** 에서 **게스트 작업** 을 선택합니다. "자식에 전파" 확인란을 선택하여 vCenter Server 아래의 모든 개체에 권한을 전파할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="읽기 전용 역할에서 게스트 작업을 허용하는 확인란":::


> [!NOTE]
> vCenter Server 계정의 범위를 지정하여 검색 대상을 특정 vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 서버로 제한할 수 있습니다. vCenter Server 사용자 계정의 범위를 지정하는 방법에 대해 [**자세히 알아보세요**](set-discovery-scope.md).

### <a name="create-an-account-to-access-servers"></a>서버에 액세스하는 계정 만들기

설치된 애플리케이션을 검색하고, 에이전트 없는 종속성을 분석하고, SQL Server 인스턴스 및 데이터베이스를 검색하려면 서버에서 필요한 권한이 있는 사용자 계정이 필요합니다. 사용자 계정은 어플라이언스 구성 관리자에서 제공할 수 있습니다. 어플라이언스는 서버에 에이전트를 설치하지 않습니다.

1. Windows 서버의 경우 서버 관리자 권한이 있는 계정(로컬 또는 도메인)을 만듭니다. SQL Server 인스턴스와 데이터베이스를 검색하려면 Windows 또는 SQL Server 계정이 sysadmin 서버 역할의 멤버여야 합니다. 사용자 계정에 필요한 역할을 할당하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles).
2. Linux 서버의 경우 루트 권한이 있는 계정을 만듭니다. 또는 /bin/netstat 및 /bin/ls 파일에 대해 CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE 권한이 있는 계정을 만들 수 있습니다.

> [!NOTE]
> 이제 구성 관리자에서 여러 서버 자격 증명을 추가하여 설치된 애플리케이션을 검색하고, 에이전트 없는 종속성을 분석하고, SQL Server 인스턴스 및 데이터베이스를 검색할 수 있습니다. 여러 도메인/Windows(비도메인)/Linux(비도메인) 및/또는 SQL Server 인증 자격 증명을 추가할 수 있습니다. [**자세한 정보**](add-server-credentials.md)

## <a name="set-up-a-project"></a>프로젝트 설정

새 프로젝트를 설정합니다.

1. Azure Portal > **모든 서비스** 에서 **Azure Migrate** 를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate** 를 선택합니다.
3. **개요** 에서 **프로젝트 만들기** 를 선택합니다.
5. **프로젝트 만들기** 에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보** 에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="프로젝트 이름 및 지역 상자":::

7. **만들기** 를 선택합니다.
8. 프로젝트가 배포될 때까지 몇 분 정도 기다립니다. **Azure Migrate: 검색 및 평가** 도구는 기본적으로 새 프로젝트에 추가됩니다.

> [!NOTE]
> 이미 프로젝트를 만든 경우 동일한 프로젝트를 사용하여 추가 어플라이언스를 등록하면 더 많은 서버를 검색하고 평가할 수 있습니다. [**자세히 알아보세요**](create-manage-projects.md#find-a-project).

## <a name="set-up-the-appliance"></a>어플라이언스 설정

Azure Migrate: 검색 및 평가는 간단한 Azure Migrate 어플라이언스를 사용합니다. 이 어플라이언스는 서버를 검색하고 서버 구성 및 성능 메타데이터를 Azure Migrate로 보냅니다. 이 어플라이언스는 프로젝트에서 다운로드할 수 있는 OVA 템플릿을 배포하여 설정할 수 있습니다.

> [!NOTE]
> 어떤 이유로 템플릿을 사용하여 어플라이언스를 설정할 수 없는 경우 기존 Windows Server 2016 서버에서 PowerShell 스크립트를 사용하여 설정할 수 있습니다. [**자세한 정보**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>OVA를 사용하여 배포

OVA 템플릿을 사용하여 어플라이언스를 설정하려면 다음을 수행합니다.

1. 포털에서 어플라이언스 이름을 제공하고, 프로젝트 키를 생성합니다.
1. OVA 템플릿 파일을 다운로드하여 vCenter Server로 가져옵니다. OVA가 안전한지 확인하세요.
1. OVA 파일에서 어플라이언스를 만들고, Azure Migrate에 연결할 수 있는지 확인합니다.
1. 어플라이언스를 처음으로 구성하고, 프로젝트 키를 사용하여 어플라이언스를 프로젝트에 등록합니다.

### <a name="1-generate-the-project-key"></a>1. 프로젝트 키 생성

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 선택합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다.
3. **1: 프로젝트 키 생성** 에서 VMware 환경에서 서버를 검색하도록 설정할 Azure Migrate 어플라이언스에 대한 이름을 제공합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성** 을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [검색] 페이지를 닫지 마세요.
1. Azure 리소스가 성공적으로 만들어지면 **프로젝트 키** 가 생성됩니다.
1. 어플라이언스를 구성하는 동안 어플라이언스 등록을 완료하는 데 필요하므로 키를 복사합니다.

### <a name="2-download-the-ova-template"></a>2. OVA 템플릿 다운로드

**2: Azure Migrate 어플라이언스 다운로드** 에서 .OVA 파일을 선택하고, **다운로드** 를 클릭합니다.

### <a name="verify-security"></a>보안 확인

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 OVA 파일에 대한 해시를 생성합니다.
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   사용 예: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    - Azure 퍼블릭 클라우드의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(11.9GB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government의 경우:
    
        **알고리즘** | **다운로드** | **SHA256**
        --- | --- | ---
        VMware(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. 어플라이언스 서버 만들기

다운로드한 파일을 가져오고, VMware 환경에서 서버 만들기

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포** 를 클릭합니다.
2. OVF 템플릿 배포 마법사 > **원본** 에서 OVA 파일의 위치를 지정합니다.
3. **이름** 및 **위치** 에서 서버 식별 이름을 지정합니다. 서버를 호스트할 인벤토리 개체를 선택합니다.
5. **호스트/클러스터** 에서 서버가 실행될 호스트 또는 클러스터를 지정합니다.
6. **스토리지** 에서 서버의 스토리지 대상을 지정합니다.
7. **디스크 형식** 에서 디스크 유형 및 크기를 지정합니다.
8. **네트워크 매핑** 에서 서버가 연결할 네트워크를 지정합니다. 메타데이터를 Azure Migrate로 전송하려면 네트워크가 인터넷에 연결되어야 합니다.
9. 설정을 검토 및 확인한 다음 **마침** 을 클릭합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 서버에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.


### <a name="4-configure-the-appliance"></a>4. 어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 OVA 대신 [**PowerShell 스크립트**](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 수행할 필요가 없습니다.

1. vSphere Client 콘솔에서 서버를 마우스 오른쪽 단추로 클릭한 다음, **콘솔 열기** 를 선택합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. 어플라이언스에 연결할 수 있는 컴퓨터에서 브라우저를 열고, 어플라이언스 구성 관리자의 URL(`https://appliance name or IP address: 44368`)을 엽니다.

   또는 어플라이언스 서버 바탕 화면에서 구성 관리자 바로 가기를 선택하여 구성 관리자를 열 수 있습니다.
1. **사용 조건** 에 동의하고 타사 정보를 읽습니다.
1. 구성 관리자 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
   - **연결**: 어플라이언스가 서버의 인터넷 연결을 확인합니다. 서버에서 프록시를 사용하는 경우:
     - **프록시 설정** 을 클릭하여 프록시 주소(`http://ProxyIPAddress` 또는 `http://ProxyFQDN`) 및 수신 대기 포트를 지정합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     - HTTP 프록시만 지원됩니다.
     - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인할 수 있습니다.
   - **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우 **설치 지침** 에서 제공한 대로 VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

     Azure Migrate 서버 마이그레이션에서 VDDK를 사용하여 Azure로 마이그레이션하는 동안 서버를 복제합니다. 
1. 원하는 경우 어플라이언스를 구성하는 동안 언제든지 **필수 구성 요소를 다시 실행** 하여 어플라이언스에서 모든 필수 구성 요소를 충족하는지 확인할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="어플라이언스 구성 관리자의 패널 1":::

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **Azure Migrate: 검색 및 평가 > 검색 > 기존 어플라이언스 관리** 로 차례로 이동하여 키 생성 시 제공한 어플라이언스 이름을 선택하고, 해당 키를 복사합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="디바이스 코드를 보여주는 모달":::

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 디바이스 코드를 붙여넣고, Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 로그인 탭을 실수로 로그인하지 않고 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 로그인 단추를 다시 사용하도록 설정해야 합니다.
1. 성공적으로 로그인한 후 어플라이언스 구성 관리자를 사용하여 이전 탭으로 돌아갑니다.
1. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 권한이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기** 를 클릭하여 등록 세부 정보를 확인할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="어플라이언스 구성 관리자의 패널 2":::

## <a name="start-continuous-discovery"></a>연속 검색 시작

### <a name="provide-vcenter-server-details"></a>vCenter Server 세부 정보 입력

어플라이언스는 서버의 구성 및 성능 데이터를 검색하기 위해 vCenter Server에 연결해야 합니다.

1. **1단계: vCenter Server 자격 증명 제공** 에서 **자격 증명 추가** 를 클릭하여 자격 증명 이름을 지정하고, 어플라이언스가 vCenter Server 서버에서 실행 중인 서버를 검색하는 데 사용할 vCenter Server 계정의 **사용자 이름** 및 **암호** 를 추가합니다.
    - 이 문서의 앞부분에서 설명한 대로 필요한 권한이 있는 계정을 설정했어야 합니다.
    - 검색 범위를 특정 VMware 개체(vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 서버)로 지정하려면 [이 문서](set-discovery-scope.md)의 지침을 검토하여 Azure Migrate에서 사용하는 계정을 제한합니다.
1. **2단계: vCenter Server 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 드롭다운에서 자격 증명 이름을 선택하고, vCenter Server의 **IP 주소/FQDN** 을 지정합니다. **포트** 를 기본값(443)으로 그대로 두거나 vCenter Server에서 수신 대기하는 사용자 지정 포트를 지정하고, **저장** 을 클릭할 수 있습니다.
1. **저장** 을 클릭하면 어플라이언스에서 제공된 자격 증명을 사용하여 vCenter Server에 대한 연결의 유효성을 검사하고, vCenter Server IP 주소/FQDN에 대한 **유효성 검사 상태** 를 테이블에 표시합니다.
1. 검색을 시작하기 전에 언제든지 vCenter Server에 대한 연결의 **유효성을 다시 검사** 할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="어플라이언스 구성 관리자의 vCenter Server 세부 정보에 대한 패널 3":::

### <a name="provide-server-credentials"></a>서버 자격 증명 입력

**3단계: 서버 자격 증명을 제공하여 소프트웨어 인벤토리, 에이전트 없는 종속성 분석 및 SQL Server 인스턴스/데이터베이스 검색 수행** 에서 여러 서버 자격 증명을 제공하도록 선택하거나, 이러한 기능을 사용하지 않으려는 경우 단계를 건너 뛰고 vCenter Server 검색으로 계속 진행하도록 선택할 수 있습니다. 나중에 언제든지 의도를 변경할 수 있습니다.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="어플라이언스 구성 관리자의 서버 세부 정보에 대한 패널 3":::

이러한 기능을 사용하려면 아래 단계에 따라 서버 자격 증명을 제공할 수 있습니다. 어플라이언스는 검색 기능을 수행하기 위해 자격 증명을 서버에 자동으로 매핑하려고 시도합니다.

- **자격 증명 추가** 단추를 클릭하여 서버 자격 증명을 추가할 수 있습니다. 그러면 드롭다운에서 **자격 증명 유형** 을 선택할 수 있는 모달이 열립니다.
- 도메인/Windows(비도메인)/Linux(비도메인)/SQL Server 인증 자격 증명을 제공할 수 있습니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).
- 자격 증명 형식마다 자격 증명의 식별 이름을 지정하고, **사용자 이름** 및 **암호** 를 추가하고, **저장** 을 클릭해야 합니다.
- 도메인 자격 증명을 선택하는 경우 도메인에 대한 FQDN도 지정해야 합니다. FQDN은 해당 도메인의 Active Directory를 사용하여 자격 증명의 신뢰성에 대한 유효성을 검사하는 데 필요합니다.
- 설치된 애플리케이션을 검색하고, 에이전트 없는 종속성을 분석하고, SQL Server 인스턴스 및 데이터베이스를 검색하기 위해 계정에 [필요한 권한](add-server-credentials.md#required-permissions)을 검토합니다.
- 여러 자격 증명을 한 번에 추가하려면 **추가** 를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다.
- **저장** 또는 **더 추가** 를 클릭하면 어플라이언스가 도메인의 Active Directory를 사용하여 도메인 자격 증명의 유효성을 검사합니다. 어플라이언스에서 반복을 여러 차례 수행하여 각 서버에 자격 증명을 매핑할 때 계정 잠금을 방지하기 위해 이 작업이 수행됩니다.
- 자격 증명 테이블에서 모든 도메인 자격 증명의 **유효성 검사 상태** 를 볼 수 있습니다. 도메인 자격 증명만 유효성 검사가 수행됩니다.
- 유효성 검사가 실패하는 경우 **실패** 상태를 클릭하여 발생한 오류를 살펴보고, 문제를 해결한 후 **자격 증명 유효성 재검사** 를 클릭하여 실패한 도메인 자격 증명의 유효성을 다시 검사할 수 있습니다.

### <a name="start-discovery"></a>검색 시작

1. **검색 시작** 을 선택하여 vCenter Server 검색을 시작합니다. 검색이 성공적으로 시작되면 원본 테이블에서 vCenter Server IP 주소/FQDN에 대한 검색 상태를 확인할 수 있습니다.
1. 서버 자격 증명을 제공한 경우 vCenter Server 검색이 완료된 후 소프트웨어 인벤토리(설치된 애플리케이션 검색)가 자동으로 시작됩니다. 소프트웨어 인벤토리는 12시간마다 한 번씩 수행됩니다.
1. [소프트웨어 인벤토리](how-to-discover-applications.md)는 서버에서 실행되는 SQL Server 인스턴스를 식별하여 해당 정보를 사용하고, 어플라이언스는 어플라이언스에 제공된 Windows 인증 또는 SQL Server 인증 자격 증명을 통해 인스턴스에 연결을 시도하고 SQL Server 데이터베이스와 해당 속성에 대한 데이터를 수집합니다. SQL 검색은 24시간마다 한 번씩 수행됩니다.
1. 소프트웨어 인벤토리 중에 추가된 서버 자격 증명은 서버에 대해 반복되고, 에이전트 없는 종속성 분석에 대해 유효성이 검사됩니다. 포털에서 에이전트 없는 종속성 분석을 서버에 사용하도록 설정할 수 있습니다. 유효성 검사에 성공한 서버만 선택하여 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다.

> [!Note]
>Azure Migrate는 Azure Migrate 어플라이언스와 원본 SQL Server 인스턴스 간의 통신을 암호화합니다(암호화 연결 속성이 TRUE로 설정됨). 이러한 연결은 [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate)로 암호화됩니다(TRUE로 설정됨). 전송 계층에서는 SSL을 사용하여 채널을 암호화하고 인증서 체인을 무시하여 신뢰의 유효성을 확인합니다. 어플라이언스 서버는 [**인증서의 루트 인증 기관을 신뢰**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)하도록 설정되어야 합니다.<br/>
서버가 시작될 때 서버에 인증서가 제공되지 않으면 SQL Server는 로그인 패킷을 암호화하는 데 사용할 자체 서명된 인증서를 생성합니다. [**자세한 정보**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

검색은 다음과 같이 작동합니다.

- 검색된 서버 인벤토리가 포털에 표시될 때까지 약 15분이 걸립니다.
- 설치된 애플리케이션을 검색하는 데 다소 시간이 걸릴 수 있습니다. 소요 시간은 검색할 서버 수에 따라 달라집니다. 서버가 500개인 경우 검색된 인벤토리가 Azure Migrate 포털에 표시될 때까지 약 1시간이 걸립니다.
- 서버 검색이 완료되면 포털에서 서버에 대한 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다.
- 검색이 시작된 후 24시간 내에 SQL Server 인스턴스 및 데이터베이스 데이터가 포털에 표시되기 시작합니다.

## <a name="next-steps"></a>다음 단계

- Azure VM으로 마이그레이션할 [서버를 평가](./tutorial-assess-vmware-azure-vm.md)합니다.
- Azure SQL로 마이그레이션할 [SQL 서버를 평가](./tutorial-assess-sql.md)합니다.
- 검색 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.