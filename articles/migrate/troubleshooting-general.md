---
title: Azure Migrate 문제 해결 | Microsoft Docs
description: Azure Migrate 서비스의 알려진 문제에 대한 개요와 일반적인 오류 해결 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: raynew
ms.openlocfilehash: 0e2a8f269a98babc17f36ceff209ee2f057e6911
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302321"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate 문제 해결

[Azure Migrate](migrate-services-overview.md) 는 Microsoft의 평가 및 마이그레이션과 ISV (독립 소프트웨어 공급 업체) 제품에 대 한 Microsoft 도구의 허브를 제공 합니다. 이 문서에서는 Azure Migrate Azure Migrate 관련 된 오류 해결에 대 한 도움말을 제공 합니다. 서버 평가 및 Azure Migrate: 서버 마이그레이션.

## <a name="azure-migrate-project-issues"></a>Azure Migrate 프로젝트 문제

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>기존 Azure Migrate 프로젝트를 찾을 수 없습니다.

Azure Migrate의 [두 가지 버전이](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 있습니다. 프로젝트를 만든 버전에 따라 다음 단계를 수행 하 여 프로젝트를 찾습니다.

1. 이전 버전 (이전 환경)의 Azure Migrate를 사용 하 여 만든 프로젝트를 찾고 있는 경우 아래 단계에 따라 프로젝트를 찾습니다.

    1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
    2. Azure Migrate 대시보드에 이전 프로젝트에 액세스 하는 방법을 설명 하는 배너가 표시 됩니다. 이전 환경에서 프로젝트를 만든 경우에만이 배너가 표시 됩니다. 배너를 클릭 합니다.

    ![기존 프로젝트 액세스](./media/troubleshooting-general/access-existing-projects.png)

    3. 이제 이전 버전의 Azure Migrate을 사용 하 여 만든 기존 프로젝트의 목록이 표시 됩니다.

2. 현재 버전 (새 환경)으로 만든 프로젝트를 찾고 있는 경우 아래 단계에 따라 프로젝트를 찾습니다.

    1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
    2. Azure Migrate 대시보드에서 왼쪽 창의 **서버** 페이지로 이동 하 고 오른쪽 위 모서리에서 **변경** 을 선택 합니다.

    ![기존 Azure Migrate 프로젝트로 전환](./media/troubleshooting-general/switch-project.png)

    3. 적절 한 **구독** 을 선택 하 고 **프로젝트를 마이그레이션합니다**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>두 번째 Azure Migrate 프로젝트를 만들 수 없습니다.

아래 단계에 따라 새 Azure Migrate 프로젝트를 만듭니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드에서 왼쪽 창의 **서버** 페이지로 이동 하 고 오른쪽 위 모서리에서 **변경** 을 선택 합니다.

   ![Azure Migrate 프로젝트 변경](./media/troubleshooting-general/switch-project.png)

3. 새 프로젝트를 만들려면 아래 스크린샷에 표시 된 대로 **여기를 클릭** 하십시오 .를 선택 합니다.

   ![두 번째 Azure Migrate 프로젝트 만들기](./media/troubleshooting-general/create-new-project.png)

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Azure Migrate 프로젝트 및 관련 Log Analytics 작업 영역 삭제

Azure Migrate 프로젝트를 삭제 하면 검색 된 컴퓨터에 대 한 메타 데이터와 함께 마이그레이션 프로젝트가 삭제 됩니다. 그러나 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우에는 Log Analytics 작업 영역을 자동으로 삭제 하지 않습니다. 이는 같은 Log Analytics 작업 영역이 여러 사용 사례에 사용되었을 수 있기 때문입니다. Log Analytics 작업 영역도 삭제하려면 수동으로 작업 영역을 삭제해야 합니다.

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.
     1. 마이그레이션 프로젝트를 아직 삭제 하지 않은 경우 Essentials 섹션의 서버 평가 개요 페이지에서 작업 영역에 대 한 링크를 찾을 수 있습니다.

     ![LA 작업 영역](./media/troubleshooting-general/loganalytics-workspace.png)

     2. 마이그레이션 프로젝트를 이미 삭제 한 경우 Azure Portal 왼쪽 창에서 **리소스 그룹** 을 선택 합니다. 작업 영역을 만든 리소스 그룹으로 이동 하 여 검색 합니다.
2. [이 문서](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)의 지침에 따라 작업 영역을 삭제합니다.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>*요청에 사용자 ID 헤더가 있어야 합니다* 오류 메시지와 함께 마이그레이션 프로젝트 만들기 실패

조직의 Azure AD(Azure Active Directory) 테넌트에 대한 액세스 권한이 없는 사용자에게 이 문제가 발생할 수 있습니다. 사용자가 처음으로 Azure AD 테넌트에 추가되면 해당 사용자는 테넌트에 조인하라는 이메일 초대장을 받게 됩니다. 사용자는 이메일로 이동하여 초대를 수락해야만 테넌트에 성공적으로 추가됩니다. 이메일을 볼 수 없는 경우 테넌트에 액세스할 수 있는 사용자에게 [여기](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)에 설명된 단계에 따라 초대장을 다시 보내 달라고 부탁하면 됩니다.

초대 이메일을 받은 후에는 이메일을 열고 이메일의 링크를 클릭하여 초대를 수락해야 합니다. 이 작업이 완료 되 면 Azure Portal 로그 아웃 한 후 다시 로그인 해야 브라우저가 새로 고쳐지지 않습니다. 그 후 마이그레이션 프로젝트 만들기를 시도할 수 있습니다.

## <a name="appliance-issues"></a>어플라이언스 문제

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>오류로 인해 VMware에 대 한 Azure Migrate 어플라이언스를 배포 하지 못했습니다. 제공된 매니페스트 파일이 잘못되었습니다. 잘못된 OVF 매니페스트 항목입니다.

1. 해당 해시 값을 확인 하 여 Azure Migrate 어플라이언스 OVA 파일이 올바르게 다운로드 되는지 확인 합니다. 해시 값을 확인하려면 [문서](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance)를 참조하세요. 해시 값이 일치하지 않는 경우 OVA 파일을 다시 다운로드하고 배포를 다시 시도합니다.
2. 여전히 실패하고 VMware vSphere 클라이언트를 OVF 배포에 사용하는 경우 vSphere Web Client를 통해 배포를 시도합니다. 그래도 실패하는 경우 다른 웹 브라우저를 사용해 보세요.
3. vSphere 웹 클라이언트를 사용 중이며 vCenter Server 6.5 또는 6.7에서 해당 클라이언트를 배포하려는 경우 다음 단계에 따라 ESXi 호스트에서 직접 OVA 배포를 시도합니다.
   - 웹 클라이언트 (https://<*호스트 IP 주소*>/du)를 사용 하 여 ESXi 호스트에 직접 연결 (vCenter Server 대신) 합니다.
   - **홈** > **인벤토리**로 이동 합니다.
   - **파일 배포 파일** > **배포 템플릿**을 클릭 합니다. OVA로 이동 하 여 배포를 완료 합니다.
4. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>어플라이언스에서 인터넷에 연결할 수 없습니다.

이 문제는 사용 중인 컴퓨터가 프록시 뒤에 있는 경우 발생할 수 있습니다. 프록시에 필요한 경우 권한 부여 자격 증명을 제공해야 합니다.
URL 기반 방화벽 프록시를 사용 하 여 아웃 바운드 연결을 제어 하는 경우 다음 필수 Url을 허용 목록에 추가 해야 합니다.

시나리오 | URL 목록
--- | ---
VMware에 대 한 서버 평가 | [여기](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Hyper-v에 대 한 서버 평가 | [여기](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
VMware에 대 한 서버 마이그레이션 (에이전트 없음) | [여기](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
VMware에 대 한 서버 마이그레이션 (에이전트 기반) | [여기](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Hyper-v에 대 한 서버 마이그레이션 | [여기](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

가로채기 프록시를 사용 하 여 인터넷에 연결 하는 경우에는 프록시 인증서를 어플라이언스 VM으로 가져와야 합니다. [여기](https://docs.microsoft.com/azure/migrate/concepts-collector) 설명된 단계에 따라 프록시 인증서를 가져올 수 있습니다.

### <a name="error-802-date-and-time-synchronization-error"></a>오류 802: 날짜 및 시간 동기화 오류

서버 시계가 현재 시간과 동기화되지 않아 5분 이상 차이가 벌어질 수 있습니다. 다음과 같이 VM 수집기에서 시계 시간을 현재 시간과 일치하도록 변경합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인하려면 w32tm /tz 명령을 실행합니다.
3. 시간을 동기화하려면 w32tm /resync 명령을 실행합니다.


### <a name="error-unabletoconnecttoserver"></a>UnableToConnectToServer 오류

다음 오류로 인해 vCenter Server "Servername.com:9443"에 연결할 수 없습니다. https://Servername.com:9443/sdk 를 수신 대기 중인 메시지를 수락할 수 있는 엔드포인트가 없습니다.

수집기 어플라이언스가 최신 버전인지 확인하고, 최신 버전이 아니면 어플라이언스를 [최신 버전](https://docs.microsoft.com/azure/migrate/concepts-collector)으로 업그레이드합니다.

최신 버전에서도 여전히 문제가 발생하면 수집기 컴퓨터가 지정된 vCenter Server 이름을 확인할 수 없거나 지정된 포트가 잘못되었을 수 있습니다. 기본적으로 포트가 지정되지 않은 경우 수집기는 포트 번호 443에 연결하려고 합니다.

1. 수집기 컴퓨터에서 Servername.com를 ping해 봅니다.
2. 1단계가 실패하는 경우 IP 주소를 통해 vCenter Server에 연결해보세요.
3. vCenter에 연결할 정확한 포트 번호를 식별합니다.
4. 마지막으로 vCenter Server가 실행 중인지 확인합니다.

## <a name="discovery-issues"></a>검색 문제

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migrate-tiles-show-a-status-of-discovery-in-progress"></a>검색을 시작 했지만 Azure Portal에서 검색 된 Vm이 표시 되지 않습니다. 서버 평가 및 서버 마이그레이션 타일이 "검색 중" 상태를 표시 합니다.
어플라이언스에서 검색을 시작한 후 검색 된 컴퓨터가 Azure Portal에 표시 될 때까지 기다립니다. VMware 검색에는 약 15 분이 걸리고 Hyper-v 검색의 경우 추가 된 호스트 당 약 2 분이 소요 됩니다. 이 시간이 지난 후에도 계속 해 서 "검색 진행 중"이 표시 되 면 **서버** 탭에서 **새로 고침** 을 클릭 합니다. 서버 평가 및 서버 마이그레이션 타일에서 검색 된 서버 수가 표시 됩니다.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>온-프레미스 환경을 지속적으로 검색 하지만 온-프레미스 환경에서 삭제 된 Vm이 포털에 계속 표시 되는 어플라이언스를 사용 하 고 있습니다.

어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 될 때까지 최대 30 분이 걸립니다. 30 분이 지난 후에도 최신 정보가 표시 되지 않으면 다음 단계를 사용 하 여 데이터 새로 고침을 실행 합니다.

1. 서버에서 > Azure Migrate: 서버 평가에서 **개요**를 클릭 합니다.
2. **관리**에서를 클릭 **에이전트 상태**
3. **에이전트를 새로 고치**는 옵션을 클릭 합니다. 에이전트 목록 아래에이 옵션이 표시 됩니다.
4. 새로 고침 작업이 완료 될 때까지 기다립니다. Vm에 대 한 최신 정보를 확인할 수 있는지 확인 합니다.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>포털의 온-프레미스 Vm에 대 한 최신 정보가 없습니다.

어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 될 때까지 최대 30 분이 걸립니다. 30 분이 지난 후에도 최신 정보가 표시 되지 않으면 다음 단계를 사용 하 여 데이터 새로 고침을 실행 합니다.

1. 서버에서 > Azure Migrate: 서버 평가에서 **개요**를 클릭 합니다.
2. **관리**에서를 클릭 **에이전트 상태**
3. **에이전트를 새로 고치**는 옵션을 클릭 합니다. 에이전트 목록 아래에이 옵션이 표시 됩니다.
4. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 Vm에 대 한 최신 정보가 표시 됩니다.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>서버 이름을 확인할 수 없기 때문에 호스트 또는 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9 (오류 ID: 50004)
어플라이언스를 처리 하는 DNS에서 사용자가 제공한 클러스터 또는 호스트 이름을 확인할 수 없는 경우이 오류가 발생 합니다. 클러스터에서이 오류가 표시 되 면 클러스터의 정규화 된 도메인 이름을 제공 하십시오. 

클러스터의 호스트에 대해서도이 오류가 표시 될 수 있습니다. 이 경우 어플라이언스는 클러스터에 연결할 수 있습니다. 그러나 클러스터에서 정규화 된 도메인 이름이 아닌 호스트 이름을 반환 했습니다. 

이 오류를 해결 하려면 IP 주소와 호스트 이름의 매핑을 추가 하 여 어플라이언스의 hosts 파일을 업데이트 합니다.
1. 관리자 권한으로 메모장을 엽니다. C:\Windows\System32\Drivers\etc\hosts. 파일을 엽니다.
2. 행에 IP 주소 및 호스트 이름을 추가 합니다. 이 오류가 표시 되는 각 호스트 또는 클러스터에 대해 반복 합니다.
3. Hosts 파일을 저장 하 고 닫습니다.
4. 어플라이언스 관리 앱을 사용 하 여 어플라이언스를 호스트에 연결할 수 있는지 확인할 수 있습니다. 30 분 후 Azure Portal에서 이러한 호스트에 대 한 최신 정보를 볼 수 있습니다. 


## <a name="assessment-issues"></a>평가 문제

### <a name="azure-readiness-issues"></a>Azure 준비 문제

문제점 | 재구성
--- | ---
지원되지 않는 부팅 유형 | Azure는 공유 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행 하기 전에 부팅 유형을 BIOS로 변환 하는 것이 좋습니다. <br/><br/>Azure Migrate 서버 마이그레이션을 사용 하 여 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환 하므로 이러한 Vm의 마이그레이션을 수행할 수 있습니다.
조건부로 지원되는 Windows OS | OS의 지원 종료 날짜가 경과되어, [Azure에서 지원되기](https://aka.ms/WSosstatement) 위해 CSA(사용자 지정 지원 계약)가 필요한 경우 Azure로 마이그레이션하기 전에 OS를 업그레이드하는 것이 좋습니다.
지원되지 않는 Windows OS | Azure는 [선택된 Windows OS 버전](https://aka.ms/WSosstatement)만 지원하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure는 [선택된 Linux OS 버전](../virtual-machines/linux/endorsed-distros.md)만 보증하므로 Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드하는 것이 좋습니다.
보증되지 않는 Linux OS | 컴퓨터를 Azure에서 부팅할 수 있지만 Azure에서 OS 지원을 제공하지 않습니다. Azure로 마이그레이션하기 전에 OS를 [보증된 Linux 버전](../virtual-machines/linux/endorsed-distros.md)으로 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | Azure Migrate가 VM의 Azure 준비 상태를 식별할 수 없으므로 VM의 운영 체제가 vCenter 서버에서 '기타'로 지정되었습니다. 컴퓨터를 마이그레이션하기 전에 컴퓨터 내에서 실행되는 OS가 Azure에서 [지원](https://aka.ms/azureoslist)되는지 확인합니다.
지원되지 않는 OS 비트 | 32비트 OS가 있는 VM은 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 VM의 OS를 32비트에서 64비트로 업그레이드하는 것이 좋습니다.
Visual Studio 구독이 필요합니다. | 컴퓨터에는 Visual Studio 구독 에서만 지원 되는 Windows 클라이언트 OS가 포함 되어 있습니다.
필요한 저장소 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 저장소 성능(IOPS/처리량)이 Azure VM 지원을 초과합니다. 마이그레이션을 시작하기 전에 컴퓨터의 저장소 요구 사항을 낮춰봅니다.
필요한 네트워크 성능을 제공하는 VM을 찾을 수 없습니다. | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정된 위치에서 VM을 찾을 수 없습니다. | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
부적합한 디스크가 하나 이상 있습니다. | VM에 연결된 하나 이상의 디스크가 Azure 요구 사항을 충족하지 않습니다. VM에 연결된 각 디스크에 대해 디스크 크기가 4TB 미만인지 확인합니다. 4TB 미만이 아니면 Azure로 마이그레이션하기 전에 디스크 크기를 축소합니다. 각 디스크에 필요한 성능(IOPS/처리량)이 Azure [관리 가상 머신 디스크](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)에서 지원되는지 확인합니다.   
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure는 4TB 이하의 디스크를 지원합니다. 마이그레이션을 시작하기 전에 디스크를 4TB 미만으로 축소합니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 저장소 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure가 적합한 종류의 VM을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>평가 속성에서 Azure 제품으로 EA (기업계약)를 지정할 수 없나요?
Azure Migrate: 서버 평가는 현재 EA (기업계약) 기반 가격 책정을 지원 하지 않습니다. 해결 방법은 Azure 제품으로 ' 종 량 제 '을 사용 하 고 ' 할인율 ' 속성을 사용 하 여 사용자 지정 할인을 지정 하는 것입니다. [평가를 사용자 지정할 수 있는 방법에 대해 자세히 알아보세요](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>서버 평가에서 내 Linux Vm을 조건부로 준비 된 것으로 표시 하는 이유는 무엇 인가요? 이 문제를 해결 하기 위해 필요한 항목이 있나요?
서버 평가에는 온-프레미스 Vm에 설치 된 Linux 운영 체제의 부 버전을 검색할 수 없는 알려진 간격이 있습니다. 예를 들어 RHEL 6.10의 경우 현재 서버 평가는 OS 버전으로 RHEL 6만 검색 합니다. Azure는 특정 버전의 Linux만 보증 때문에 Linux Vm은 현재 서버 평가에서 조건부로 준비 된 것으로 표시 되어 있습니다. [Azure linux 지원 설명서](https://aka.ms/migrate/selfhost/azureendorseddistros)를 검토 하 여 온-프레미스 VM에서 실행 되는 Linux OS가 azure에서 보증 여부를 수동으로 확인할 수 있습니다. 보증 배포판를 확인 한 후에는이 경고를 무시 해도 됩니다.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>서버 평가에서 권장 하는 VM SKU에는 온-프레미스에 할당 된 것 보다 많은 수의 코어와 더 큰 메모리 크기가 있습니다. 그 이유는 무엇입니까?
서버 평가에서 VM SKU 권장 사항은 평가 속성에 따라 달라 집니다. 서버 평가, ' 성능 기반 ' 및 ' 온-프레미스 ' 평가에서 두 종류의 평가를 만들 수 있습니다. 성능 기반 평가의 경우 서버 평가는 온-프레미스 vm (CPU, 메모리, 디스크 및 네트워크 사용률)의 사용률 데이터를 고려 하 여 온-프레미스 Vm의 올바른 대상 VM SKU를 확인 합니다. 또한 성능 기반 크기 조정의 경우 적합 한 사용률을 파악 하기 위해 편안 하 게 고려 됩니다. 온-프레미스 크기 조정의 경우 성능 데이터를 고려 하지 않으며 온-프레미스에 할당 된 항목에 따라 대상 SKU를 사용 하는 것이 좋습니다.

예를 들어 50% CPU 50 사용률과%의 메모리 사용률을 포함 하는 온-프레미스 VM과 8gb 1.3의 메모리를 사용 하 고 평가에 편안한 요소가 지정 되어 있다고 가정해 보겠습니다. 평가의 크기 조정 기준이 ' 온-프레미스 ' 인 경우, 4 개의 코어와 8 GB 메모리를 사용 하는 Azure VM SKU를 사용 하는 것이 좋습니다. 그러나 크기 조정 기준은 실제 CPU 및 메모리 사용률 (50% 4 개 코어 * 1.3 = 2.6 코어 및 50% 8 GB)을 기반으로 합니다. memory * 1.3 = 5.3-GB 메모리), 4 개 코어 (지원 되는 가장 가까운 코어 수) 및 8gb 메모리 크기 (지원 되는 가장 가까운 메모리 크기)의 가장 저렴 한 VM SKU를 권장 합니다. [서버 평가에서 크기 조정을 수행 하는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>서버 평가에서 권장 하는 디스크 SKU에는 온-프레미스에 할당 된 것 보다 큰 크기가 있습니다. 그 이유는 무엇입니까?
서버 평가의 디스크 크기 조정은 두 가지 평가 속성 (크기 조정 기준 및 저장소 유형)에 따라 달라 집니다. 크기 조정 기준이 ' 성능 기반 '이 고 저장소 유형이 ' 자동 '으로 설정 된 경우 디스크의 IOPS 및 처리량 값은 대상 디스크 유형 (표준 HDD, 표준 SSD 또는 프리미엄 디스크)을 식별 하는 것으로 간주 됩니다. 디스크 유형 내의 디스크 SKU는 온-프레미스 디스크의 크기 요구 사항을 고려 하 여 권장 됩니다. 크기 조정 기준이 ' 성능 기반 '이 고 저장소 유형이 ' 프리미엄 ' 인 경우 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 Azure에서 Premium disk SKU를 사용 하는 것이 좋습니다. 크기 조정 기준이 ' 온-프레미스 ' 크기 조정 이며 저장소 유형이 ' 표준 HDD ', ' 표준 SSD ' 또는 ' Premium ' 인 경우 디스크 크기를 조정 하는 데 동일한 논리가 사용 됩니다.

예를 들어, 32 GB 메모리를 포함 하는 온-프레미스 디스크가 있지만 디스크에 대 한 집계 된 읽기/쓰기 IOPS가 800 IOPS 인 경우 서버 평가는 더 높은 IOPS 요구 사항으로 인해 프리미엄 디스크 유형을 권장 하 고 다음을 지원할 수 있는 디스크 SKU를 권장 합니다. 필요한 IOPS 및 크기입니다. 이 예제에서 가장 가까운 일치 항목은 P15 (256 GB, 1100 IOPS)입니다. 따라서 온-프레미스 디스크에 필요한 크기가 32 GB 이더라도 서버 평가에서 온-프레미스 디스크의 높은 IOPS 요구 사항으로 인해 크기가 더 큰 디스크를 권장 합니다.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>서버 평가에서 예상 하는 계산 비용에 포함 된 VM의 OS 라이선스 비용은 어떻습니까?
서버 평가는 현재 Windows 컴퓨터에 대 한 OS 라이선스 비용만 고려 하며, Linux 컴퓨터에 대 한 OS 라이선스 비용은 현재 고려 되지 않습니다. 

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>성능 기록 및 백분위수 활용률이 크기 추천에 어떤 영향을 주나요?
이러한 속성은 ' 성능 기반 ' 크기 조정에만 적용 됩니다. 서버 평가는 온-프레미스 컴퓨터의 성능 데이터를 지속적으로 수집 하 고이를 사용 하 여 Azure에서 VM SKU 및 디스크 SKU를 권장 합니다. 서버 평가에서 성능 데이터를 수집 하는 방법은 다음과 같습니다.
- Azure Migrate 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 VMware Vm에 대해 실시간 사용률 데이터를 수집 하 고 Hyper-v Vm의 경우 30 초 마다 수집 합니다.
- 어플라이언스는 20/30 초 샘플을 롤업 하 여 10 분 마다 단일 데이터 요소를 만듭니다. 단일 데이터 요소를 만들기 위해 어플라이언스는 모든 20/30 초 샘플에서 최고 값을 선택 하 여 Azure에 보냅니다.
- 성능 기간 및 성능 기록 백분위 수 값에 따라 서버 평가에서 평가를 만들 때 대표적인 사용률 값이 식별 됩니다. 예를 들어 성능 기록이 1 주이 고 백분위 수 사용률이 95 인 경우 마지막 1 주에 대 한 모든 10 분 샘플 요소를 오름차순으로 정렬 하 고 95 번째 백분위 수를 대표 값으로 선택 Azure Migrate 합니다.
95 번째 백분위 수 값을 사용 하면 99 번째 백분위 수를 선택 하는 경우 포함 될 수 있는 이상 값을 무시 합니다. 기간에 대 한 피크 사용을 선택 하 고 이상 값을 누락 하지 않으려는 경우 백분위 수를 백분위 수로 선택 해야 합니다.

## <a name="dependency-visualization-issues"></a>종속성 시각화 문제

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government 프로젝트에 대한 종속성 시각화 기능을 찾을 수 없습니다.

Azure Migrate는 종속성 시각화 기능에 대한 서비스 맵에 의존하며, 현재 Azure Government에서는 서비스 맵을 사용할 수 없으므로 Azure Government에서는 이 기능을 사용할 수 없습니다.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>온-프레미스 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 설치했는데 종속성이 Azure Migrate 포털에 표시됩니다.

일반적으로는 에이전트를 설치한 후 15~30분이 지나면 Azure Migrate 포털에 종속성이 표시됩니다. 30분 이상 기다렸다면 아래 단계를 수행하여 MMA 에이전트가 OMS 작업 영역과 통신할 수 있는지 확인합니다.

Windows VM:
1. **제어판** 으로 이동 하 여 **Microsoft Monitoring Agent**를 시작 합니다.
2. MMA 속성 팝업의 **Azure Log Analytics (OMS)** 탭으로 이동 합니다.
3. 작업 영역의 **상태**가 녹색인지 확인합니다.
4. 상태가 녹색이 아니면 작업 영역을 제거했다가 MMA에 다시 추가해 봅니다.
        ![MMA 상태](./media/troubleshooting-general/mma-status.png)

Linux VM의 경우 MMA 및 종속 에이전트 설치 명령이 정상적으로 실행되었는지 확인합니다.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>MMA에서는 어떤 운영 체제가 지원되나요?

MMA에서 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)에 나와 있습니다.
MMA에서 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)에 나와 있습니다.

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>종속성 에이전트는 어떤 운영 체제를 지원하나요?

종속성 에이전트가 지원하는 Windows 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)에 나와 있습니다.
종속성 에이전트가 지원하는 Linux 운영 체제 목록은 [여기](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)에 나와 있습니다.

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>1시간이 넘는 기간에 대해서는 Azure Migrate의 종속성을 시각화할 수 없나요?
Azure Migrate에서는 최대 1시간 동안의 종속성을 시각화할 수 있습니다. 하지만 Azure Migrate에서는 최대 1개월 전의 특정 날짜로 돌아가 종속성을 시각화할 수 있으며 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 종속성을 확인할 수 있는 시간은 1시간입니다. 그러나 Azure Monitor 로그를 사용하면 더 긴 기간 동안의 [종속성 데이터를 쿼리](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)할 수 있습니다.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>VM이 10개보다 많은 그룹의 종속성은 시각화할 수 없나요?
[그룹의 종속성 시각화](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 시에 허용되는 최대 VM 수는 10개입니다. VM이 10개보다 많은 그룹이 있다면 더 작은 그룹 여러 개로 분할한 다음 종속성을 시각화하는 것이 좋습니다.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>에이전트를 설치하고 종속성 시각화 그룹을 사용하여 그룹을 만들었습니다. 장애 조치(failover) 이후, 컴퓨터가 "종속성 보기" 대신 "에이전트 설치" 작업을 표시합니다.
* 계획된 또는 계획되지 않은 장애 조치(failover) 후에는 온-프레미스 컴퓨터가 꺼지고 동급 컴퓨터가 Azure에 생성됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다. 사용자가 온-프레미스 IP 주소를 유지하는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다. MAC 및 IP 주소가 모두 다르면 Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결하지 않으며, 종속성을 보는 대신 사용자에게 에이전트를 설치하라고 요청합니다.
* 테스트 장애 조치(failover) 후에도 온-프레미스 컴퓨터는 예상대로 계속 켜져 있습니다. Azure에서 생성된 동급 컴퓨터는 다른 MAC 주소를 획득하며 다른 IP 주소를 획득할 수도 있습니다. 사용자가 이러한 컴퓨터에서 나가는 Azure Monitor를 기록 하는 것을 차단 하지 않는 한, Azure Migrate는 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않으며, 종속성을 보는 대신 사용자에 게 에이전트를 설치 하 라고 요청 합니다.

## <a name="collect-azure-portal-logs"></a>Azure Portal 로그 수집

**Azure Portal 네트워크 트래픽 로그를 수집할 어떻게 할까요? 있나요?**

1. 브라우저를 열고 포털에 로그인 [하](https://portal.azure.com)여 탐색 합니다.
2. F12 키를 눌러 개발자 도구를 시작합니다. 필요한 경우 **탐색에 대한 항목 지우기** 설정을 선택 취소합니다.
3. **네트워크** 탭을 클릭하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 표시 되지 않으면 검정색 원을 클릭 하 여 시작 합니다.
   - Microsoft Edge/IE에서는 자동으로 기록이 시작됩니다. 자동으로 시작되지 않으면 녹색 재생 단추를 클릭합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - 크롬에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content**를 클릭합니다. 로그를 압축 하 고 har 파일로 내보냅니다.
   - Microsoft Edge/IE에서는 **캡처된 트래픽 내보내기** 아이콘을 클릭합니다. 로그를 압축 하 고 내보냅니다.
6. **콘솔** 탭으로 이동하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하는 방법은 다음과 같습니다.
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. 다른 **이름으로 저장**을 선택 하 고 로그를 내보내고 압축 합니다.
   - Microsoft Edge/IE의 경우 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사**를 선택합니다.
7. 개발자 도구를 닫습니다.
