---
title: Hyper-v Vm에 대 한 마이그레이션에 대 한 Azure 마이그레이션 Server 평가/을 어플라이언스를 설정 | Microsoft Docs
description: 검색, 평가 및 Azure 마이그레이션 Server 평가/마이그레이션을 사용 하 여 Hyper-v Vm의 에이전트 없는 마이그레이션에 대 한 어플라이언스를 설정 하는 방법을 설명 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: c531fe49ebff6c021547c2d1c2f382bcd6c9caef
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811766"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Hyper-v Vm에 대 한 어플라이언스 설정

이 문서에서는 Azure Migrate Server 평가 도구를 사용 하 여 Hyper-v Vm을 평가 하거나 Azure 마이그레이션 Server 마이그레이션 도구를 사용 하 여 Azure로 VMware Vm을 마이그레이션하는 경우 Azure Migrate 어플라이언스를 설정 하는 방법을 설명 합니다.

Hyper-v VM 어플라이언스는 다음을 수행 하 여 Azure 마이그레이션 Server 평가/마이그레이션 사용 경량 어플라이언스:

- 온-프레미스 Hyper-v Vm을 검색 합니다.
- 검색 된 Vm에 Azure 마이그레이션 Server 평가/마이그레이션 메타 데이터 및 성능 데이터를 보냅니다.

[자세한](migrate-appliance.md) Azure Migrate 어플라이언스에 대 한 합니다.


## <a name="appliance-deployment-steps"></a>어플라이언스 배포 단계

설정 하려면 어플라이언스에 있습니다.
- Azure portal에서 압축 된 Hyper-v VHD를 다운로드 합니다.
- 어플라이언스를 만들고 Azure Migrate Server 평가에 연결할 수 있는지 확인 합니다. 
- 처음에 대 한 어플라이언스를 구성 하 고 Azure Migrate 프로젝트를 사용 하 여 등록 키를 누릅니다.

## <a name="download-the-vhd"></a>VHD 다운로드

어플라이언스에 대 한 압축 된 VHD 템플릿을 다운로드 합니다.

1. **마이그레이션 목표** > **서버** > **Azure Migrate: Server 평가**, 클릭 **Discover**합니다.
2. **컴퓨터 검색** > **컴퓨터가 가상화 되어 있습니까?** , 클릭 **예, Hyper-v를 사용 하 여**.
3. 클릭 **다운로드** VHD 파일을 다운로드 합니다.

    ![VM를 다운로드 합니다.](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>보안 확인

배포 하기 전에 압축된 된 파일, 안전한 지 확인 합니다.

1. 파일을 다운로드한 컴퓨터에서 관리자 명령 창을 엽니다.
2. VHD에 대 한 해시를 생성 하려면 다음 명령을 실행 합니다.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 사용 예: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  어플라이언스 버전 1.19.05.10, 생성된 된 해시는 이러한 설정을 일치 해야 합니다.

  **알고리즘** | **해시 값**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="create-the-appliance-vm"></a>어플라이언스 VM 만들기

다운로드 한 파일을 가져오고 VM을 만듭니다.

1. 어플라이언스 VM을 호스트 하는 Hyper-v 호스트의 폴더에 압축 된 VHD 파일을 추출 합니다. 세 개의 폴더가 추출 됩니다.
2. Hyper-V 관리자를 엽니다. **동작**, 클릭 **가상 머신 가져오기**합니다.

    ![VHD를 배포 합니다.](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 가상 머신 마법사에서 > **시작 하기 전에**, 클릭 **다음**합니다.
3. **폴더 찾기**, 추출 된 VHD를 포함 하는 폴더를 지정 합니다. 그리고 **다음**을 클릭합니다.
1. **가상 머신 선택**, 클릭 **다음**합니다.
2. **가져오기 유형 선택**, 클릭 **가상 머신 복사 (새로운 고유 ID 만들기)** 합니다. 그리고 **다음**을 클릭합니다.
3. **대상 선택**, 기본 설정을 그대로 적용 합니다. **다음**을 클릭합니다.
4. **저장소 폴더**, 기본 설정을 그대로 적용 합니다. **다음**을 클릭합니다.
5. **네트워크 선택**, VM에서 사용할 가상 스위치를 지정 합니다. 스위치에는 Azure로 데이터 전송에 인터넷 연결이 필요 합니다.
6. **요약**에서 설정을 검토 합니다. **마침**을 클릭합니다.
7. Hyper-v 관리자에서 > **가상 머신**, VM을 시작 합니다.


### <a name="verify-appliance-access-to-azure"></a>Azure 어플라이언스에 액세스할 수 있는지 확인

어플라이언스 VM에 연결할 수 있는지 확인 [Azure Url](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)합니다.

## <a name="configure-the-appliance"></a>어플라이언스 구성

처음에 대 한 어플라이언스를 설정 합니다.

1. Hyper-v 관리자에서 > **Virtual Machines**, VM을 마우스 오른쪽 단추로 클릭 > **Connect**합니다.
2. 어플라이언스에 대 한 언어, 표준 시간대 및 암호를 제공 합니다.
3. VM에 연결 하 고 어플라이언스 웹 앱의 URL을 열 수 있는 모든 컴퓨터에서 브라우저를 열고: **https://*어플라이언스 이름 또는 IP 주소*: 44368**.

   또는 앱 바로 가기를 클릭 하 여 어플라이언스 바탕 화면에서 앱을 열 수 있습니다.
1. 웹 앱에서 > **필수 조건 설정**, 다음을 수행 합니다.
    - **라이선스**: 사용 조건에 동의하고 타사 정보를 읽습니다.
    - **연결**: 앱의 VM이 인터넷에 액세스할 수 있는지 확인 합니다. 프록시를 사용 하는 VM:
        - 클릭 **프록시 설정**, 형식 프록시 주소 및 수신 포트를 지정 하 고 http://ProxyIPAddress 또는 http://ProxyFQDN 합니다.
        - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
        - HTTP 프록시만 지원됩니다.
    - **동기화 시간**: 시간을 확인 합니다. 어플라이언스의 시간이 제대로 작동 하려면 VM 검색에 대 한 인터넷 시간과 동기화 되어야 합니다.
    - **업데이트 설치**: Azure Migrate Server 평가 기기에 최신 업데이트가 설치 되어 있는지 확인 합니다.

### <a name="register-the-appliance-with-azure-migrate"></a>어플라이언스를 Azure Migrate로 등록

1. 클릭 **로그인**합니다. 표시 되지 않으면 브라우저에서 팝업 차단을 사용 하지 않도록 설정 했으므로 있는지 확인 합니다.
2. 새 탭에서 Azure 자격 증명을 사용 하 여 로그인 합니다. 
    - 사용자 이름 및 암호를 로그인 합니다.
    - PIN으로 로그인 지원 되지 않습니다.
3. 성공적으로 로그인 한 후 웹 앱으로 다시 이동 합니다.
4. Azure Migrate 프로젝트가 만들어진 구독을 선택 합니다. 다음 프로젝트를 선택 합니다.
5. 어플라이언스에 대 한 이름을 지정 합니다. 이름은를 14 자까지 영숫자 또는 이어야 합니다.
6. **등록**을 클릭합니다.


### <a name="delegate-credentials-for-smb-vhds"></a>SMB Vhd에 대 한 자격 증명을 위임 합니다.

Smb에 Vhd를 실행 하는 경우 Hyper-v 호스트에 기기에서 자격 증명을 위임할을 수 있어야 합니다. 기기에서이 수행 합니다.

1. 어플라이언스 VM에서이 명령을 실행 합니다. HyperVHost1/HyperVHost2은 예제 호스트 이름입니다.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. 또는 어플라이언스의 로컬 그룹 정책 편집기에서 수행 합니다.
    - **로컬 컴퓨터 정책** > **컴퓨터 구성**, 클릭 **관리 템플릿** > **시스템**  >  **자격 증명을 위임**합니다.
    - 두 번 클릭 **새 자격 증명 위임 허용**, 선택한 **Enabled**합니다.
    - **옵션**, 클릭 **표시**를 사용 하 여 목록에 검색 하려는 각 Hyper-v 호스트를 추가 하 고 **wsman /** 접두사로 합니다.
    - **자격 증명 위임을**를 두 번 클릭 **NTLM 전용일 서버 인증을 사용 하 여 새 자격 증명 위임 허용**합니다. 다시 사용 하 여 목록에 검색 하려는 각 Hyper-v 호스트를 추가 **wsman /** 접두사로 합니다.

## <a name="start-continuous-discovery"></a>연속 검색 시작

Hyper-v 호스트 또는 클러스터 기기에서 연결 하 고 VM 검색을 시작 합니다.

1. **사용자 이름** 하 고 **암호**, 어플라이언스가 Vm을 검색 하는 데 사용할 계정 자격 증명을 지정 합니다. 자격 증명의 이름을 지정 하 고 클릭 **세부 정보를 저장할**합니다.
2. 클릭 **호스트 추가**, Hyper-v 호스트/클러스터 세부 정보를 지정 합니다.
3. **유효성 검사**를 클릭합니다. 유효성 검사 후 각 호스트/클러스터에서 검색할 수 있는 Vm 수가 표시 됩니다.
    - 호스트에 대 한 유효성 검사에 실패 하는 경우에서 아이콘을 마우스로 오류를 검토 합니다 **상태** 열입니다. 문제를 해결 하 고 유효성을 다시 검사 합니다.
    - 호스트 또는 클러스터를 제거 하려면 선택 > **삭제**합니다.
    - 클러스터에서 특정 호스트를 제거할 수 없습니다. 전체 클러스터만 제거할 수 있습니다.
    - 클러스터의 특정 호스트를 사용 하 여 문제가 있는 경우에 클러스터를 추가할 수 있습니다.
4. 유효성이 검사 되 면 클릭 **저장 및 검색 시작** 검색 프로세스를 시작 합니다.

이 검색을 시작합니다. Azure portal에 표시할 검색 된 Vm의 메타 데이터에 대 한 약 15 분이 걸립니다. 

## <a name="verify-vms-in-the-portal"></a>포털에서 VM 확인

검색에는 다음이 완료 되 면 포털에서 Vm이 표시 되는지 확인할 수 있습니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate-서버** > **Azure Migrate: Server 평가** 페이지의 수를 표시 하는 아이콘을 클릭 합니다 **서버 검색**합니다. 


## <a name="next-steps"></a>다음 단계

사용해 보세요 [Hyper-v 평가](tutorial-assess-hyper-v.md) Azure Migrate Server 평가 사용 하 여 합니다.