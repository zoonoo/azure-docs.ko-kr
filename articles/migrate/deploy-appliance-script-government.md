---
title: Azure 정부에서 Azure 마이그레이션 어플라이언스 설정
description: Azure 정부에서 Azure 마이그레이션 어플라이언스를 설정하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726736"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure 정부에서 어플라이언스 설정 

이 문서에 따라 Azure 정부 클라우드에서 VMware VM, Hyper-VM 및 물리적 서버에 대한 [Azure 마이그레이션 어플라이언스를](deploy-appliance.md) 배포합니다. 스크립트를 실행하여 어플라이언스를 만들고 Azure에 연결할 수 있는지 확인합니다. 퍼블릭 클라우드에서 어플라이언스를 설정하려면 [이 문서를](deploy-appliance-script.md)따르십시오.


> [!NOTE]
> 템플릿(VMware VM 및 Hyper-VM)에 대한 템플릿을 사용하여 어플라이언스를 배포하는 옵션은 Azure 정부에서 지원되지 않습니다.


## <a name="prerequisites"></a>사전 요구 사항

스크립트는 기존 물리적 컴퓨터 또는 VM에서 Azure 마이그레이션 어플라이언스를 설정합니다.

- 어플라이언스 역할을 하는 컴퓨터는 32GB메모리, 8개의 vCPU, 약 80GB의 디스크 스토리지 및 외부 가상 스위치를 갖춘 Windows Server 2016을 실행해야 합니다. 정적 또는 동적 IP 주소가 필요하며 인터넷에 액세스해야 합니다.
- 어플라이언스를 배포하기 전에 [VMware VM,](migrate-appliance.md#appliance---vmware) [Hyper-VM](migrate-appliance.md#appliance---hyper-v)및 물리적 서버에 대한 자세한 어플라이언스 요구 사항을 [검토합니다.](migrate-appliance.md#appliance---physical)
- 기존 Azure 마이그레이션 어플라이언스에서 스크립트를 실행하지 마십시오.

## <a name="set-up-the-appliance-for-vmware"></a>VMware용 어플라이언스 설정

VMware에 대한 어플라이언스를 설정하려면 Azure 포털에서 압축된 파일을 다운로드하고 내용을 추출합니다. PowerShell 스크립트를 실행하여 어플라이언스 웹 앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음 Azure 마이그레이션 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭합니다.
2.  **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, VMWare vSphere 하이퍼바이저 사용**을 선택합니다.
3.  압축된 파일을 다운로드하려면 **다운로드를**클릭합니다. 


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. 생성된 해시 값을 확인합니다. 최신 어플라이언스 버전의 경우:

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | 6316bcc8bc9322204295bfe33f4be3949
          

### <a name="run-the-script"></a>스크립트 실행

스크립트의 수행 은 다음과 같습니다.

- 에이전트 및 웹 응용 프로그램을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재원할 수 있는 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure 마이그레이션에 대한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\마이크로소프트 Azure\Config
    - **로그 파일**: %ProgramData%\마이크로소프트 Azure\로그

스크립트를 실행하려면

1. 압축된 파일을 어플라이언스를 호스트하는 컴퓨터의 폴더로 추출합니다. 기존 Azure 마이그레이션 어플라이언스에서 컴퓨터에서 스크립트를 실행하지 않도록 합니다.
2. 관리자(승격된) 권한으로 컴퓨터에서 PowerShell을 시작합니다.
3. PowerShell 디렉터리를 다운로드한 압축 파일에서 추출한 내용이 포함된 폴더로 변경합니다.
4. 다음과 같이 **스크립트를 실행합니다.**``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. 스크립트가 성공적으로 실행되면 어플라이언스 웹 응용 프로그램을 실행하여 어플라이언스를 설정할 수 있습니다. 문제가 발생하면 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>타임스탬프</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스가 [정부 클라우드에](migrate-appliance.md#government-cloud-urls)대한 Azure URL에 연결할 수 있는지 확인합니다.


## <a name="set-up-the-appliance-for-hyper-v"></a>하이퍼-V용 어플라이언스 설정

Hyper-V에 대한 어플라이언스를 설정하려면 Azure 포털에서 압축된 파일을 다운로드하고 내용을 추출합니다. PowerShell 스크립트를 실행하여 어플라이언스 웹 앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음 Azure 마이그레이션 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭합니다.
2.  **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 선택합니다.
3.  압축된 파일을 다운로드하려면 **다운로드를**클릭합니다. 


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. 생성된 해시 값을 확인합니다. 최신 어플라이언스 버전의 경우:

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>스크립트 실행

스크립트의 수행 은 다음과 같습니다.

- 에이전트 및 웹 응용 프로그램을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재원할 수 있는 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure 마이그레이션에 대한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\마이크로소프트 Azure\Config
    - **로그 파일**: %ProgramData%\마이크로소프트 Azure\로그

스크립트를 실행하려면

1. 압축된 파일을 어플라이언스를 호스트하는 컴퓨터의 폴더로 추출합니다. 기존 Azure 마이그레이션 어플라이언스에서 컴퓨터에서 스크립트를 실행하지 않도록 합니다.
2. 관리자(승격된) 권한으로 컴퓨터에서 PowerShell을 시작합니다.
3. PowerShell 디렉터리를 다운로드한 압축 파일에서 추출한 내용이 포함된 폴더로 변경합니다.
4. 다음과 같이 **스크립트를 실행합니다.**``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. 스크립트가 성공적으로 실행되면 어플라이언스 웹 응용 프로그램을 실행하여 어플라이언스를 설정할 수 있습니다. 문제가 발생하면 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>타임스탬프</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스가 [정부 클라우드에](migrate-appliance.md#government-cloud-urls)대한 Azure URL에 연결할 수 있는지 확인합니다.


## <a name="set-up-the-appliance-for-physical-servers"></a>물리적 서버에 대한 어플라이언스 설정

VMware에 대한 어플라이언스를 설정하려면 Azure 포털에서 압축된 파일을 다운로드하고 내용을 추출합니다. PowerShell 스크립트를 실행하여 어플라이언스 웹 앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음 Azure 마이그레이션 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭합니다.
2.  **검색 컴퓨터에서** > **컴퓨터가 가상화되어 있습니까?** **Not virtualized/Other**
3.  압축된 파일을 다운로드하려면 **다운로드를**클릭합니다. 


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. 생성된 해시 값을 확인합니다. 최신 어플라이언스 버전의 경우:

    **알고리즘** | **해시 값**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>스크립트 실행

스크립트의 수행 은 다음과 같습니다.

- 에이전트 및 웹 응용 프로그램을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재원할 수 있는 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure 마이그레이션에 대한 영구 설정을 사용 하 여 레지스트리 키 (HKLM)를 업데이트 합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\마이크로소프트 Azure\Config
    - **로그 파일**: %ProgramData%\마이크로소프트 Azure\로그

스크립트를 실행하려면

1. 압축된 파일을 어플라이언스를 호스트하는 컴퓨터의 폴더로 추출합니다. 기존 Azure 마이그레이션 어플라이언스에서 컴퓨터에서 스크립트를 실행하지 않도록 합니다.
2. 관리자(승격된) 권한으로 컴퓨터에서 PowerShell을 시작합니다.
3. PowerShell 디렉터리를 다운로드한 압축 파일에서 추출한 내용이 포함된 폴더로 변경합니다.
4. 다음과 같이 **스크립트를 실행합니다.**``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. 스크립트가 성공적으로 실행되면 어플라이언스 웹 응용 프로그램을 실행하여 어플라이언스를 설정할 수 있습니다. 문제가 발생하면 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>타임스탬프</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스가 [정부 클라우드에](migrate-appliance.md#government-cloud-urls)대한 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후 처음으로 구성하고 Azure Migrate 프로젝트에 등록해야 합니다.

- [VMware에](how-to-set-up-appliance-vmware.md#configure-the-appliance)대한 어플라이언스를 설정합니다.
- [하이퍼-V에](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)대한 어플라이언스를 설정합니다.
- 물리적 서버에 대한 어플라이언스를 [설정합니다.](how-to-set-up-appliance-physical.md)