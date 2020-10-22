---
title: Azure Migrate 서버 평가를 사용하여 Hyper-V VM 검색
description: Azure Migrate 서버 평가 도구를 사용하여 온-프레미스 Hyper-V VM을 검색하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 0643a13a07572dc24ef895062593e00188a0752f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317162"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>자습서: 서버 평가를 사용하여 Hyper-V VM 검색

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 인벤토리 및 워크로드를 검색합니다. 

이 자습서에서는 Azure Migrate를 사용하여 온-프레미스 Hyper-V VM(가상 머신)을 검색하는 방법을 보여줍니다. 간단한 Azure Migrate 어플라이언스를 사용하는 서버 평가 도구입니다. 어플라이언스를 Hyper-V VM으로 배포하여 머신 및 성능 메타데이터를 지속적으로 검색합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정 설정
> * Hyper-V 환경에서 검색할 준비를 합니다.
> * Azure Migrate 프로젝트를 만듭니다.
> * Azure Migrate 어플라이언스를 설정합니다.
> * 연속 검색을 시작합니다.

> [!NOTE]
> 이 자습서에서는 시나리오를 가장 빠르게 시도할 수 있는 경로를 보여주며, 기본 옵션을 사용합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 이러한 필수 구성 요소가 있는지 확인합니다.


**요구 사항** | **세부 정보**
--- | ---
**Hyper-V 호스트** | VM이 있는 Hyper-V 호스트는 독립 실행형이거나 클러스터에 있을 수 있습니다.<br/><br/> 호스트에서 Windows Server 2019, Windows Server 2016 또는 Windows Server 2012 R2가 실행되고 있어야 합니다.<br/><br/> 어플라이언스가 CIM(Common Information Model) 세션을 사용하여 VM 메타데이터 및 성능 데이터를 가져오기 위해 연결할 수 있도록 WinRM 포트 5985(HTTP)에서 인바운드 연결을 허용하는지 확인합니다.
**어플라이언스 배포** | Hyper-v에는 어플라이언스에 VM을 할당하는 리소스가 필요합니다.<br/><br/> - Windows Server 2016<br/><br/> \- RAM 16GB<br/><br/> - 8개의 vCPU<br/><br/> - 약 80GB의 디스크 스토리지.<br/><br/> - 외부 가상 스위치.<br/><br/> - 직접 또는 프록시를 통해 VM에 대한 인터넷 액세스.
**VM** | VM은 모든 Windows 또는 Linux 운영 체제를 실행할 수 있습니다. 

시작하기 전에 어플라이언스가 검색 중에 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---hyper-v)할 수 있습니다.

## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

Azure Migrate 프로젝트를 만들고 Azure Migrate 어플라이언스를 등록하려면 다음이 포함된 계정이 필요합니다.
- Azure 구독에 대한 기여자 또는 소유자 권한.
- Azure Active Directory 앱을 등록할 수 있는 권한.

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 다음과 같이 소유자와 협력하여 권한을 할당합니다.


1. Azure Portal에서 "구독"을 검색하여 **서비스**에서 **구독**을 선택합니다.

    ![Azure 구독을 검색하는 검색 상자](./media/tutorial-discover-hyper-v/search-subscription.png)

2. **구독** 페이지에서 Azure Migrate 프로젝트를 만들려는 구독을 선택합니다. 
3. 구독에서 **액세스 제어(IAM)**  > **액세스 확인**을 선택합니다.
4. **액세스 확인**에서 관련 사용자 계정을 검색합니다.
5. **역할 할당 추가**에서 **추가**를 클릭합니다.

    ![사용자 계정을 검색하여 액세스를 확인하고 역할을 할당합니다.](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. **역할 할당 추가**에서 기여자 또는 소유자 역할을 선택하고 계정(이 예제에서는 azmigrateuser)을 선택합니다. 그런 다음 **Save**를 클릭합니다.

    ![계정에 역할을 할당하는 역할 할당 추가 페이지를 엽니다.](./media/tutorial-discover-hyper-v/assign-role.png)

7. 포털에서 사용자를 검색하고 **서비스**에서 **사용자**를 선택합니다.
8. **사용자 설정**에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예**로 설정됨).

    ![사용자 설정에서 사용자가 Active Directory 앱을 등록할 수 있는지 확인합니다.](./media/tutorial-discover-hyper-v/register-apps.png)

9. 또는 테넌트/전역 관리자가 **애플리케이션 개발자** 역할을 계정에 할당하여 AAD 앱 등록을 허용할 수 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Hyper-V 호스트 준비

Hyper-V 호스트에서 관리자 액세스 권한이 있는 계정을 설정합니다. 어플라이언스는 이 계정을 검색에 사용합니다.

- 옵션 1: Hyper-V 호스트 머신에 대한 관리자 액세스 권한이 있는 계정을 설정합니다.
- 옵션 2: 로컬 관리자 계정 또는 도메인 관리자 계정을 준비하고 다음 그룹에 계정을 추가합니다. 원격 관리 사용자, Hyper-V 관리자 및 성능 모니터 사용자.


## <a name="set-up-a-project"></a>프로젝트 설정

새 Azure Migrate 프로젝트를 설정합니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**에서 **프로젝트 만들기**를 선택합니다.
5. **프로젝트 만들기**에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보**에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

   ![프로젝트 이름 및 지역 상자](./media/tutorial-discover-hyper-v/new-project.png)

7. **만들기**를 선택합니다.
8. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다.

**Azure Migrate : 서버 평가** 도구는 기본적으로 새 프로젝트에 추가됩니다.

![기본적으로 추가된 서버 평가 도구를 보여주는 페이지](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>어플라이언스 설정

이 자습서에서는 다음과 같이 Hyper-V VM에 어플라이언스를 설정합니다.

- 포털에서 어플라이언스 이름을 제공하고 Azure Migrate 프로젝트 키를 생성합니다.
- Azure Portal에서 압축된 Hyper-V VHD를 다운로드합니다.
- 어플라이언스를 만들고, Azure Migrate 서버 평가에 연결할 수 있는지 확인합니다.
- 어플라이언스를 처음으로 구성하고 Azure Migrate 프로젝트 키를 사용하여 Azure Migrate 프로젝트에 등록합니다.
> [!NOTE]
> 어떤 이유로 템플릿을 사용하여 어플라이언스를 설정할 수 없는 경우 PowerShell 스크립트를 사용하여 설정할 수 있습니다. [자세히 알아보기](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate 프로젝트 키 생성

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 선택합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 선택합니다.
3. **1: Azure Migrate 프로젝트 키 생성**에서 Hyper-V VM을 검색하도록 설정할 Azure Migrate 어플라이언스에 대한 이름을 제공합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성**을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [컴퓨터 검색] 페이지를 닫지 마세요.
1. Azure 리소스가 성공적으로 만들어지면 **Azure Migrate 프로젝트 키**가 생성됩니다.
1. 어플라이언스를 구성하는 동안 어플라이언스 등록을 완료하는 데 필요하므로 키를 복사합니다.

### <a name="download-the-vhd"></a>VHD 다운로드

**2: Azure Migrate 어플라이언스 다운로드**에서 .VHD 파일을 선택하고, **다운로드**를 클릭합니다. 


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.

2. 다음 PowerShell 명령을 실행하여 ZIP 파일에 대한 해시를 생성합니다.
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 사용 예: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.

    - Azure 퍼블릭 클라우드의 경우:

        **시나리오** | **다운로드** | **SHA256**
        --- | --- | ---
        Hyper-V(8.91GB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Azure Government의 경우:

        **시나리오*** | **다운로드** | **SHA256**
        --- | --- | ---
        Hyper-V(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. 어플라이언스 VM을 호스팅할 Hyper-V 호스트의 폴더에 압축된 VHD 파일을 추출합니다. 세 개의 폴더가 추출됩니다.
2. Hyper-V 관리자를 엽니다. **작업**에서 **Virtual Machine 가져오기**를 클릭합니다.
2. Virtual Machine 가져오기 마법사 > **시작하기 전에**에서 **다음**을 클릭합니다.
3. **폴더 찾기**에서 추출된 VHD가 포함된 폴더를 지정합니다. 그런 후 **Next** 를 클릭합니다.
1. **Virtual Machine 선택**에서 **다음**을 클릭합니다.
2. **가져오기 유형 선택**에서 **가상 머신 복사(새로운 고유 ID 만들기)** 를 클릭합니다. 그런 후 **Next** 를 클릭합니다.
3. **대상 선택**에서 기본 설정을 그대로 둡니다. **다음**을 클릭합니다.
4. **스토리지 폴더**에서 기본 설정을 그대로 둡니다. **다음**을 클릭합니다.
5. **네트워크 선택**에서 VM에서 사용할 가상 스위치를 지정합니다. 이 스위치에는 데이터를 Azure로 보내기 위한 인터넷 연결이 필요합니다.
6. **요약** 에서 설정을 검토합니다. **마침**을 클릭합니다.
7. Hyper-V 관리자 > **Virtual Machines**에서 VM을 시작합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 VHD 대신 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 관련이 없습니다.

1. Hyper-V 관리자 > **Virtual Machines**에서 마우스 오른쪽 단추로 VM > **연결**을 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. VM에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 클릭하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
1. **사용 조건**에 동의하고 타사 정보를 읽습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **연결**: 앱에서 VM이 인터넷에 액세스할 수 있는지 확인합니다. VM에서 프록시를 사용하는 경우:
      - **프록시 설정**을 클릭하여 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 대기 포트를 지정합니다.
      - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
      - HTTP 프록시만 지원됩니다.
      - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장**을 클릭하여 연결 확인을 다시 트리거합니다.
    - **시간 동기화**: 시간이 확인됩니다. VM 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate 서버 평가에서 최신 업데이트가 어플라이언스에 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기**를 클릭하여 어플라이언스에서 실행되는 구성 요소의 상태와 버전을 확인할 수 있습니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **Azure Migrate 프로젝트 키**를 붙여넣습니다. 키가 없는 경우 **서버 평가 > 검색 > 기존 어플라이언스 관리**로 차례로 이동하여 키 생성 시 제공한 어플라이언스 이름을 선택하고, 해당 키를 복사합니다.
1. **로그인**을 클릭합니다. 그러면 새 브라우저 탭에서 Azure 로그인 프롬프트가 열립니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다. 
4. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 권한이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기**를 클릭하여 등록 세부 정보를 확인할 수 있습니다.



### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD에 대한 자격 증명 위임

SMB에서 VHD를 실행하는 경우 자격 증명을 어플라이언스에서 Hyper-V 호스트로 위임하도록 설정해야 합니다. 어플라이언스에서 이 작업을 수행하려면 다음을 수행합니다.

1. 어플라이언스 VM에서 다음 명령을 실행합니다. HyperVHost1/HyperVHost2는 예제 호스트 이름입니다.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. 또는 어플라이언스의 로컬 그룹 정책 편집기에서 다음을 수행합니다.
    - **로컬 컴퓨터 정책** > **컴퓨터구성**에서 **관리 템플릿** > **시스템** > **자격 증명 위임**을 차례로 클릭합니다.
    - **새로운 자격 증명 위임 허용**을 두 번 클릭하고 **사용**을 선택합니다.
    - **옵션**에서 **표시**를 클릭하고, **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.
    - **자격 증명 위임**에서 **서버 인증이 NTLM 전용일 경우 새로운 자격 증명 허용**을 두 번 클릭합니다. **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.

## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스에서 Hyper-V 호스트 또는 클러스터에 연결하고, VM 검색을 시작합니다.

1. **1단계: Hyper-V 호스트 자격 증명 제공**에서 **자격 증명 추가**를 클릭하여 자격 증명 이름을 지정하고, 어플라이언스에서 VM을 검색하는 데 사용할 Hyper-V 호스트/클러스터에 대한 **사용자 이름** 및 **암호**를 추가합니다. **Save**를 클릭합니다.
1. 여러 자격 증명을 한 번에 추가하려면 **추가**를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다. Hyper-V VM 검색에는 여러 자격 증명이 지원됩니다.
1. **2단계: Hyper-V 호스트/클러스터 세부 정보 제공**에서 **검색 원본 추가**를 클릭하여 Hyper-V 호스트/클러스터 **IP 주소/FQDN** 및 호스트/클러스터에 연결할 자격 증명 이름을 지정합니다.
1. **단일 항목** 또는 **여러 항목**을 한 번에 추가할 수 있습니다. 또한 **CSV 가져오기**를 통해 Hyper-V 호스트/클러스터 세부 정보를 제공하는 옵션이 있습니다.


    - **단일 항목 추가**를 선택하는 경우 자격 증명 이름 및 Hyper-V 호스트/클러스터 **IP 주소/FQDN**을 지정하고, **저장**을 클릭해야 합니다.
    - **여러 항목 추가** _(기본적으로 선택됨)_ 를 선택하는 경우 텍스트 상자에서 자격 증명 이름과 함께 Hyper-V 호스트/클러스터 **IP 주소/FQDN**을 지정하여 여러 레코드를 한 번에 추가할 수 있습니다. 추가된 레코드를 **확인**하고 **저장**을 클릭합니다.
    - **CSV 가져오기**를 선택하는 경우 CSV 템플릿 파일을 다운로드하고, 파일을 Hyper-V 호스트/클러스터 **IP 주소/FQDN** 및 자격 증명 이름으로 채울 수 있습니다. 그런 다음, 파일을 어플라이언스로 가져와서 파일의 레코드를 **확인**하고 **저장**을 클릭합니다.

1. [저장]을 클릭하면 어플라이언스에서 추가된 Hyper-V 호스트/클러스터에 대한 연결의 유효성을 검사하고, 각 호스트/클러스터에 대한 **유효성 검사 상태**를 테이블에 표시합니다.
    - 호스트/클러스터의 유효성이 성공적으로 검사되면 해당 IP 주소/FQDN을 클릭하여 자세한 정보를 확인할 수 있습니다.
    - 호스트에 대한 유효성 검사가 실패하면 테이블의 [상태] 열에서 **유효성 검사 실패**를 클릭하여 오류를 검토합니다. 문제를 해결하고 유효성을 다시 검사합니다.
    - 호스트 또는 클러스터를 제거하려면 **삭제**를 클릭합니다.
    - 클러스터에서 특정 호스트를 제거할 수 없습니다. 전체 클러스터만 제거할 수 있습니다.
    - 클러스터의 특정 호스트에 문제가 있는 경우에도 클러스터를 추가할 수 있습니다.
1. 검색을 시작하기 전에 언제든지 호스트/클러스터에 대한 연결의 **유효성을 다시 검사**할 수 있습니다.
1. **검색 시작**을 클릭하여 유효성이 성공적으로 검사된 호스트/클러스터에서 VM 검색을 시작합니다. 검색이 성공적으로 시작되면 테이블에서 각 호스트/클러스터에 대한 검색 상태를 확인할 수 있습니다.

그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 호스트당 약 2분이 걸립니다.

## <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색이 완료되면 Azure Portal에서 해당 VM이 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버**의 수를 표시하는 아이콘을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Azure VM으로 마이그레이션할 [Hyper-V VM을 평가](tutorial-assess-hyper-v.md)합니다.
- 어플라이언스가 검색 중에 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---hyper-v)합니다.