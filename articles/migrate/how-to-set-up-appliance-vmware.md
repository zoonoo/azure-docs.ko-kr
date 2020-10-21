---
title: VMware에 대 한 Azure Migrate 어플라이언스 설정
description: VMware Vm을 평가 하 고 마이그레이션하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: f20bb77c29d98ab4e3549bfed43d47d1f1f7dc0c
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318213"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware Vm에 대 한 어플라이언스 설정

이 문서에 따라 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 평가에 대 한 Azure Migrate 어플라이언스를 설정 하 고 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용 하 여 에이전트 없이 마이그레이션할 수 있습니다.

[Azure Migrate 어플라이언스](migrate-appliance.md) 는 온-프레미스 VMware vm을 검색 하 고, AZURE에 VM 메타 데이터/성능 데이터를 전송 하 고, 에이전트 없는 마이그레이션 중 VMware vm을 복제 하기 위해 서버 평가 및 서버 마이그레이션을 Azure Migrate에서 사용 하는 경량 어플라이언스입니다.

몇 가지 방법을 사용 하 여 어플라이언스를 배포할 수 있습니다.

- 다운로드한 OVA 템플릿을 사용하여 VMware VM에 설정합니다. 이 방법은이 문서에 설명 되어 있습니다.
- PowerShell 설치 관리자 스크립트를 사용하여 VMware VM 또는 물리적 머신에 설정합니다. OVA 템플릿을 사용하여 VM을 설정할 수 없거나 Azure 정부에 있는 경우 [이 방법](deploy-appliance-script.md)을 사용해야 합니다.

어플라이언스를 만든 후 Azure Migrate:Server Assessment에 연결하여, 처음으로 구성하고, Azure Migrate 프로젝트에 등록할 수 있는지 확인합니다.


## <a name="appliance-deployment-ova"></a>OVA (어플라이언스 배포)

OVA 템플릿을 사용하여 어플라이언스를 설정하려면 다음을 수행합니다.
- 포털에서 어플라이언스 이름을 제공하고, Azure Migrate 프로젝트 키를 생성합니다.
- OVA 템플릿 파일을 다운로드하여 vCenter Server로 가져옵니다.
- 어플라이언스를 만들고, Azure Migrate 서버 평가에 연결할 수 있는지 확인합니다.
- 어플라이언스를 처음으로 구성하고 Azure Migrate 프로젝트 키를 사용하여 Azure Migrate 프로젝트에 등록합니다.

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate 프로젝트 키 생성

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 선택합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다.
3. **1: Azure Migrate 프로젝트 키 생성**에서 VMware VM을 검색하도록 설정할 Azure Migrate 어플라이언스에 대한 이름을 제공합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성**을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [컴퓨터 검색] 페이지를 닫지 마세요.
1. Azure 리소스가 성공적으로 만들어지면 **Azure Migrate 프로젝트 키**가 생성됩니다.
1. 어플라이언스를 구성하는 동안 어플라이언스 등록을 완료하는 데 필요하므로 키를 복사합니다.

### <a name="download-the-ova-template"></a>OVA 템플릿 다운로드
**2: Azure Migrate 어플라이언스 다운로드**에서 .OVA 파일을 선택하고, **다운로드**를 클릭합니다. 


   ![컴퓨터 검색 선택](./media/tutorial-assess-vmware/servers-discover.png)


   ![키 생성 선택](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>보안 확인

배포하기 전에 OVA 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행 하 여 OVA에 대 한 해시를 생성 합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. 최신 어플라이언스 버전의 경우 생성 된 해시가 이러한 [설정과](./tutorial-discover-vmware.md#verify-security)일치 해야 합니다.



## <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. vSphere Client 콘솔에서 **파일** > **OVF 템플릿 배포**를 클릭합니다.
![OVF 템플릿을 배포하는 메뉴 명령](./media/tutorial-assess-vmware/deploy-ovf.png)

2. OVF 템플릿 배포 마법사 > **원본**에서 OVA 파일의 위치를 지정합니다.
3. **이름** 및 **위치**에서 친숙한 VM 이름을 지정합니다. VM을 호스팅할 인벤토리 개체를 선택합니다.
5. **호스트/클러스터**에서 VM이 실행될 호스트 또는 클러스터를 지정합니다.
6. **스토리지**에서 VM에 대한 스토리지 대상을 지정합니다.
7. **디스크 형식**에서 디스크 유형 및 크기를 지정합니다.
8. **네트워크 매핑**에서 VM이 연결할 네트워크를 지정합니다. 메타데이터를 Azure Migrate 서버 평가에 보내려면 네트워크에 인터넷 연결이 필요합니다.
9. 설정을 검토 및 확인한 다음 **마침**을 클릭합니다.


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
1. **사용 조건**에 동의하고 타사 정보를 읽습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
   - **연결**: 앱에서 VM이 인터넷에 액세스할 수 있는지 확인합니다. VM에서 프록시를 사용하는 경우:
     - **프록시 설정**을 클릭하여 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 대기 포트를 지정합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     - HTTP 프록시만 지원됩니다.
     - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장**을 클릭하여 연결 확인을 다시 트리거합니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기**를 클릭하여 어플라이언스에서 실행되는 구성 요소의 상태와 버전을 확인할 수 있습니다.
   - **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우 **설치 지침**에서 제공한 대로 VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

     Azure Migrate 서버 마이그레이션에서 VDDK를 사용하여 Azure로 마이그레이션하는 동안 머신을 복제합니다. 
1. 원하는 경우 어플라이언스를 구성하는 동안 언제든지 **필수 구성 요소를 다시 실행**하여 어플라이언스에서 모든 필수 구성 요소를 충족하는지 확인할 수 있습니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **Azure Migrate 프로젝트 키**를 붙여넣습니다. 키가 없는 경우 **서버 평가 > 검색 > 기존 어플라이언스 관리**로 차례로 이동하여 키 생성 시 제공한 어플라이언스 이름을 선택하고, 해당 키를 복사합니다.
1. **로그인**을 클릭합니다. 그러면 새 브라우저 탭에서 Azure 로그인 프롬프트가 열립니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다. 
4. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 [권한](./tutorial-discover-vmware.md#prepare-an-azure-user-account)이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기**를 클릭하여 등록 세부 정보를 확인할 수 있습니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스는 VM의 구성 및 성능 데이터를 검색하기 위해 vCenter Server에 연결해야 합니다.

1. **1단계: vCenter Server 자격 증명 제공**에서 **자격 증명 추가**를 클릭하여 자격 증명 이름을 지정하고, 어플라이언스가 vCenter Server 인스턴스에서 VM을 검색하는 데 사용할 vCenter Server 계정에 대한 **사용자 이름** 및 **암호**를 추가합니다.
    - [이전 자습서](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter)에서 필요한 권한이 있는 계정을 설정했어야 합니다.
    - 검색 범위를 특정 VMware 개체(vCenter Server 데이터 센터, 클러스터, 클러스터 폴더, 호스트, 호스트 폴더 또는 개별 VM)로 지정하려면 [이 문서](set-discovery-scope.md)의 지침을 검토하여 Azure Migrate에서 사용하는 계정을 제한합니다.
1. **2단계: vCenter Server 세부 정보 제공**에서 **검색 원본 추가**를 클릭하여 드롭다운에서 자격 증명 이름을 선택하고, vCenter Server 인스턴스의 **IP 주소/FQDN**을 지정합니다. **포트**를 기본값(443)으로 그대로 두거나 vCenter Server에서 수신 대기하는 사용자 지정 포트를 지정하고, **저장**을 클릭할 수 있습니다.
1. [저장]을 클릭하면 어플라이언스에서 제공된 자격 증명을 사용하여 vCenter Server에 대한 연결의 유효성을 검사하고, vCenter Server IP 주소/FQDN에 대한 **유효성 검사 상태**를 테이블에 표시합니다.
1. 검색을 시작하기 전에 언제든지 vCenter Server에 대한 연결의 **유효성을 다시 검사**할 수 있습니다.
1. **3단계: 설치된 애플리케이션을 검색하고 에이전트 없는 종속성 매핑을 수행하기 위한 VM 자격 증명 제공**에서 **자격 증명 추가**를 클릭하여 자격 증명을 제공하는 운영 체제, 자격 증명 이름, **사용자 이름** 및 **암호**를 지정합니다. 그런 다음, **저장**을 클릭합니다.

    - [애플리케이션 검색 기능](how-to-discover-applications.md) 또는 [에이전트 없는 종속성 분석 기능](how-to-create-group-machine-dependencies-agentless.md)에 사용할 계정을 만든 경우 필요에 따라 자격 증명을 여기에 추가해야 합니다.
    - 이러한 기능을 사용하지 않으려면 슬라이더를 클릭하여 단계를 건너뛸 수 있습니다. 나중에 언제든지 의도를 되돌릴 수 있습니다.
    - [애플리케이션 검색](migrate-support-matrix-vmware.md#application-discovery-requirements) 또는 [에이전트 없는 종속성 분석](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)에 필요한 자격 증명을 검토합니다.

5. **검색 시작**을 선택하여 VM 검색을 시작합니다. 검색이 성공적으로 시작되면 테이블에서 vCenter Server IP 주소/FQDN에 대한 검색 상태를 확인할 수 있습니다.

검색은 다음과 같이 작동합니다.
- 검색된 VM 메타데이터가 포털에 표시되는 데 약 15분이 걸립니다.
- 설치된 애플리케이션, 역할 및 기능을 검색하는 데 시간이 걸립니다. 기간은 검색되는 VM 수에 따라 달라집니다. 500개 VM의 경우 애플리케이션 인벤토리가 Azure Migrate 포털에 표시되는 데 약 1시간이 걸립니다.

## <a name="next-steps"></a>다음 단계

[VMware 평가](./tutorial-assess-vmware-azure-vm.md) 및 [에이전트 없는 마이그레이션](tutorial-migrate-vmware.md)에 대 한 자습서를 검토 합니다.