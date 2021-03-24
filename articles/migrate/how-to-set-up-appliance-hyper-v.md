---
title: Hyper-v에 대 한 Azure Migrate 어플라이언스 설정
description: Hyper-v에서 서버를 평가 하 고 마이그레이션하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 71fe30212b31e810bfe3e1ba10f80be6b09ad4fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863686"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Hyper-v에서 서버에 대 한 어플라이언스 설정

이 문서에 따라 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) 도구를 사용 하 여 hyper-v에서 서버를 검색 하 고 평가 하는 Azure Migrate 어플라이언스를 설정 합니다.

[Azure Migrate 어플라이언스](migrate-appliance.md) 는 Azure Migrate: 검색 및 평가/마이그레이션과 hyper-v에서 온-프레미스 서버를 검색 하 고 서버 메타 데이터/성능 데이터를 Azure에 전송 하는 데 사용 되는 경량 어플라이언스입니다.

몇 가지 방법을 사용 하 여 어플라이언스를 배포할 수 있습니다.

- 다운로드 한 VHD를 사용 하 여 Hyper-v의 서버에 설정 합니다. 이 문서에서 설명 하는 방법입니다.
- PowerShell 설치 관리자 스크립트를 사용 하 여 Hyper-v 또는 물리적 서버의 서버에 설정 합니다. VHD를 사용 하 여 서버를 설정할 수 없거나 Azure Government 중인 경우 [이 방법을](deploy-appliance-script.md) 사용 해야 합니다.

어플라이언스를 만든 후에 Azure Migrate: 검색 및 평가에 연결할 수 있는지 확인 하 고, 처음으로 구성 하 고, 프로젝트에 등록 합니다.

## <a name="appliance-deployment-vhd"></a>어플라이언스 배포 (VHD)

VHD 템플릿을 사용 하 여 어플라이언스를 설정 하려면:

- 포털에서 어플라이언스 이름을 제공 하 고 프로젝트 키를 생성 합니다.
- Azure Portal에서 압축된 Hyper-V VHD를 다운로드합니다.
- 어플라이언스를 만들고 Azure Migrate: 검색 및 평가에 연결할 수 있는지 확인 합니다.
- 처음으로 어플라이언스를 구성 하 고 프로젝트 키를 사용 하 여 프로젝트에 등록 합니다.

### <a name="generate-the-project-key"></a>프로젝트 키 생성

1. **마이그레이션 목표**  >  **Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 에서 **검색** 을 선택 합니다.
2. 서버 를  >  **가상화 하는** 서버 검색에서 **예, hyper-v 사용** 을 선택 합니다.
3. **1: 프로젝트 키 생성** 에서 hyper-v의 서버를 검색 하는 데 설정할 Azure Migrate 어플라이언스의 이름을 제공 합니다. 이름은 14 자 이하의 영숫자여야 합니다.
1. **키 생성** 을 클릭하여 필요한 Azure 리소스 만들기를 시작합니다. 리소스를 만드는 동안 서버 검색 페이지를 닫지 마십시오.
1. Azure 리소스를 성공적으로 만든 후에는 **프로젝트 키** 가 생성 됩니다.
1. 어플라이언스를 구성하는 동안 어플라이언스 등록을 완료하는 데 필요하므로 키를 복사합니다.

### <a name="download-the-vhd"></a>VHD 다운로드

**2: Azure Migrate 어플라이언스 다운로드** 에서 .VHD 파일을 선택하고, **다운로드** 를 클릭합니다.

   ![검색 서버에 대 한 선택 항목](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![키 생성 선택](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 서버에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 VHD에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance"></a>어플라이언스 만들기

다운로드 한 파일을 가져오고 어플라이언스를 만듭니다.

1. 어플라이언스를 호스트할 Hyper-v 호스트의 폴더에 압축 된 VHD 파일을 추출 합니다. 세 개의 폴더가 추출됩니다.
2. Hyper-V 관리자를 엽니다. **작업** 에서 **Virtual Machine 가져오기** 를 클릭합니다.

    ![VHD 배포](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Virtual Machine 가져오기 마법사 > **시작하기 전에** 에서 **다음** 을 클릭합니다.
3. **폴더 찾기** 에서 추출된 VHD가 포함된 폴더를 지정합니다. 그런 후 **Next** 를 클릭합니다.
1. **Virtual Machine 선택** 에서 **다음** 을 클릭합니다.
2. **가져오기 유형 선택** 에서 **가상 머신 복사(새로운 고유 ID 만들기)** 를 클릭합니다. 그런 후 **Next** 를 클릭합니다.
3. **대상 선택** 에서 기본 설정을 그대로 둡니다. **다음** 을 클릭합니다.
4. **스토리지 폴더** 에서 기본 설정을 그대로 둡니다. **다음** 을 클릭합니다.
5. **네트워크 선택** 에서 서버가 사용할 가상 스위치를 지정 합니다. 이 스위치에는 데이터를 Azure로 보내기 위한 인터넷 연결이 필요합니다.
6. **요약** 에서 설정을 검토합니다. **마침** 을 클릭합니다.
7. Hyper-V 관리자 > **Virtual Machines** 에서 VM을 시작합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스에서 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드의 Azure URL에 연결할 수 있는지 확인합니다.

### <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

> [!NOTE]
> 다운로드한 VHD 대신 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 설정하는 경우 이 절차의 처음 두 단계는 관련이 없습니다.

1. Hyper-v 관리자 > **Virtual Machines** 에서 서버를 마우스 오른쪽 단추로 클릭 하 > **연결** 합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. 어플라이언스에 연결할 수 있는 모든 시스템에서 브라우저를 열고 어플라이언스 웹 앱의 URL ( **https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 클릭하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
1. **사용 조건** 에 동의하고 타사 정보를 읽습니다.
1. 웹앱 > **필수 구성 요소 설정** 에서 다음을 수행합니다.
    - **연결**: 앱에서 서버가 인터넷에 액세스할 수 있는지 확인합니다. 서버에서 프록시를 사용하는 경우:
      - **설정 프록시** 를 클릭 하 고 양식 http://ProxyIPAddress 또는 http://ProxyFQDN) 수신 대기 포트에서 프록시 주소를 지정 합니다.
      - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
      - HTTP 프록시만 지원됩니다.
      - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
    - **시간 동기화**: 시간이 확인됩니다. 서버 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate: 검색 및 평가는 어플라이언스에 최신 업데이트가 설치 되어 있는지 확인 합니다. 확인이 완료되면 **어플라이언스 서비스 보기** 를 클릭하여 어플라이언스에서 실행되는 구성 요소의 상태와 버전을 확인할 수 있습니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. 포털에서 복사한 **프로젝트 키** 를 붙여넣습니다. 키가 없는 경우 **Azure Migrate: 검색 및 평가> 검색 및 평가를 검색> 기존 어플라이언스를 검색** 하 고, 키 생성 시 제공한 어플라이언스 이름을 선택 하 고, 해당 키를 복사 합니다.
1. Azure로 인증하려면 디바이스 코드가 필요합니다. **로그인** 을 클릭하면 아래와 같이 디바이스 코드가 포함된 모달이 열립니다.

    ![디바이스 코드를 보여주는 모달](./media/tutorial-discover-vmware/device-code.png)

1. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
1. 새 탭에서 Azure 사용자 이름 및 암호를 사용 하 여 장치 코드를 붙여넣고 로그인 합니다.
   
   PIN을 사용한 로그인은 지원되지 않습니다.
3. 로그인 탭을 실수로 로그인하지 않고 닫은 경우에는 어플라이언스 구성 관리자의 브라우저 탭을 새로 고쳐 로그인 단추를 다시 사용하도록 설정해야 합니다.
1. 성공적으로 로그인한 후 어플라이언스 구성 관리자를 사용하여 이전 탭으로 돌아갑니다.
4. 로깅에 사용되는 Azure 사용자 계정에 키 생성 시 만든 Azure 리소스에 대한 올바른 [권한](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)이 있는 경우 어플라이언스 등록이 시작됩니다.
1. 어플라이언스가 성공적으로 등록되면 **세부 정보 보기** 를 클릭하여 등록 세부 정보를 확인할 수 있습니다.



### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD에 대한 자격 증명 위임

SMB에서 VHD를 실행하는 경우 자격 증명을 어플라이언스에서 Hyper-V 호스트로 위임하도록 설정해야 합니다. 어플라이언스에서 이 작업을 수행하려면 다음을 수행합니다.

1. 어플라이언스에서이 명령을 실행 합니다. HyperVHost1/HyperVHost2는 예제 호스트 이름입니다.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. 또는 어플라이언스의 로컬 그룹 정책 편집기에서 다음을 수행합니다.
    - **로컬 컴퓨터 정책** > **컴퓨터구성** 에서 **관리 템플릿** > **시스템** > **자격 증명 위임** 을 차례로 클릭합니다.
    - **새로운 자격 증명 위임 허용** 을 두 번 클릭하고 **사용** 을 선택합니다.
    - **옵션** 에서 **표시** 를 클릭하고, **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.
    - **자격 증명 위임** 에서 **서버 인증이 NTLM 전용일 경우 새로운 자격 증명 허용** 을 두 번 클릭합니다. **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.

## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스에서 Hyper-v 호스트 또는 클러스터로 연결 하 고 검색을 시작 합니다.

1. **1 단계: hyper-v 호스트 자격 증명 제공** 에서 자격 증명 **추가** 를 클릭 하 여 자격 증명에 대 한 이름을 지정 하 고, 어플라이언스에서 서버를 검색 하는 데 사용할 hyper-v 호스트/클러스터의 **사용자 이름** 및 **암호** 를 추가 합니다. **Save** 를 클릭합니다.
1. 여러 자격 증명을 한 번에 추가하려면 **추가** 를 클릭하여 더 많은 자격 증명을 저장하고 추가합니다. Hyper-v에서 서버를 검색 하는 데는 여러 자격 증명이 지원 됩니다.
1. **2단계: Hyper-V 호스트/클러스터 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 Hyper-V 호스트/클러스터 **IP 주소/FQDN** 및 호스트/클러스터에 연결할 자격 증명 이름을 지정합니다.
1. **단일 항목** 또는 **여러 항목** 을 한 번에 추가할 수 있습니다. 또한 **CSV 가져오기** 를 통해 Hyper-V 호스트/클러스터 세부 정보를 제공하는 옵션이 있습니다.

    ![검색 원본 추가 선택](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - **단일 항목 추가** 를 선택하는 경우 자격 증명 이름 및 Hyper-V 호스트/클러스터 **IP 주소/FQDN** 을 지정하고, **저장** 을 클릭해야 합니다.
    - **여러 항목 추가** _(기본적으로 선택 됨)_ 를 선택 하는 경우 텍스트 상자에 자격 증명의 이름을 지정 하 여 Hyper-v 호스트/클러스터 **IP 주소/FQDN** 을 지정 하 여 한 번에 여러 레코드를 추가할 수 있습니다. 추가 된 레코드를 확인 하 고 **저장** 을 클릭 합니다.
    - **CSV 가져오기** 를 선택하는 경우 CSV 템플릿 파일을 다운로드하고, 파일을 Hyper-V 호스트/클러스터 **IP 주소/FQDN** 및 자격 증명 이름으로 채울 수 있습니다. 그런 다음, 파일을 어플라이언스로 가져와서 파일의 레코드를 **확인** 하고 **저장** 을 클릭합니다.

1. [저장]을 클릭하면 어플라이언스에서 추가된 Hyper-V 호스트/클러스터에 대한 연결의 유효성을 검사하고, 각 호스트/클러스터에 대한 **유효성 검사 상태** 를 테이블에 표시합니다.
    - 호스트/클러스터의 유효성이 성공적으로 검사되면 해당 IP 주소/FQDN을 클릭하여 자세한 정보를 확인할 수 있습니다.
    - 호스트에 대한 유효성 검사가 실패하면 테이블의 [상태] 열에서 **유효성 검사 실패** 를 클릭하여 오류를 검토합니다. 문제를 해결하고 유효성을 다시 검사합니다.
    - 호스트 또는 클러스터를 제거하려면 **삭제** 를 클릭합니다.
    - 클러스터에서 특정 호스트를 제거할 수 없습니다. 전체 클러스터만 제거할 수 있습니다.
    - 클러스터의 특정 호스트에 문제가 있는 경우에도 클러스터를 추가할 수 있습니다.
1. 검색을 시작 하기 전에 언제 든 지 호스트/클러스터에 대 한 연결의 **유효성** 을 다시 검사할 수 있습니다.
1. **검색 시작** 을 클릭 하 여 성공적으로 확인 된 호스트/클러스터에서 서버 검색을 시작 합니다. 검색이 성공적으로 시작되면 테이블에서 각 호스트/클러스터에 대한 검색 상태를 확인할 수 있습니다.

그러면 검색을 시작합니다. 검색된 서버의 메타데이터가 Azure Portal에 표시되는 데 호스트당 약 2분이 걸립니다.

## <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색이 완료되면 서버가 포털에 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate-Windows, Linux 및 SQL server**  >  **Azure Migrate: 검색 및 평가** 페이지에서 **검색 된 서버의** 수를 표시 하는 아이콘을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

Azure Migrate: 검색 및 평가를 사용 하 여 [hyper-v 평가](tutorial-assess-hyper-v.md) 를 사용해 보세요.