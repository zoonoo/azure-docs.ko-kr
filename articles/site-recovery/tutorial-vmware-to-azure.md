---
title: "Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware VM 재해 복구 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Azure에 온-프레미스 VMware VM에 대한 재해 복구를 설정하는 방법을 알아봅니다."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1c9bfe567b1e0872abc7aba054127735d5f61754
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Azure에 온-프레미스 VMware VM 재해 복구 설정

이 자습서에서는 Windows를 실행하는 온-프레미스 VMware VM에 대한 재해 복구를 Azure에 설정하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Site Recovery에 대한 Recovery Services 자격 증명 모음 만들기
> * 원본 및 대상 복제 환경 설정
> * 복제 정책 만들기
> * VM에 대한 복제 사용

이 문서는 시리즈의 세 번째 자습서입니다. 이 자습서에서는 이전 자습서의 작업을 이미 완료했다고 가정합니다.

1. [Azure 준비](tutorial-prepare-azure.md)
2. [온-프레미스 VMware 준비](tutorial-prepare-on-premises-vmware.md)

시작하기 전에 재해 복구 시나리오에 대한 [아키텍처를 검토](concepts-vmware-to-azure-architecture.md)하는 것이 좋습니다.

## <a name="configure-vmware-account-permissions"></a>VMware 계정 권한 구성

1. vCenter 수준에서 역할을 만듭니다. 역할에 **Azure_Site_Recovery**라는 이름을 지정합니다.
2. **Azure_Site_Recovery** 역할에 다음 권한을 할당합니다.

   **Task** | **역할/권한** | **세부 정보**
   --- | --- | ---
   **VM 검색** | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 읽기 전용 사용자(최소)<br/><br/> 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
   **전체 복제, 장애 조치, 장애 복구** |  데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -> 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 컴퓨터 -> 구성<br/><br/> 가상 컴퓨터 -> 상호 작용 -> 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 컴퓨터 -> 인벤토리 -> 만들기, 등록, 등록 취소<br/><br/> 가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/> 가상 컴퓨터 -> 스냅숏 -> 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.

3. vCenter 서버 또는 vSphere 호스트에서 사용자를 만듭니다. 사용자에게 역할을 할당합니다.

## <a name="specify-what-you-want-to-replicate"></a>복제하려는 항목 지정

복제하려는 각 VM에 모바일 서비스가 설치되어야 합니다. Site Recovery에서 VM에 대한 복제를 사용하도록 설정하면 이 서비스를 자동으로 설치합니다. 자동 설치하려면 Site Recovery에서 VM에 액세스하는 데 사용할 계정을 준비해야 합니다.

도메인 또는 로컬 계정을 사용할 수 있습니다. Linux VM의 경우 계정은 원본 Linux 서버의 루트여야 합니다. Windows VM의 경우 도메인 계정을 사용하지 않으면 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다.

  - 레지스트리의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가하고 값을 1로 설정합니다.

## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경 설정은 Site Recovery 통합 설치 프로그램 다운로드, 구성 서버 설정/자격 증명 모음에 등록 및 VM 검색으로 구성됩니다.

구성 서버는 모든 Site Recovery 구성 요소를 호스팅하는 단일 온-프레미스 VMware VM입니다. 이 VM은 구성 서버, 프로세스 서버 및 마스터 대상 서버를 실행합니다.

- 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
- 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하고 캐싱, 압축 및 암호화를 사용하여 최적화하며 복제 데이터를 Azure Storage로 전송합니다. 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스를 설치하고 온-프레미스 VMware 서버에서 VM의 자동 검색을 수행합니다.
- 마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.

구성 서버 VM은 다음 요구 사항을 충족하는 고가용성 VMware VM이어야 합니다.

| **요구 사항** | **세부 정보** |
|-----------------|-------------|
| CPU 코어 수| 8 |
| RAM | 12GB |
| 디스크 수 | 3 - OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용) |
| 사용 가능한 디스크 공간(프로세스 서버 캐시) | 600GB |
| 사용 가능한 디스크 공간(보존 디스크) | 600GB |
| 운영 체제 버전 | Windows Server 2012 R2 |
| 운영 체제 로케일 | 미국 영어(en-us) |
| VMware vSphere PowerCLI 버전 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Windows Server 역할 | Active Directory Domain Services, 인터넷 정보 서비스 및 Hyper-V 역할은 사용하지 않습니다. |
| NIC 유형 | VMXNET3 |
| IP 주소 유형 | 정적 |
| 포트 | 443(컨트롤 채널 오케스트레이션)<br/>9443(데이터 전송)|

구성 서버 VM에서 시스템 시계가 시간 서버와 동기화되었는지 확인합니다.
시간은 15분 이내에 동기화되어야 합니다. 시간 차이가 15분보다 크면 설치가 실패합니다.

구성 서버 VM에서 다음 URL에 액세스할 수 있는지 확인합니다.

- *.accesscontrol.windows.net. 액세스 제어 및 ID 관리에 사용됩니다.
- *.backup.windowsazure.com. 복제 데이터 전송 및 조정에 사용됩니다.
- *.blob.core.windows.net. 복제된 데이터를 저장하는 저장소 계정에 액세스하는 데 사용됩니다.
- *.hypervrecoverymanager.windowsazure.com. 복제 관리 작업 및 조정에 사용됩니다.
- time.nist.gov and time.windows.com. 시스템 시간과 글로벌 시간 사이의 시간 동기화를 확인하는 데 사용됩니다.

Azure Government 클라우드에 대한 URL:

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

모든 IP 주소 기반 방화벽 규칙에서 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653) 및 443(HTTPS)/9443(데이터 복제) 포트에 대한 통신을 허용해야 합니다. 구독의 Azure 지역 및 미국 서부에 대한 IP 주소 범위를 허용해야 합니다(Access Control 및 Identity Management에 사용됨).

### <a name="download-the-site-recovery-unified-setup"></a>Site Recovery 통합 설치 프로그램 다운로드

1. [Azure Portal](https://portal.azure.com)을 열고 **모든 리소스**를 클릭합니다.
2. **ContosoVMVault**라는 Recovery Service 자격 증명 모음을 클릭합니다.
3. **Site Recovery** > **인프라 준비** > **보호 목표**를 차례로 클릭합니다.
4. 컴퓨터가 있는 위치에 대해 **온-프레미스**를 선택하고, 컴퓨터를 복제할 위치를 **To Azure(대상 Azure)**로 설정하고, **Yes, with VMware vSphere Hypervisor(예, VMware vSphere 하이퍼바이저 포함)**를 선택합니다. 그런 다음 **확인**을 클릭합니다.
5. [원본 준비] 창에서 **+ 구성 서버**를 클릭합니다.
6. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
7. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
8. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![원본 설정](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>구성 서버 설정

1. 통합 설치 프로그램 설치 파일을 실행합니다.
2. **시작하기 전에**에서 **구성 서버 및 프로세스 서버 설치**를 선택하고 **다음**을 클릭합니다.

3. **타사 소프트웨어 라이선스**에서 **동의함**을 클릭하여 MySQL을 다운로드하고 설치한 후 **다음**을 클릭합니다.

4. **등록**에서 자격 증명 모음에서 다운로드한 등록 키를 선택합니다.

5. **인터넷 설정**에서 구성 서버에서 실행 중인 공급자가 인터넷을 통해 Azure Site Recovery에 연결하는 방법을 지정합니다.

   - 현재 컴퓨터에 설정된 프록시를 사용하여 연결하려면 **프록시 서버를 사용하여 Azure Site Recovery에 연결**을 선택합니다.
   - 공급자를 직접 연결하려면 **프록시 서버 없이 Azure Site Recovery에 직접 연결**을 선택합니다.
   - 기존 프록시에 인증이 필요하거나 공급자 연결에 사용자 지정 프록시를 사용하려면 **사용자 지정 프록시 설정으로 연결**을 선택하고 주소, 포트 및 자격 증명을 지정합니다.

   ![방화벽](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. **필수 조건 확인**에서 설치 프로그램은 설치가 실행될 수 있는지 확인합니다. **글로벌 시간 동기화 확인**에 대한 경고가 표시되면 시스템 시계의 시간(**날짜 및 시간** 설정)이 표준 시간대와 같은지 확인합니다.

   ![필수 조건](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. **MySQL 구성**에서 설치된 MySQL 서버 인스턴스에 로그온하기 위한 자격 증명을 만듭니다.

8. **환경 세부 정보**에서 VMware VM을 보호하려면 **예**를 선택합니다. 설치 프로그램에서 PowerCLI 6.0이 설치되어 있는지 확인합니다.

9. **설치 위치**에서 이진 파일을 설치하고 캐시를 저장할 위치를 선택합니다. 최소 5GB의 디스크 공간이 있는 드라이브를 선택해야 하지만 600GB 이상의 사용 가능한 공간이 있는 캐시 드라이브를 선택하는 것이 좋습니다.

10. **네트워크 선택**에서 구성 서버가 복제 데이터를 전송 및 수신할 수신기(네트워크 어댑터 및 SSL 포트)를 지정합니다. 9443 포트는 복제 트래픽을 보내고 받는 데 사용되는 기본 포트이지만, 환경의 요구 사항에 맞게 이 포트 번호를 수정할 수 있습니다. 또한 복제 작업을 오케스트레이션하는 데 사용되는 443 포트를 엽니다. 복제 트래픽을 보내거나 받는 데 443 포트를 사용하면 안 됩니다.

11. **요약**에서 정보를 검토하고 **설치**를 클릭합니다. 설치 프로그램에서 구성 서버를 설치하고 Azure Site Recovery 서비스에 등록합니다.

    ![요약](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    설치가 완료되면 암호가 생성됩니다. 복제를 사용하도록 설정할 때 필요하므로 암호를 복사하고 안전한 위치에 보관합니다. 서버가 자격 증명 모음의 **설정** > **서버** 창에 표시됩니다.

### <a name="configure-automatic-discovery"></a>자동 검색 구성

VM을 검색하려면 구성 서버에서 온-프레미스 VMware 서버에 연결해야 합니다. 이 자습서에서는 서버에 대한 관리자 권한이 있는 계정을 사용하여 vCenter 서버 또는 vSphere 호스트를 추가합니다.

1. 구성 서버에서 **CSPSConfigtool.exe**를 시작합니다. 바탕 화면에서 바로 가기로 사용할 수 있으며, *설치 위치*\home\svsystems\bin 폴더에 있습니다.

2. **계정 관리** > **계정 추가**를 클릭합니다.

   ![계정 추가](./media/tutorial-vmware-to-azure/credentials1.png)

3. **계정 세부 정보**에서 자동 검색에 사용할 계정을 추가합니다.

   ![세부 정보](./media/tutorial-vmware-to-azure/credentials2.png)

서버를 추가하려면

1. [Azure Portal](https://portal.azure.com)을 열고 **모든 리소스**를 클릭합니다.
2. **ContosoVMVault**라는 Recovery Service 자격 증명 모음을 클릭합니다.
3. **Site Recovery** > **인프라 준비** > **원본**을 차례로 클릭합니다.
4. vCenter 서버 또는 vSphere ESXi 호스트에 연결하려면 **+ vCenter**를 선택합니다.
5. **vCenter 추가**에서 서버에 대한 표시 이름을 지정합니다. 그런 다음 IP 주소 또는 FQDN을 지정합니다.
6. VMware 서버가 다른 포트에서 요청을 수신 대기하도록 구성되지 않은 경우 443으로 설정된 포트를 그대로 둡니다.
7. 서버 연결에 사용할 계정을 선택합니다. **확인**을 클릭합니다.

Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

> [!NOTE]
> 포털에 계정 이름이 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > ***서버 이름*** > **서버 새로 고침**을 클릭합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 리소스 관리자 기반인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. [Azure Portal](https://portal.azure.com)을 열고 **모든 리소스**를 클릭합니다.
2. **ContosoVMVault**라는 Recovery Service 자격 증명 모음을 클릭합니다.
3. 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** > **+복제 정책**을 차례로 클릭합니다.
4. **복제 정책 만들기**에서 **VMwareRepPolicy**라는 정책 이름을 지정합니다.
5. **RPO 임계값**에서 기본값인 60분을 사용합니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
6. **복구 지점 보존**에서 각 복구 지점에 대한 보존 기간으로 기본값인 24시간을 사용합니다. 이 자습서에서는 72시간을 선택합니다. 복제된 VM은 하나의 시간대에서 임의의 시점으로 복구할 수 있습니다.
7. **앱 일치 스냅숏 빈도**에서 앱 일치 스냅숏을 만드는 빈도로 기본값인 60분을 사용합니다. **확인** 을 클릭하여 정책을 만듭니다.

   ![정책](./media/tutorial-vmware-to-azure/replication-policy.png)

정책은 구성 서버와 자동으로 연결됩니다. 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy**인 경우 장애 복구(failback) 정책은 **rep-policy-failback**이 됩니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.

## <a name="enable-replication"></a>복제 활성화

VM에 대한 복제를 사용하도록 설정되면 Site Recovery에서 모바일 서비스를 설치합니다. 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.

다음과 같이 복제를 사용합니다.

1. **응용 프로그램 복제** > **원본**을 클릭합니다.
2. **원본**에서 구성 서버를 선택합니다.
3. **컴퓨터 형식**에서 **Virtual Machines**를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저**에서 vSphere 호스트를 관리하는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. 프로세스 서버(구성 서버)를 선택합니다. 그런 다음 **확인**을 클릭합니다.
6. **대상**에서 장애 조치 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. 장애 조치 VM에 대해 Azure(클래식 또는 리소스 관리)에서 사용할 배포 모델을 선택합니다.
7. 데이터 복제에 사용할 Azure Storage 계정을 선택합니다.
8. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
9. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다.
10. **Virtual Machines** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.
11. **속성** > **속성 구성**에서 컴퓨터에 모바일 서비스를 자동으로 설치하기 위해 프로세스 서버에서 사용할 계정을 선택합니다.
12. **복제 설정** > **복제 설정 구성**에서 올바른 복제 정책이 선택되어 있는지 확인합니다.
13. **복제 사용**을 클릭합니다.

**설정** > **작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.

추가하는 VM을 모니터링하려면 **구성 서버**
> **마지막 연결**에서 VM을 마지막으로 검색한 시간을 확인할 수 있습니다. 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [재해 복구 훈련 실행](site-recovery-test-failover-to-azure.md)
