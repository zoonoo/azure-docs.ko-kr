---
title: 스크립트를 사용하여 Azure Migrate 어플라이언스 설정
description: 스크립트를 사용하여 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: dfa96948b7e582457a9f09eed89d5cbe3bbc762d
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109750396"
---
# <a name="set-up-an-appliance-with-a-script"></a>스크립트를 사용하여 어플라이언스 설정

이 문서를 따라 VMware 및 Hyper-V에 있는 서버에 대한 평가/마이그레이션을 위한 [Azure Mirgrate 어플라이언스](./migrate-appliance-architecture.md)를 만들 수 있습니다. 스크립트를 실행하여 어플라이언스를 만들고 해당 어플라이언스를 Azure에 연결할 수 있는지 확인하게 됩니다. 

스크립트를 사용하거나 Azure Portal에서 다운로드한 템플릿을 사용하여 VMware 및 Hyper-V에 있는 서버에 대한 어플라이언스를 배포할 수 있습니다. 다운로드한 템플릿을 사용하여 어플라이언스를 만들 수 없는 경우 스크립트를 사용하는 것이 유용할 수 있습니다.

- 템플릿을 사용하려면 [VMware](./tutorial-discover-vmware.md) 또는 [Hyper-V](./tutorial-discover-hyper-v.md)에 대한 자습서를 따르세요.
- 물리적 서버용 어플라이언스를 설정하려면 스크립트만 사용할 수 있습니다. [이 문서](how-to-set-up-appliance-physical.md)를 따르세요.
- Azure Government 클라우드에서 어플라이언스를 설정하려면 [이 문서](deploy-appliance-script-government.md)를 따릅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 스크립트는 기존 서버에 Azure Migrate 어플라이언스를 설정합니다.

- 어플라이언스 역할을 하는 서버는 다음 하드웨어 및 OS 요구 사항을 충족해야 합니다.

시나리오 | 요구 사항
--- | ---
VMware | Windows Server 2016, 메모리 32GB, vCPU 8개, 디스크 스토리지 80GB
Hyper-V | Windows Server 2016, 메모리 16GB, vCPU 8개, 디스크 스토리지 80GB

- 서버에는 외부 가상 스위치도 필요합니다. 고정 또는 동적 IP 주소가 필요합니다. 
- 어플라이언스를 배포하기 전에 [VMware](migrate-appliance.md#appliance---vmware), [Hyper-V](migrate-appliance.md#appliance---hyper-v)에 있는 서버에 대한 자세한 어플라이언스 요구 사항을 검토합니다.
- 기존 Azure Migrate 어플라이언스에서 스크립트를 실행하지 않아야 합니다.

## <a name="set-up-the-appliance-for-vmware"></a>VMware용 어플라이언스 설정

VMware용 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2140334)에서 AzureMigrateInstaller-Server-Public.zip이라는 Zip 파일을 다운로드하고 콘텐츠를 추출합니다. PowerShell 스크립트를 실행하여 어플라이언스 웹앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음 프로젝트에 어플라이언스를 등록합니다.

### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Azure 퍼블릭 클라우드의 최신 어플라이언스 버전 및 스크립트를 확인합니다.

    **알고리즘** | **다운로드** | **SHA256**
    --- | --- | ---
    VMware(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>스크립트 실행

스크립트가 수행하는 작업은 다음과 같습니다.

- 에이전트 및 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS, PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다.
- Azure Migrate에 대한 영구적인 설정을 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스에서 스크립트를 실행하지 않아야 합니다.
2. 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 Zip 파일에서 추출한 콘텐츠를 포함하는 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 을 실행합니다.

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. 스크립트가 성공적으로 실행되면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 애플리케이션을 시작합니다. 문제가 발생하는 경우 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="set-up-the-appliance-for-hyper-v"></a>Hyper-V용 어플라이언스 설정

Hyper-V용 어플라이언스를 설정하려면 포털 또는 [여기](https://go.microsoft.com/fwlink/?linkid=2105112)에서 AzureMigrateInstaller-Server-Public.zip이라는 zip 파일을 다운로드하고 콘텐츠를 추출합니다. PowerShell 스크립트를 실행하여 어플라이언스 웹앱을 시작합니다. 어플라이언스를 설정하고 처음으로 구성합니다. 그런 다음 프로젝트에 어플라이언스를 등록합니다.


### <a name="verify-file-security"></a>파일 보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Azure 퍼블릭 클라우드의 최신 어플라이언스 버전 및 스크립트를 확인합니다.

    **시나리오** | **다운로드** | **SHA256**
    --- | --- | ---
    Hyper-V(85.8MB) | [최신 버전](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

### <a name="run-the-script"></a>스크립트 실행

스크립트가 수행하는 작업은 다음과 같습니다.

- 에이전트 및 웹 애플리케이션을 설치합니다.
- Windows 정품 인증 서비스, IIS, PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다.
- Azure Migrate에 대한 영구적인 설정을 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 다음과 같이 로그 및 구성 파일을 만듭니다.
    - **구성 파일**: %ProgramData%\Microsoft Azure\Config
    - **로그 파일**: %ProgramData%\Microsoft Azure\Logs

스크립트를 실행하려면

1. 어플라이언스를 호스팅할 서버의 폴더에 압축 파일을 추출합니다. 기존 Azure Migrate 어플라이언스에서 스크립트를 실행하지 않아야 합니다.
2. 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드한 Zip 파일에서 추출한 콘텐츠를 포함하는 폴더로 PowerShell 디렉터리를 변경합니다.
4. 다음과 같이 스크립트 **AzureMigrateInstaller.ps1** 을 실행합니다.

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. 스크립트가 성공적으로 실행되면 어플라이언스를 설정할 수 있도록 어플라이언스 웹 애플리케이션을 시작합니다. 문제가 발생하는 경우 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log에서 스크립트 로그를 검토합니다.

### <a name="verify-access"></a>액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

어플라이언스를 배포한 후에는 처음으로 구성해야 하며 프로젝트에 등록해야 합니다.

- [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)용 어플라이언스를 설정합니다.
- [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)용 어플라이언스를 설정합니다.
