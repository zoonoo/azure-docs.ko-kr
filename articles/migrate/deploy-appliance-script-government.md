---
title: Azure Government에서 Azure Migrate 어플라이언스 설정
description: Azure Government에서 Azure Migrate 어플라이언스를 설정하는 방법 알아보기
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775207"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Azure Government에서 어플라이언스 설정 

이 문서에 따라 VMware 환경의 서버, Hyper-V의 서버 및 물리적 서버에 대한 [Azure Migrate 어플라이언스](./migrate-appliance-architecture.md)를 Azure Government 클라우드에 배포합니다. 스크립트를 실행하여 어플라이언스를 만들고 해당 어플라이언스를 Azure에 연결할 수 있는지 확인하게 됩니다. 퍼블릭 클라우드에서 어플라이언스를 설정하려면 [이 문서](deploy-appliance-script.md)를 따릅니다.


> [!NOTE]
> 템플릿을 사용하여 어플라이언스를 배포하는 옵션(VMware 환경 및 Hyper-V 환경의 서버)은 Azure Government에서 지원되지 않습니다.


## <a name="prerequisites"></a>필수 구성 요소

이 스크립트는 기존 물리적 서버 또는 가상화된 서버에 Azure Migrate 어플라이언스를 설정합니다.

- 어플라이언스 역할을 하는 서버는 Windows Server 2016, 32GB 메모리, 8개의 vCPU, 80GB의 디스크 스토리지 및 외부 가상 스위치를 실행해야 합니다. 고정 또는 동적 IP 주소이 필요하며 인터넷에 액세스할 수 있어야 합니다.
- 어플라이언스를 배포하기 전에 [VMware](migrate-appliance.md#appliance---vmware) 또는 [Hyper-V](migrate-appliance.md#appliance---hyper-v) 및 [물리적 서버](migrate-appliance.md#appliance---physical)에 대한 자세한 어플라이언스 요구 사항을 검토합니다.
- 기존 Azure Migrate 어플라이언스에서 스크립트를 실행하지 않아야 합니다.

## <a name="set-up-the-appliance-for-vmware"></a>VMware용 어플라이언스 설정

VMware에 대한 어플라이언스를 설정하려면 Azure Portal에서 zip 파일을 다운로드하고 콘텐츠의 압축을 풉니다. PowerShell 스크립트를 실행하여 어플라이언스 웹앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음, 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다.
3. **다운로드** 를 클릭하여 압축 파일을 다운로드합니다.

### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **알고리즘** | **다운로드** | **SHA256**
    --- | --- | ---
    VMware(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>스크립트 실행

스크립트가 수행하는 작업은 다음과 같습니다.

- 에이전트 및 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정을 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스가 있는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 Zip 파일에서 추출한 콘텐츠를 포함하는 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 을 실행합니다.
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. 스크립트가 성공적으로 실행되면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 애플리케이션을 시작합니다. 문제가 발생하는 경우 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.


## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V용 어플라이언스 설정

Hyper-V에 대한 어플라이언스를 설정하려면 Azure Portal에서 zip 파일을 다운로드하고 콘텐츠의 압축을 풉니다. PowerShell 스크립트를 실행하여 어플라이언스 웹앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음, 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1.  **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2.  **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **예, Hyper-V 사용** 을 선택합니다.
3.  **다운로드** 를 클릭하여 압축 파일을 다운로드합니다. 


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **시나리오** | **다운로드** | **SHA256**
    --- | --- | ---
    Hyper-V(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>스크립트 실행

스크립트가 수행하는 작업은 다음과 같습니다.

- 에이전트 및 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정을 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스를 실행하는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 Zip 파일에서 추출한 콘텐츠를 포함하는 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 을 실행합니다. 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. 스크립트가 성공적으로 실행되면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 애플리케이션을 시작합니다. 문제가 발생하는 경우 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.


## <a name="set-up-the-appliance-for-physical-servers"></a>물리적 서버용 어플라이언스 설정

VMware에 대한 어플라이언스를 설정하려면 Azure Portal에서 zip 파일을 다운로드하고 콘텐츠의 압축을 풉니다. PowerShell 스크립트를 실행하여 어플라이언스 웹앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음, 프로젝트에 어플라이언스를 등록합니다.

### <a name="download-the-script"></a>스크립트 다운로드

1. **마이그레이션 목표** > **Windows, Linux 및 SQL 서버** > **Azure Migrate: 검색 및 평가** 에서 **검색** 을 클릭합니다.
2. **서버 검색** > **서버가 가상화되어 있습니까?** 에서 **가상화되지 않음/기타** 를 선택합니다.
3. **다운로드** 를 클릭하여 압축 파일을 다운로드합니다.

### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. 최신 어플라이언스 버전 및 해시 값을 확인합니다.

    **시나리오** | **다운로드** _ | _ *해시 값**
    --- | --- | ---
    물리적(85MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>스크립트 실행

스크립트가 수행하는 작업은 다음과 같습니다.

- 에이전트 및 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정을 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스를 실행하는 서버에서 스크립트를 실행하지 않아야 합니다.
2. 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 Zip 파일에서 추출한 콘텐츠를 포함하는 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 을 실행합니다.

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. 스크립트가 성공적으로 실행되면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 애플리케이션을 시작합니다. 문제가 발생하는 경우 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [정부 클라우드퍼블릭](migrate-appliance.md#government-cloud-urls)의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성해야 하며 프로젝트에 등록해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)용 어플라이언스를 설정합니다.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)용 어플라이언스를 설정합니다.
- [물리적 서버](how-to-set-up-appliance-physical.md)용 어플라이언스를 설정합니다.
