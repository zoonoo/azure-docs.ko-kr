---
title: 에이전트 없는 VMware 마이그레이션을 위한 Azure Migrate 스케일 아웃 어플라이언스 설정
description: Hyper-v Vm을 마이그레이션하기 위해 Azure Migrate 스케일 아웃 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 634eb2d22e3fa570ac9412d4fb8afd917b5c2eaa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564010"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>Azure로 VMware 가상 컴퓨터의 에이전트 없는 마이그레이션 확장

이 문서에서는 VMware Vm의 마이그레이션에 대 한 Azure Migrate 서버 마이그레이션 도구의 에이전트 없는 메서드를 사용 하 여 확장 어플라이언스를 사용 하 여 많은 수의 VMware Vm (가상 머신)을 Azure로 마이그레이션하는 방법을 이해 하는 데 도움을 줍니다.

VMware 가상 컴퓨터에 대해 에이전트 없는 마이그레이션 방법을 사용 하면 다음을 수행할 수 있습니다.

- Azure Migrate 어플라이언스 하나를 사용 하 여 단일 vCenter 서버에서 동시에 최대 300 Vm을 복제 합니다.
- 마이그레이션을 위해 두 번째 스케일 아웃 어플라이언스를 배포 하 여 단일 vCenter 서버에서 동시에 최대 500 Vm을 복제 합니다.

이 아티클에서는 다음 방법을 설명합니다.

- VMware 가상 컴퓨터의 에이전트 없는 마이그레이션을 위한 스케일 아웃 어플라이언스 추가
- 스케일 아웃 어플라이언스를 사용 하 여 동시에 최대 500 Vm을 마이그레이션합니다.

##  <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음 단계를 수행 해야 합니다.

- Azure Migrate 프로젝트를 만듭니다.
- Azure Migrate 어플라이언스 (기본 어플라이언스)를 배포 하 고 vCenter 서버에서 관리 하는 VMware 가상 컴퓨터의 전체 검색을 수행 합니다.
- 마이그레이션할 하나 이상의 가상 컴퓨터에 대 한 복제를 구성 합니다.
> [!IMPORTANT]
> 마이그레이션을 위해 스케일 아웃 어플라이언스를 추가 하려면 먼저 프로젝트에 복제 가상 컴퓨터가 하나 이상 있어야 합니다.

위의 단계를 수행 하는 방법에 대 한 자세한 내용은 [에이전트 없는 마이그레이션 방법으로 VMware 가상 머신을 Azure로 마이그레이션](./tutorial-migrate-vmware.md)자습서를 참조 하세요.

## <a name="deploy-a-scale-out-appliance"></a>스케일 아웃 어플라이언스 배포

스케일 아웃 어플라이언스를 추가 하려면 아래에 설명 된 단계를 따르세요.

1. **검색** 을 클릭  >  **하면 컴퓨터가 가상화 되었습니까?** 
1. **VMware VSphere 하이퍼바이저를 사용 하 여 예를 선택 합니다.**
1. 다음 단계에서 에이전트 없는 복제를 선택 합니다.
1. 어플라이언스 유형 선택 메뉴에서 **기존 기본 어플라이언스 확장** 을 선택 합니다.
1. 스케일 아웃 하려는 기본 어플라이언스 (검색을 사용 하는 어플라이언스)를 선택 합니다.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="수평 확장 등록을 위한 컴퓨터 검색 페이지":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure Migrate 프로젝트 키 생성

1. **Azure Migrate 프로젝트 키 생성** 에서 스케일 아웃 어플라이언스에 대 한 접미사 이름을 제공 합니다. 접미사는 영숫자 문자만 포함할 수 있으며 길이는 14 자로 제한 됩니다.
2. **키 생성** 을 클릭 하 여 필요한 Azure 리소스 만들기를 시작 합니다. 리소스를 만드는 동안 [검색] 페이지를 닫지 마세요.
3. 생성 된 키를 복사 합니다. 확장 어플라이언스 등록을 완료 하려면 나중에 키가 필요 합니다.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. 스케일 아웃 어플라이언스에 대 한 설치 관리자 다운로드

**다운로드 Azure Migrate 어플라이언스** 에서 **다운로드** 를 클릭 합니다. PowerShell 설치 관리자 스크립트를 다운로드 하 여 Windows Server 2016를 실행 하는 기존 서버에 확장 어플라이언스를 배포 하 고, 필수 하드웨어 구성 (32 GB RAM, 8 개 vCPUs, 80 g b의 디스크 저장소 및 인터넷 액세스에 직접 또는 프록시를 통해)을 배포 해야 합니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="스케일 아웃 어플라이언스 용 스크립트 다운로드":::

> [!TIP]
> 다음 단계를 사용 하 여 다운로드 한 zip 파일에 대 한 체크섬의 유효성을 검사할 수 있습니다.
>
> 1. 관리자 권한으로 명령 프롬프트 열기
> 2. 다음 명령을 실행하여 압축된 파일의 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 퍼블릭 클라우드의 사용 예: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. 계산 된 해시 값이이 문자열과 일치 하지 않는 경우 포털에서 스케일 아웃 어플라이언스 설치 관리자의 최신 버전을 다운로드 합니다. e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure Migrate 설치 프로그램 스크립트 실행
설치 프로그램 스크립트는 다음을 수행합니다.

- 더 많은 동시 서버 복제를 수행 하도록 게이트웨이 에이전트 및 어플라이언스 구성 관리자를 설치 합니다.
- Windows 정품 인증 서비스, IIS 및 PowerShell ISE를 비롯한 Windows 역할을 설치합니다.
- IIS 재작성 모듈을 다운로드하여 설치합니다. [자세히 알아보기](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure Migrate에 대한 영구적인 설정 세부 정보를 사용하여 레지스트리 키(HKLM)를 업데이트합니다.
- 지정된 경로에 다음 파일을 만듭니다.
    - **구성 파일**: %Programdata%\Microsoft Azure\Config
    - **로그 파일**: %Programdata%\Microsoft Azure\Logs

스크립트를 다음과 같이 실행합니다.

1. 스케일 아웃 어플라이언스를 호스트 하는 서버의 폴더에 zip 파일을 추출 합니다.  기존 Azure Migrate 어플라이언스를 사용 하는 서버에서 스크립트를 실행 하지 않아야 합니다.
2. 위 서버에서 관리자(상승된) 권한을 사용하여 PowerShell을 시작합니다.
3. 다운로드 한 zip 파일에서 콘텐츠를 추출한 폴더로 PowerShell 디렉터리를 변경 합니다.
4. 다음 명령을 사용 하 여 **AzureMigrateInstaller.ps1**  이라는 스크립트를 실행 합니다.

    - 퍼블릭 클라우드의 경우: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    스크립트가 실행을 완료 하면 어플라이언스 구성 관리자를 시작 합니다.

문제가 발생 하는 경우 다음 위치에서 스크립트 로그에 액세스할 수 있습니다. <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_ 문제 해결을 위한<em>로그입니다.</em>


### <a name="4-configure-the-appliance"></a>4. 어플라이언스 구성

시작 하기 전에 [이러한 Azure 끝점](migrate-appliance.md#public-cloud-urls) 을 스케일 아웃 어플라이언스에서 액세스할 수 있는지 확인 합니다.

- 스케일 아웃 어플라이언스 서버에 연결할 수 있는 모든 컴퓨터에서 브라우저를 열고 어플라이언스 구성 관리자: **https://*확장 어플라이언스 이름 또는 IP 주소*: 44368** 의 URL을 엽니다.

   또는 구성 관리자의 바로 가기를 사용 하 여 스케일 아웃 어플라이언스 서버의 바탕 화면에서 구성 관리자를 열 수 있습니다.
- **사용 조건** 에 동의하고 타사 정보를 읽습니다.
- 구성 관리자 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
   - **연결**: 어플라이언스가 서버의 인터넷 연결을 확인합니다. 서버에서 프록시를 사용하는 경우:
     1. **프록시 설정** 을 클릭하여 프록시 주소(http://ProxyIPAddress 또는 http://ProxyFQDN) 형식) 및 수신 대기 포트를 지정합니다.
     2. 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     3. HTTP 프록시만 지원됩니다.
     4. 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
   - **시간 동기화**: 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
   - **업데이트 설치**: 어플라이언스에서 최신 업데이트가 설치되어 있는지 확인합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스 서버에서 실행되는 서비스의 상태와 버전을 확인할 수 있습니다.
   - **VDDK 설치**: 어플라이언스에서 VMware vSphere VDDK(Virtual Disk Development Kit)가 설치되어 있는지 확인합니다. 설치 되지 않은 경우 VDDK 6.7를 VMware에서 다운로드 하 고, 어플라이언스의 지정 된 위치에 다운로드 한 zip 콘텐츠를 추출 합니다 (어플라이언스 Configuration Manager 화면의 **설치 지침 참조** ).


### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **Azure Migrate 프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **서버 평가>> 기존 어플라이언스 관리를 검색** 하 고, 기본 어플라이언스 이름을 선택 하 고, 연결 된 확장 어플라이언스를 찾아 해당 키를 복사 합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="디바이스 코드를 보여주는 모달":::

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 디바이스 코드를 붙여넣고 Azure 사용자 이름과 암호를 사용하여 로그인합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 로그인 탭을 실수로 로그인하지 않고 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 로그인 단추를 다시 사용하도록 설정해야 합니다.
1. 성공적으로 로그인 한 후에는 이전 탭으로 돌아가서 어플라이언스 구성 관리자를 사용 합니다.
1. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 권한이 있는 경우 어플라이언스 등록이 시작됩니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="어플라이언스 구성 관리자의 패널 2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>기본 어플라이언스에서 어플라이언스 구성 가져오기

스케일 아웃 어플라이언스 등록을 완료 하려면 **가져오기** 를 클릭 하 여 기본 어플라이언스에서 필요한 구성 파일을 가져옵니다.

1. **가져오기** 를 클릭 하면 기본 어플라이언스에서 필요한 구성 파일을 가져오는 방법에 대 한 지침이 포함 된 팝업 창이 열립니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="구성 가져오기 모달":::
1. 기본 어플라이언스에 로그인 (원격 데스크톱) 하 고 다음 PowerShell 명령을 실행 합니다.

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. 위의 명령을 실행 하 여 만든 zip 파일을 스케일 아웃 어플라이언스로 복사 합니다. Zip 파일에는 스케일 아웃 어플라이언스를 등록 하는 데 필요한 구성 파일이 포함 되어 있습니다.
1. 이전 단계에서 열린 팝업 창에서 복사 된 구성 zip 파일의 위치를 선택 하 고 **저장** 을 클릭 합니다.

파일을 성공적으로 가져오면 스케일 아웃 어플라이언스의 등록이 완료 되 고 마지막으로 성공한 가져오기의 타임 스탬프가 표시 됩니다. **자세히 보기** 를 클릭 하 여 등록 세부 정보를 볼 수도 있습니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="스크린샷 Azure Migrate 프로젝트를 사용 하는 스케일 아웃 어플라이언스 등록을 보여 줍니다.":::

이 시점에서 확장 기기가 vCenter 서버에 연결할 수 있는지 유효성을 다시 검사 해야 합니다. **유효성** 재검사를 클릭 하 여 스케일 아웃 어플라이언스에서 vCenter Server 연결의 유효성을 검사 합니다.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="유효성을 검사할 보기 자격 증명과 검색 원본을 보여 주는 스크린샷":::

> [!IMPORTANT]
> 기본 어플라이언스에서 vCenter Server 자격 증명을 편집 하는 경우 구성 파일을 스케일 아웃 어플라이언스로 다시 가져와서 최신 구성을 가져오고 지속적인 복제를 계속 합니다.<br/> 규모 확장 기기가 더 이상 필요 하지 않은 경우 스케일 아웃 어플라이언스를 사용 하지 않도록 설정 해야 합니다. 필요 하지 않은 경우 스케일 아웃 어플라이언스를 사용 하지 않도록 설정 하는 방법에 [**대해 자세히 알아보세요**](./common-questions-appliance.md) .

## <a name="replicate"></a>복제

1. 스케일 아웃 어플라이언스를 등록 한 후 Azure Migrate: 서버 마이그레이션 타일에서 **복제** 를 클릭 합니다.

2.  화면의 단계를 따라 더 많은 가상 컴퓨터 복제를 시작 합니다. 

이제 스케일 아웃 어플라이언스를 사용 하 여 500 Vm을 동시에 복제할 수 있습니다. Azure Portal를 통해 200의 일괄 처리로 Vm을 마이그레이션할 수도 있습니다.

Azure Migrate 서버 마이그레이션 도구는 복제를 위해 기본 및 확장 어플라이언스 사이에 가상 컴퓨터를 배포 하는 과정을 처리 합니다. 복제가 완료 되 면 가상 컴퓨터를 마이그레이션할 수 있습니다.

> [!TIP]
> 많은 수의 가상 컴퓨터를 마이그레이션하려는 경우 최적의 성능을 위해 200의 일괄 처리로 가상 컴퓨터를 마이그레이션하는 것이 좋습니다.
  
## <a name="next-steps"></a>다음 단계

이 아티클에서는 다음에 대해 알아보았습니다.
- 스케일 아웃 어플라이언스를 구성 하는 방법
- 스케일 아웃 어플라이언스를 사용 하 여 Vm을 복제 하는 방법


Azure Migrate: 서버 마이그레이션 도구를 사용 하 여 서버를 Azure로 마이그레이션하는 방법에 [대해 자세히 알아보세요](./tutorial-migrate-vmware.md) .