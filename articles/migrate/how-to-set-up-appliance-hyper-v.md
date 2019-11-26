---
title: Hyper-v에 대 한 Azure Migrate 어플라이언스 설정
description: Hyper-v Vm을 평가 하 고 마이그레이션하기 위해 Azure Migrate 어플라이언스를 설정 하는 방법에 대해 알아봅니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 1676ad3dde5a97d2afdb64595d51f5502f9dcc3d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185927"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Hyper-v Vm에 대 한 어플라이언스 설정

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 Hyper-v Vm을 평가 하는 경우 또는 Azure Migrate 서버 마이그레이션 도구를 사용 하 여 VMware Vm을 Azure로 마이그레이션하는 경우 Azure Migrate 어플라이언스를 설정 하는 방법을 설명 합니다.

Hyper-v VM 어플라이언스는 다음을 수행 하기 위해 Azure Migrate 서버 평가/마이그레이션에 사용 되는 경량 어플라이언스입니다.

- 온-프레미스 Hyper-v Vm을 검색 합니다.
- 검색 된 Vm에 대 한 메타 데이터 및 성능 데이터를 Azure Migrate 서버 평가/마이그레이션으로 보냅니다.

Azure Migrate 어플라이언스에 [대해 자세히 알아보세요](migrate-appliance.md) .


## <a name="appliance-deployment-steps"></a>어플라이언스 배포 단계

어플라이언스를 설정하려면 다음을 수행합니다.
- Azure Portal에서 압축된 Hyper-V VHD를 다운로드합니다.
- 어플라이언스를 만들고, Azure Migrate 서버 평가에 연결할 수 있는지 확인합니다.
- 어플라이언스를 처음으로 구성하고, Azure Migrate 프로젝트에 등록합니다.

## <a name="download-the-vhd"></a>VHD 다운로드

어플라이언스에 대한 압축된 VHD 템플릿을 다운로드합니다.

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가**에서 **검색**을 클릭 합니다.
2. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용**을 클릭합니다.
3. **다운로드**를 클릭하여 VHD 파일을 다운로드합니다.

    ![VM 다운로드](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>보안 확인

배포하기 전에 압축된 파일이 안전한지 확인합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. 다음 명령을 실행하여 VHD에 대한 해시를 생성합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  어플라이언스 버전 2.19.11.12의 경우 생성 된 해시가 이러한 설정과 일치 해야 합니다.

  **알고리즘** | **해시 값**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드한 파일을 가져오고 VM을 만듭니다.

1. 어플라이언스 VM을 호스팅할 Hyper-V 호스트의 폴더에 압축된 VHD 파일을 추출합니다. 세 개의 폴더가 추출됩니다.
2. Hyper-V 관리자를 엽니다. **작업**에서 **Virtual Machine 가져오기**를 클릭합니다.

    ![VHD 배포](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Virtual Machine 가져오기 마법사 > **시작하기 전에**에서 **다음**을 클릭합니다.
3. **폴더 찾기**에서 추출된 VHD가 포함된 폴더를 지정합니다. 그런 후 **다음**을 클릭합니다.
1. **Virtual Machine 선택**에서 **다음**을 클릭합니다.
2. **가져오기 유형 선택**에서 **가상 머신 복사(새로운 고유 ID 만들기)** 를 클릭합니다. 그런 후 **다음**을 클릭합니다.
3. **대상 선택**에서 기본 설정을 그대로 둡니다. **다음**을 누릅니다.
4. **스토리지 폴더**에서 기본 설정을 그대로 둡니다. **다음**을 누릅니다.
5. **네트워크 선택**에서 VM에서 사용할 가상 스위치를 지정합니다. 이 스위치에는 데이터를 Azure로 보내기 위한 인터넷 연결이 필요합니다.
6. **요약** 에서 설정을 검토합니다. **마침**을 클릭합니다.
7. Hyper-V 관리자 > **Virtual Machines**에서 VM을 시작합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure에 대한 어플라이언스 액세스 확인

어플라이언스 VM에서 [Azure URL](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)에 연결할 수 있는지 확인합니다.

## <a name="configure-the-appliance"></a>어플라이언스 구성

어플라이언스를 처음으로 설정합니다.

1. Hyper-V 관리자 > **Virtual Machines**에서 마우스 오른쪽 단추로 VM > **연결**을 클릭합니다.
2. 어플라이언스에 대한 언어, 표준 시간대 및 암호를 제공합니다.
3. VM에 연결할 수 있는 모든 컴퓨터에서 브라우저를 열고 어플라이언스 웹 앱의 URL ( **https://*어플라이언스 이름 또는 IP 주소*: 44368**)을 엽니다.

   또는 앱 바로 가기를 클릭하여 어플라이언스 데스크톱에서 앱을 열 수 있습니다.
1. 웹앱 > **필수 구성 요소 설정**에서 다음을 수행합니다.
    - **라이선스**: 사용 조건에 동의 하 고 타사 정보를 읽습니다.
    - **연결**: 앱에서 VM이 인터넷에 연결 되어 있는지 확인 합니다. VM에서 프록시를 사용하는 경우:
        - **프록시 설정**을 클릭하고, 프록시 주소와 수신 포트를 http://ProxyIPAddress 또는 http://ProxyFQDN 형식으로 지정합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
    - **시간 동기화**: 시간이 확인 됩니다. VM 검색이 제대로 작동하려면 어플라이언스의 시간이 인터넷 시간과 동기화되어야 합니다.
    - **업데이트 설치**: Azure Migrate 서버 평가는 어플라이언스에 최신 업데이트가 설치 되어 있는지 확인 합니다.

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate를 사용하여 어플라이언스 등록

1. **로그인**을 클릭합니다. 표시되지 않으면 브라우저에서 팝업 차단을 사용하지 않도록 설정했는지 확인합니다.
2. 새로 만들기 탭에서 Azure 자격 증명을 사용하여 로그인합니다.
    - 사용자 이름과 암호를 사용하여 로그인합니다.
    - PIN을 사용한 로그인은 지원되지 않습니다.
3. 성공적으로 로그인하면 웹앱으로 돌아갑니다.
4. Azure Migrate 프로젝트가 만들어진 구독을 선택합니다. 그런 다음, 해당 프로젝트를 선택합니다.
5. 어플라이언스의 이름을 지정합니다. 이름은 14자 이하의 영숫자여야 합니다.
6. **Register**를 클릭합니다.


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD에 대한 자격 증명 위임

SMB에서 VHD를 실행하는 경우 자격 증명을 어플라이언스에서 Hyper-V 호스트로 위임하도록 설정해야 합니다. 어플라이언스에서이 작업을 수행 하려면 다음을 수행 합니다.

1. 어플라이언스 VM에서 다음 명령을 실행합니다. HyperVHost1/HyperVHost2는 예제 호스트 이름입니다.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 또는 어플라이언스의 로컬 그룹 정책 편집기에서 다음을 수행합니다.
    - **로컬 컴퓨터 정책** > **컴퓨터구성**에서 **관리 템플릿** > **시스템** > **자격 증명 위임**을 차례로 클릭합니다.
    - **새로운 자격 증명 위임 허용**을 두 번 클릭하고 **사용**을 선택합니다.
    - **옵션**에서 **표시**를 클릭하고, **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.
    - **자격 증명 위임**에서 **서버 인증이 NTLM 전용일 경우 새로운 자격 증명 허용**을 두 번 클릭합니다. **wsman/** 을 접두사로 사용하여 검색하려는 각 Hyper-V 호스트를 목록에 추가합니다.

## <a name="start-continuous-discovery"></a>연속 검색 시작

어플라이언스에서 Hyper-V 호스트 또는 클러스터에 연결하고, VM 검색을 시작합니다.

1. **사용자 이름** 및 **암호**에서 어플라이언스가 VM을 검색하는 데 사용할 읽기 전용 계정 자격 증명을 지정합니다. 친숙한 자격 증명 이름을 지정하고, **세부 정보 저장**을 클릭합니다.
2. **호스트 추가**를 클릭하고, Hyper-V 호스트/클러스터 세부 정보를 지정합니다.
3. **유효성 검사**를 클릭합니다. 유효성 검사가 완료되면 각 호스트/클러스터에서 검색할 수 있는 VM 수가 표시됩니다.
    - 호스트에 대한 유효성 검사가 실패하면 마우스로 **상태** 열의 아이콘 위를 가리켜서 오류를 검토합니다. 문제를 해결하고, 유효성을 다시 검사합니다.
    - 호스트 또는 클러스터를 제거하려면 **삭제**를 선택합니다.
    - 클러스터에서 특정 호스트를 제거할 수 없습니다. 전체 클러스터만 제거할 수 있습니다.
    - 클러스터의 특정 호스트에 문제가 있는 경우에도 클러스터를 추가할 수 있습니다.
4. 유효성 검사가 완료되면 **저장 및 검색 시작**을 클릭하여 검색 프로세스를 시작합니다.

그러면 검색을 시작합니다. 검색된 VM의 메타데이터가 Azure Portal에 표시되는 데 약 15분이 걸립니다.

## <a name="verify-vms-in-the-portal"></a>포털에서 VM을 확인합니다.

검색이 완료되면 Azure Portal에서 해당 VM이 표시되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색 된 서버의**수를 표시 하는 아이콘을 클릭 합니다.


## <a name="next-steps"></a>다음 단계

Azure Migrate Server 평가를 사용 하 여 [hyper-v 평가](tutorial-assess-hyper-v.md) 를 사용해 보세요.
