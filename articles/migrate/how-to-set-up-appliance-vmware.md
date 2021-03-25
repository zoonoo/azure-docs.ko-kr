---
title: VMware에 대 한 Azure Migrate 어플라이언스 설정
description: VMware 환경에서 서버를 평가 하 고 마이그레이션하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: c2ffa85ed6cb007dd766d4517a86783d21d4913e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110511"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>VMware 환경에서 서버에 대 한 어플라이언스 설정

이 문서에 따라 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 도구를 사용 하 여 Azure Migrate 어플라이언스를 설정 하 고 [Azure Migrate: 서버 마이그레이션](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용 하 여 에이전트 없이 마이그레이션할 수 있습니다.

[Azure Migrate 어플라이언스](migrate-appliance.md) 는 vCenter Server에서 실행 되는 서버를 검색 하 고, 서버 구성 및 성능 메타 데이터를 Azure로 전송 하 고, 에이전트 없는 마이그레이션을 사용 하 여 서버를 복제 하기 위해 검색 및 평가 및 서버 마이그레이션과 같은 Azure Migrate에서 사용 하는 경량 어플라이언스입니다.

몇 가지 방법을 사용 하 여 어플라이언스를 배포할 수 있습니다.

- 다운로드 한 OVA 템플릿을 사용 하 여 vCenter Server에서 서버를 만듭니다. 이 방법은이 문서에 설명 되어 있습니다.
- PowerShell 설치 관리자 스크립트를 사용 하 여 기존 서버에서 어플라이언스를 설정 합니다. OVA 템플릿을 사용할 수 없거나 Azure Government 중인 경우 [이 메서드](deploy-appliance-script.md) 를 사용 해야 합니다.

어플라이언스를 만든 후에 Azure Migrate: 검색 및 평가에 연결할 수 있는지 확인 하 고, 프로젝트에 등록 하 고, 검색을 시작 하도록 어플라이언스를 구성 합니다.

## <a name="deploy-with-ova"></a>OVA를 사용하여 배포

OVA 템플릿을 사용하여 어플라이언스를 설정하려면 다음을 수행합니다.

1. 포털에서 어플라이언스 이름을 제공 하 고 프로젝트 키를 생성 합니다.
1. OVA 템플릿 파일을 다운로드하여 vCenter Server로 가져옵니다. OVA가 안전한지 확인하세요.
1. OVA 파일로 어플라이언스 VM을 만들고 Azure Migrate에 연결할 수 있는지 확인합니다.
1. 처음으로 어플라이언스를 구성 하 고 프로젝트 키를 사용 하 여 프로젝트에 등록 합니다.

### <a name="1-generate-the-project-key"></a>1. 프로젝트 키 생성

1. **마이그레이션 목표**  >  **서버**  >  **Azure Migrate: 검색 및 평가** 에서 **검색** 을 선택 합니다.
2. 서버 를  >  **가상화 하는** 서버 검색에서 **예를 선택 하 VMware vSphere 하이퍼바이저를 사용** 합니다.
3. **1: 프로젝트 키 생성** 에서 VMware 환경에서 서버를 검색 하는 데 설정할 Azure Migrate 어플라이언스의 이름을 제공 합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성** 을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 검색 페이지를 닫지 마십시오.
1. Azure 리소스를 성공적으로 만든 후에는 프로젝트 키 * *가 생성 됩니다.
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

어플라이언스 서버가 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure url에 연결할 수 있는지 확인 합니다.


### <a name="4-configure-the-appliance"></a>4. 어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 OVA 대신 [**PowerShell 스크립트**](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 수행할 필요가 없습니다.

1. vSphere Client 콘솔에서 서버를 마우스 오른쪽 단추로 클릭한 다음, **콘솔 열기** 를 선택합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. 어플라이언스 서버에 연결할 수 있는 모든 서버에서 브라우저를 열고 어플라이언스 구성 관리자의 `https://appliance name or IP address: 44368` URL ()을 엽니다.

   또는 어플라이언스 서버 바탕 화면에서 구성 관리자 바로 가기를 선택하여 구성 관리자를 열 수 있습니다.
1. **사용 조건** 에 동의하고 타사 정보를 읽습니다.
1. 구성 관리자 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
   - **연결**: 어플라이언스가 서버의 인터넷 연결을 확인합니다. 서버에서 프록시를 사용하는 경우:
     - **설정 프록시** 를 클릭 하 여 양식 `http://ProxyIPAddress` 또는 수신 대기 포트에서 프록시 주소를 지정 `http://ProxyFQDN` 합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     - HTTP 프록시만 지원됩니다.
     - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인할 수 있습니다.
   - **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치되지 않은 경우 **설치 지침** 에서 제공한 대로 VMware에서 VDDK 6.7을 다운로드하고, 다운로드한 zip 콘텐츠를 어플라이언스의 지정된 위치에 추출합니다.

     Azure Migrate 서버 마이그레이션에서 VDDK를 사용하여 Azure로 마이그레이션하는 동안 서버를 복제합니다. 
1. 원하는 경우 어플라이언스를 구성하는 동안 언제든지 **필수 구성 요소를 다시 실행** 하여 어플라이언스에서 모든 필수 구성 요소를 충족하는지 확인할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="어플라이언스 구성 관리자의 패널 1":::


## <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **검색 및 평가> 검색 및 평가로 이동 하> 기존 어플라이언스를 검색** 하 고, 키 생성 시 제공한 어플라이언스 이름을 선택 하 고, 해당 키를 복사 합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="디바이스 코드를 보여주는 모달":::

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 Azure 사용자 이름 및 암호를 사용 하 여 장치 코드를 붙여넣고 로그인 합니다.
   
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
    - 특정 VMware 개체 (vCenter Server 데이터 센터, 클러스터, 클러스터, 호스트, 호스트 폴더 또는 개별 서버)로 검색 범위를 지정 하려는 경우 [이 문서의](set-discovery-scope.md) 지침을 검토 하 여 Azure Migrate에서 사용 하는 계정을 제한 하십시오.
1. **2단계: vCenter Server 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 드롭다운에서 자격 증명 이름을 선택하고, vCenter Server의 **IP 주소/FQDN** 을 지정합니다. **포트** 를 기본값(443)으로 그대로 두거나 vCenter Server에서 수신 대기하는 사용자 지정 포트를 지정하고, **저장** 을 클릭할 수 있습니다.
1. **저장** 을 클릭하면 어플라이언스에서 제공된 자격 증명을 사용하여 vCenter Server에 대한 연결의 유효성을 검사하고, vCenter Server IP 주소/FQDN에 대한 **유효성 검사 상태** 를 테이블에 표시합니다.
1. 검색을 시작 하기 전에 언제 든 지 vCenter Server에 대 한 연결의 **유효성** 을 다시 검사할 수 있습니다.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server 세부 정보에 대 한 어플라이언스 구성 관리자의 패널":::

### <a name="provide-server-credentials"></a>서버 자격 증명 입력

**3단계: 서버 자격 증명을 입력하여 소프트웨어 인벤토리, 에이전트 없는 종속성 분석, SQL Server 인스턴스 및 데이터베이스 검색을 수행** 합니다. 서버 자격 증명을 여러 개 입력해도 되고, 또는 이 기능을 활용하지 않으려면 이 단계를 건너뛰고 vCenter Server 검색을 진행해도 됩니다. 나중에 언제 든 지 변경할 수 있습니다.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="어플라이언스 구성 관리자의 서버 세부 정보에 대한 패널 3":::


이러한 기능을 활용 하려는 경우 다음 단계를 수행 하 여 서버 자격 증명을 제공할 수 있습니다. 어플라이언스는 검색 기능을 수행 하기 위해 자격 증명을 서버에 자동으로 매핑하도록 시도 합니다.

- **자격 증명 추가** 단추를 클릭 하 여 서버 자격 증명을 추가할 수 있습니다. 이렇게 하면 드롭다운에서 **자격 증명 형식을** 선택할 수 있는 모달이 열립니다.
- 도메인/Windows(비도메인)/Linux(비도메인)/SQL Server 인증 자격 증명을 제공할 수 있습니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).
- 자격 증명 형식마다 자격 증명의 식별 이름을 지정하고, **사용자 이름** 및 **암호** 를 추가하고, **저장** 을 클릭해야 합니다.
- 도메인 자격 증명을 선택 하는 경우에는 도메인에 대 한 FQDN도 지정 해야 합니다. FQDN은 해당 도메인의 Active Directory 자격 증명의 신뢰성을 확인 하는 데 필요 합니다.
- 설치된 애플리케이션을 검색하고, 에이전트 없는 종속성을 분석하고, SQL Server 인스턴스 및 데이터베이스를 검색하기 위해 계정에 [필요한 권한](add-server-credentials.md#required-permissions)을 검토합니다.
- 여러 자격 증명을 한 번에 추가하려면 **추가** 를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다.
- **저장** 또는 **더 추가** 를 클릭하면 어플라이언스가 도메인의 Active Directory를 사용하여 도메인 자격 증명의 유효성을 검사합니다. 어플라이언스에서 반복을 여러 차례 수행하여 각 서버에 자격 증명을 매핑할 때 계정 잠금을 방지하기 위해 이 작업이 수행됩니다.
- 자격 증명 테이블에서 모든 도메인 자격 증명의 **유효성 검사 상태** 를 볼 수 있습니다. 도메인 자격 증명만 유효성 검사가 수행됩니다.
- 유효성 검사가 실패하는 경우 **실패** 상태를 클릭하여 발생한 오류를 살펴보고, 문제를 해결한 후 **자격 증명 유효성 재검사** 를 클릭하여 실패한 도메인 자격 증명의 유효성을 다시 검사할 수 있습니다.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="여러 자격 증명을 제공 하기 위한 어플라이언스 구성 관리자의 패널 3":::

### <a name="start-discovery"></a>검색 시작

1. **검색 시작** 을 선택하여 vCenter Server 검색을 시작합니다. 검색이 성공적으로 시작되면 원본 테이블에서 vCenter Server IP 주소/FQDN에 대한 검색 상태를 확인할 수 있습니다.
1. 서버 자격 증명을 제공한 경우 vCenter Server 검색이 완료된 후 소프트웨어 인벤토리(설치된 애플리케이션 검색)가 자동으로 시작됩니다. 소프트웨어 인벤토리는 12시간마다 한 번씩 수행됩니다.
1. [소프트웨어 인벤토리](how-to-discover-applications.md)는 서버에서 실행되는 SQL Server 인스턴스를 식별하여 해당 정보를 사용하고, 어플라이언스는 어플라이언스에 제공된 Windows 인증 또는 SQL Server 인증 자격 증명을 통해 인스턴스에 연결을 시도하고 SQL Server 데이터베이스와 해당 속성에 대한 데이터를 수집합니다. SQL 검색은 24시간마다 한 번씩 수행됩니다.
1. 소프트웨어 인벤토리 중에 추가 된 서버 자격 증명은 서버에 대해 반복 되 고 에이전트 없는 종속성 분석에 대해 유효성이 검사 됩니다. 포털에서 서버에 대 한 에이전트 없는 종속성 분석을 사용 하도록 설정할 수 있습니다. 유효성 검사에 성공한 서버만 선택하여 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다.

검색은 다음과 같이 작동합니다.

- 검색된 서버 인벤토리가 포털에 표시될 때까지 약 15분이 걸립니다.
- 설치된 애플리케이션을 검색하는 데 다소 시간이 걸릴 수 있습니다. 소요 시간은 검색할 서버 수에 따라 달라집니다. 서버가 500개인 경우 검색된 인벤토리가 Azure Migrate 포털에 표시될 때까지 약 1시간이 걸립니다.
- 서버 검색이 완료되면 포털에서 서버에 대한 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다.
- 검색이 시작된 후 24시간 내에 SQL Server 인스턴스 및 데이터베이스 데이터가 포털에 표시되기 시작합니다.

## <a name="next-steps"></a>다음 단계

[VMware 평가](./tutorial-assess-vmware-azure-vm.md) 및 [에이전트 없는 마이그레이션](tutorial-migrate-vmware.md)에 대 한 자습서를 검토 합니다.
