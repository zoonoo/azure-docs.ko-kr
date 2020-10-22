---
title: Azure Migrate 서버 평가를 사용하여 물리적 서버 검색
description: Azure Migrate 서버 평가를 사용하여 온-프레미스 물리적 서버를 검색하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e7cbd7939248686a251fdf56bf1a5f1acc952a3a
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314075"
---
# <a name="tutorial-discover-physical-servers-with-server-assessment"></a>자습서: 서버 평가를 사용하여 물리적 서버 검색

Azure로 마이그레이션하는 과정의 일환으로 평가 및 마이그레이션할 서버를 검색합니다.

이 자습서에서는 Azure Migrate를 사용하여 온-프레미스 물리적 서버를 검색하는 방법을 보여줍니다. 간단한 Azure Migrate 어플라이언스를 사용하는 서버 평가 도구입니다. 어플라이언스를 물리적 서버로 배포하여 머신 및 성능 메타데이터를 지속적으로 검색합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정을 설정합니다.
> * 검색할 물리적 서버를 준비합니다.
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
**어플라이언스** | Azure Migrate 어플라이언스를 실행할 머신이 필요합니다. 머신에는 다음이 있어야 합니다.<br/><br/> - Windows Server 2016이 설치되었습니다. _(현재 어플라이언스 배포는 Windows Server 2016에서만 지원됩니다.)_<br/><br/> - 16GB RAM, 8개의 vCPU, 약 80GB의 디스크 스토리지<br/><br/> - 직접 또는 프록시를 통해 인터넷에 액세스할 수 있는 고정 또는 동적 IP 주소.
**Windows 서버** | 어플라이언스가 구성 및 성능 메타데이터를 가져올 수 있도록 WinRM 포트 5985(HTTP)에서 인바운드 연결을 허용합니다.
**Linux 서버** | 포트 22(TCP)에서 인바운드 연결을 허용합니다.

## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

Azure Migrate 프로젝트를 만들고 Azure Migrate 어플라이언스를 등록하려면 다음이 포함된 계정이 필요합니다.
- Azure 구독에 대한 기여자 또는 소유자 권한.
- Azure Active Directory 앱을 등록할 수 있는 권한.

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 다음과 같이 소유자와 협력하여 권한을 할당합니다.

1. Azure Portal에서 "구독"을 검색하여 **서비스**에서 **구독**을 선택합니다.

    ![Azure 구독을 검색하는 검색 상자](./media/tutorial-discover-physical/search-subscription.png)

2. **구독** 페이지에서 Azure Migrate 프로젝트를 만들려는 구독을 선택합니다. 
3. 구독에서 **액세스 제어(IAM)**  > **액세스 확인**을 선택합니다.
4. **액세스 확인**에서 관련 사용자 계정을 검색합니다.
5. **역할 할당 추가**에서 **추가**를 클릭합니다.

    ![사용자 계정을 검색하여 액세스를 확인하고 역할을 할당합니다.](./media/tutorial-discover-physical/azure-account-access.png)

6. **역할 할당 추가**에서 기여자 또는 소유자 역할을 선택하고 계정(이 예제에서는 azmigrateuser)을 선택합니다. 그런 다음 **Save**를 클릭합니다.

    ![계정에 역할을 할당하는 역할 할당 추가 페이지를 엽니다.](./media/tutorial-discover-physical/assign-role.png)

7. 포털에서 사용자를 검색하고 **서비스**에서 **사용자**를 선택합니다.
8. **사용자 설정**에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예**로 설정됨).

    ![사용자 설정에서 사용자가 Active Directory 앱을 등록할 수 있는지 확인합니다.](./media/tutorial-discover-physical/register-apps.png)

9. 또는 테넌트/전역 관리자가 **애플리케이션 개발자** 역할을 계정에 할당하여 AAD 앱 등록을 허용할 수 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-physical-servers"></a>물리적 서버 준비

어플라이언스가 물리적 서버에 액세스하는 데 사용할 수 있는 계정을 설정합니다.

- Windows 서버의 경우 검색에 포함할 모든 Windows 서버에 로컬 사용자 계정을 설정합니다. 사용자 계정을 - 원격 관리 사용자 - 성능 모니터 사용자 - 성능 로그 사용자 그룹에 추가합니다.
- Linux 서버의 경우, 검색하려는 Linux 서버의 루트 계정이 필요합니다. 또는 다음과 같이 액세스를 설정합니다.
    - setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk
    - setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk(/usr/sbin/fdisk가 없는 경우)<br/> - setcap "cap_dac_override, cap_dac_read_search, cap_fowner,cap_fsetid, cap_setuid, cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin, cap_sys_resource, cap_audit_control, cap_setfcap=+eip" /sbin/lvm
    - setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode chmod a+r /sys/class/dmi/id/product_uuid


## <a name="set-up-a-project"></a>프로젝트 설정

새 Azure Migrate 프로젝트를 설정합니다.

1. Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate**를 선택합니다.
3. **개요**에서 **프로젝트 만들기**를 선택합니다.
5. **프로젝트 만들기**에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보**에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

   ![프로젝트 이름 및 지역 상자](./media/tutorial-discover-physical/new-project.png)

7. **만들기**를 선택합니다.
8. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다.

**Azure Migrate : 서버 평가** 도구는 기본적으로 새 프로젝트에 추가됩니다.

![기본적으로 추가된 서버 평가 도구를 보여주는 페이지](./media/tutorial-discover-physical/added-tool.png)


## <a name="set-up-the-appliance"></a>어플라이언스 설정

어플라이언스를 설정하려면 다음을 수행합니다.
- 포털에서 어플라이언스 이름을 제공하고 Azure Migrate 프로젝트 키를 생성합니다.
- Azure Portal에서 Azure Migrate 설치 프로그램 스크립트가 포함된 압축 파일을 다운로드합니다.
- 압축 파일의 콘텐츠를 추출합니다. 관리자 권한으로 PowerShell 콘솔을 시작합니다.
- PowerShell 스크립트를 실행하여 어플라이언스 웹 애플리케이션을 시작합니다.
- 어플라이언스를 처음으로 구성하고 Azure Migrate 프로젝트 키를 사용하여 Azure Migrate 프로젝트에 등록합니다.

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate 프로젝트 키 생성

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 선택합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **물리적 또는 기타(AWS, GCP, Xen 등)** 를 선택합니다.
3. **1: Azure Migrate 프로젝트 키 생성**에서 물리적 또는 가상 서버 검색용으로 설정할 Azure Migrate 어플라이언스의 이름을 제공합니다. 이름은 14자 이하의 영숫자여야 합니다.
1. **키 생성**을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 [컴퓨터 검색] 페이지를 닫지 마세요.
1. Azure 리소스가 성공적으로 만들어지면 **Azure Migrate 프로젝트 키**가 생성됩니다.
1. 이 키는 구성 단계에서 어플라이언스 등록을 완료하는 데 필요하므로 복사해 둡니다.

### <a name="download-the-installer-script"></a>설치 프로그램 스크립트 다운로드

**2: Azure Migrate 어플라이언스 다운로드**에서 **다운로드**를 클릭합니다.


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 퍼블릭 클라우드의 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 정부 클라우드의 사용 예: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  최신 어플라이언스 버전 및 해시 값을 확인합니다.
    - 퍼블릭 클라우드의 경우:

        **시나리오** | **다운로드*** | **해시 값**
        --- | --- | ---
        물리적(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Azure Government의 경우:

        **시나리오** | **다운로드*** | **해시 값**
        --- | --- | ---
        물리적(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate 설치 프로그램 스크립트 실행
설치 프로그램 스크립트는 다음을 수행합니다.

- 물리적 서버 검색 및 평가를 위한 에이전트와 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 지정된 경로에 다음 파일을 만듭니다.
    - **구성 파일**: %Programdata%\Microsoft Azure\Config
    - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 다음과 같이 실행합니다.

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다.  기존 Azure Migrate 어플라이언스의 머신에서 스크립트를 실행하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 압축 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음 명령을 실행하여 **AzureMigrateInstaller.ps1**이라는 스크립트를 실행합니다.

    - 퍼블릭 클라우드의 경우: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government의 경우: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    스크립트가 성공적으로 완료되면 어플라이언스 웹 애플리케이션이 시작됩니다.

문제가 발생하는 경우 문제 해결을 위해 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그에 액세스할 수 있습니다.



### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

1. 어플라이언스에 연결할 수 있는 모든 머신에서 브라우저를 열고, 어플라이언스 웹앱의 URL(**https://*어플라이언스 이름 또는 IP 주소*): 44368**)을 엽니다.

   또는 바탕 화면에서 앱 바로 가기를 클릭하여 앱을 열 수 있습니다.
2. **사용 조건**에 동의하고 타사 정보를 읽습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **연결**: 앱에서 서버가 인터넷에 액세스할 수 있는지 확인합니다. 서버에서 프록시를 사용하는 경우:
        - **프록시 설정**을 클릭하고 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 포트를 지정합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
        - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장**을 클릭하여 연결 확인을 다시 트리거합니다.
    - **시간 동기화**: 시간이 확인됩니다. 서버 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate 서버 평가는 어플라이언스에 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기**를 클릭하여 어플라이언스에서 실행 중인 구성 요소의 상태와 버전을 확인할 수 있습니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **Azure Migrate 프로젝트 키**를 붙여넣습니다. 키가 없는 경우 **서버 평가 > 검색 > 기존 어플라이언스 관리**로 차례로 이동하여 키 생성 시 제공한 어플라이언스 이름을 선택하고, 해당 키를 복사합니다.
1. **로그인**을 클릭합니다. 그러면 새 브라우저 탭에서 Azure 로그인 프롬프트가 열립니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다. 
4. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 [권한]()이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기**를 클릭하여 등록 세부 정보를 확인할 수 있습니다.


## <a name="start-continuous-discovery"></a>연속 검색 시작

이제 어플라이언스에서 검색할 물리적 서버에 연결하여 검색을 시작합니다.

1. **1단계: Windows 및 Linux 물리적 서버 또는 가상 서버를 검색하는 데 필요한 자격 증명 제공**에서 **자격 증명 추가**를 클릭하여 자격 증명의 식별 이름을 지정하고 Windows 또는 Linux 서버의 **사용자 이름** 및 **암호**를 추가합니다. **Save**를 클릭합니다.
1. 여러 자격 증명을 한 번에 추가하려면 **더 추가**를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다. 물리적 서버 검색에 여러 자격 증명이 지원됩니다.
1. **2단계: 물리적 또는 가상 서버 세부 정보 제공**에서 **검색 원본 추가**를 클릭하여 서버 **IP 주소/FQDN**을 지정하고 서버에 연결할 자격 증명의 식별 이름을 지정합니다.
1. 한 번에 하나씩 **단일 항목을 추가**하거나 한꺼번에 **여러 항목을 추가**할 수 있습니다. 또한 **CSV 가져오기**를 통해 서버 세부 정보를 제공하는 옵션도 있습니다.


    - **단일 항목 추가**를 선택하는 경우 OS 유형을 선택하고, 자격 증명의 식별 이름을 지정하고, 서버 **IP 주소/FQDN**을 추가하고 **저장**을 클릭합니다.
    - **여러 항목 추가**를 선택하는 경우 텍스트 상자에 자격 증명의 식별 이름과 서버 **IP 주소/FQDN**을 지정하여 여러 레코드를 한 번에 추가할 수 있습니다. 추가된 레코드를 **확인**하고 **저장**을 클릭하세요.
    - **CSV가져오기**를 선택하는 경우 _(기본적으로 선택됨)_ CSV 템플릿 파일을 다운로드하고 서버 **IP 주소/FQDN** 및 자격 증명 식별 이름으로 파일을 채울 수 있습니다. 그런 다음, 파일을 어플라이언스로 가져와 파일의 레코드를 **확인**하고 **저장**을 클릭합니다.

1. 저장을 클릭하면 어플라이언스가 추가된 서버에 대한 연결의 유효성을 검사하고 각 서버에 대한 테이블에 **유효성 검사 상태**를 표시합니다.
    - 서버에 대한 유효성 검사가 실패하면 테이블의 상태 열에서 **유효성 검사 실패**를 클릭하여 오류를 검토합니다. 문제를 해결하고, 유효성을 다시 검사합니다.
    - 서버를 제거하려면 **삭제**를 클릭합니다.
1. 검색을 시작하기 전에 언제든지 서버에 대한 연결의 **유효성을 다시 검사**할 수 있습니다.
1. **검색 시작**을 클릭하여 유효성 검사에 성공한 서버의 검색을 시작합니다. 검색이 성공적으로 시작되었으면 테이블의 각 서버에 대한 검색 상태를 확인할 수 있습니다.


그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 서버당 약 2분이 걸립니다.

## <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색이 완료되면 서버가 포털에 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버**의 수를 표시하는 아이콘을 클릭합니다.
## <a name="next-steps"></a>다음 단계

- Azure VM으로 마이그레이션할 [물리적 서버를 평가](tutorial-assess-physical.md)합니다.
- 어플라이언스가 검색 중에 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---physical)합니다.