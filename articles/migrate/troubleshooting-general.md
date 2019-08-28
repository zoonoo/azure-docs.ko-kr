---
title: Azure Migrate 문제 해결 | Microsoft Docs
description: Azure Migrate 서비스의 알려진 문제에 대 한 개요 및 일반적인 오류에 대 한 문제 해결 팁을 제공 합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: fa1e7fcf89ccc06e429831191ba5dfce3cf33797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828321"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate 문제 해결

[Azure Migrate](migrate-services-overview.md) 는 타사 ISV (독립 소프트웨어 공급 업체) 제품 뿐만 아니라 평가 및 마이그레이션에 대 한 도구 허브를 제공 합니다. 이 문서는 Azure Migrate, Azure Migrate 서버 평가 및 Azure Migrate 서버 마이그레이션과 관련 된 오류를 해결 하는 데 도움이 됩니다.

## <a name="azure-migrate-project-issues"></a>Azure Migrate 프로젝트 문제

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>기존 Azure Migrate 프로젝트를 찾을 수 없습니다.

Azure Migrate의 [두 가지 버전이](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 있습니다. 프로젝트를 만든 버전에 따라 다음 방법 중 하나를 사용 합니다.

* 이전 버전의 Azure Migrate을 사용 하 여 만든 프로젝트를 찾고 있는 경우 다음 단계에 따라 프로젝트를 찾습니다.

    1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
    2. Azure Migrate 대시보드에서 오래 된 프로젝트에 액세스 하는 것을 언급 하는 배너가 표시 됩니다. 이전 환경에서 프로젝트를 만든 경우에만이 배너가 표시 됩니다. 배너를 선택 합니다.

       ![기존 프로젝트 액세스](./media/troubleshooting-general/access-existing-projects.png)

    3. 이제 이전 버전의 Azure Migrate을 사용 하 여 만든 기존 프로젝트의 목록이 표시 됩니다.

* 최신 버전을 사용 하 여 만든 프로젝트를 찾고 있는 경우 (새로운 환경) 다음 단계를 수행 하 여 프로젝트를 찾습니다.

    1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
    2. Azure Migrate 대시보드에서 왼쪽 창의 **서버** 페이지로 이동 하 고 오른쪽 위 모서리에서 **변경** 을 선택 합니다.

       ![기존 Azure Migrate 프로젝트로 전환](./media/troubleshooting-general/switch-project.png)

    3. 적절 한 구독 및 Azure Migrate 프로젝트를 선택 합니다.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>두 번째 Azure Migrate 프로젝트 어떻게 할까요? 만들려면

새 Azure Migrate 프로젝트를 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동 하 여 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드에서 왼쪽 창의 **서버** 페이지로 이동 하 고 오른쪽 위 모서리에서 **변경** 을 선택 합니다.

   ![Azure Migrate 프로젝트 변경](./media/troubleshooting-general/switch-project.png)

3. 새 프로젝트를 만들려면 **여기를 클릭**하십시오 .를 선택 합니다.

   ![두 번째 Azure Migrate 프로젝트 만들기](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>어떤 Azure 지역에서 지원 Azure Migrate 있나요?

[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) 및 [hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)에 대 한 목록을 참조 하세요.

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>어떻게 할까요? Azure Migrate 프로젝트 및 연결 된 Log Analytics 작업 영역을 삭제 하 시겠습니까?

Azure Migrate 프로젝트를 삭제 하면 검색 된 컴퓨터에 대 한 마이그레이션 프로젝트 *와* 메타 데이터가 삭제 됩니다. 그러나 Log Analytics 작업 영역을 서버 평가 도구에 연결한 경우 Log Analytics 작업 영역이 자동으로 삭제 되지 않습니다. 동일한 Log Analytics 작업 영역을 여러 사용 사례에 사용할 수 있습니다. 또한 Log Analytics 작업 영역을 삭제 하려면 해당 작업 영역을 수동으로 수행 해야 합니다.

1. 프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.
     * 마이그레이션 프로젝트를 아직 삭제 하지 않은 경우 Essentials 섹션의 서버 평가 개요 페이지에서 작업 영역에 대 한 링크를 찾을 수 있습니다.

       ![LA 작업 영역](./media/troubleshooting-general/loganalytics-workspace.png)

     * 이미 마이그레이션 프로젝트를 삭제 한 경우 Azure Portal의 왼쪽 창에서 **리소스 그룹** 을 선택 합니다. 작업 영역을 만든 리소스 그룹으로 이동 하 여 검색 합니다.
2. [Azure Portal를 사용 하 여 Azure Log Analytics 작업 영역 삭제](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace)의 지침을 따릅니다.

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>"요청에 사용자 id 헤더를 포함 해야 합니다." 라는 오류가 발생 하 고 마이그레이션 프로젝트를 만들지 못했습니다.

이 문제는 조직의 Azure Active Directory (Azure AD) 테 넌 트에 액세스할 수 없는 사용자에 게 발생할 수 있습니다. 사용자가 Azure AD 테 넌 트에 처음으로 추가 되 면 사용자는 테 넌 트에 가입 하 라는 메일 초대를 받습니다. 사용자는 초대를 수락 하 여 테 넌 트에 성공적으로 추가 해야 합니다. 전자 메일을 볼 수 없는 경우 테 넌 트에 대 한 액세스 권한이 있는 사용자에 게 문의 하 고 [게스트 사용자에 게 초대 다시 보내기](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)에 지정 된 단계를 사용 하 여 초대를 다시 보내도록 요청 합니다.

초대 전자 메일이 수신 되 면 전자 메일을 열고 초대를 수락 하기 위한 링크를 선택 해야 합니다. 그런 다음 Azure Portal 로그 아웃 하 고 다시 로그인 해야 합니다. (브라우저 새로 고침이 작동 하지 않습니다.) 그런 다음 마이그레이션 프로젝트 만들기를 시작할 수 있습니다.

## <a name="appliance-issues"></a>어플라이언스 문제

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>"제공 된 매니페스트 파일이 잘못 되었습니다."와 함께 VMware에 대 한 Azure Migrate 어플라이언스를 배포 하지 못했습니다. 잘못 된 않았기 매니페스트 항목 "오류입니다.

1. 해당 해시 값을 확인 하 여 Azure Migrate 어플라이언스 OVA 파일이 올바르게 다운로드 되었는지 확인 합니다. 지침은 [Azure로의 평가 및 마이그레이션을 위해 VMware Vm 준비](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware)를 참조 하세요. 해시 값이 일치 하지 않으면 OVA 파일을 다시 다운로드 하 고 배포를 다시 시도 합니다.
2. 배포가 여전히 실패 하 고 VMware vSphere 클라이언트를 사용 하 여 파일을 배포 하는 경우에는 vSphere 웹 클라이언트를 통해 배포 해 봅니다. 그래도 배포가 실패 하면 다른 웹 브라우저를 사용해 보세요.
3. VSphere 웹 클라이언트를 사용 하 고 vCenter Server 6.5 또는 6.7에 배포 하려는 경우 다음 단계를 수행 하 여 ESXi 호스트에 직접 OVA를 배포 해 보세요.
   - 웹 클라이언트 (https://<*호스트 IP 주소*>/du)를 사용 하 여 ESXi 호스트에 직접 연결 (vCenter Server 대신) 합니다.
   - **홈** > **인벤토리**로 이동 합니다.
   - **파일 배포 파일** > **배포 템플릿**을 선택 합니다. OVA로 이동 하 여 배포를 완료 합니다.
4. 배포가 여전히 실패할 경우 Azure Migrate 지원 담당자에 게 문의 하세요.

### <a name="the-appliance-cant-connect-to-the-internet"></a>어플라이언스에서 인터넷에 연결할 수 없습니다.

이 동작은 사용 중인 컴퓨터가 프록시 뒤에 있는 경우에 발생할 수 있습니다. 프록시에 필요한 경우 권한 부여 자격 증명을 제공 해야 합니다.
URL 기반 방화벽 프록시를 사용 하 여 아웃 바운드 연결을 제어 하는 경우 다음 필수 Url을 허용 목록에 추가 해야 합니다.

- [VMware에 대 한 서버 평가](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Hyper-v에 대 한 서버 평가](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [VMware에 대 한 서버 마이그레이션 (에이전트 없음)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [VMware에 대 한 서버 마이그레이션 (에이전트 기반)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Hyper-v에 대 한 서버 마이그레이션](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

가로채기 프록시를 사용 하 여 인터넷에 연결 하는 경우에는 프록시 인증서를 어플라이언스 VM으로 가져와야 합니다. [Azure Migrate 어플라이언스](https://docs.microsoft.com/azure/migrate/concepts-collector)의 단계를 수행 하 여 프록시 인증서를 가져올 수 있습니다.

### <a name="error-802-date-and-time-synchronization-error"></a>오류 802: 날짜 및 시간 동기화 오류입니다.

서버 클록이 현재 시간과 5 분 넘게 동기화 되지 않았을 수 있습니다. 다음과 같이 VM 수집기에서 시계 시간을 현재 시간과 일치하도록 변경합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인 하려면 **w32tm/crers**를 실행 합니다.
3. 시간을 동기화 하려면 **w32tm/resync**를 실행 합니다.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>연결 하지 못했습니다. 오류: UnableToConnectToServer.

VCenter Server *Servername*: 9443에 연결 하지 못할 수 있습니다. 오류 세부 정보는 메시지를 수락할 수 있는 https://: 9443/sdk에서 수신 대기 하는 끝점이 없음을 표시 합니다.

이 경우 최신 버전의 수집기 어플라이언스를 실행 하 고 있는지 확인 합니다. 그렇지 않은 경우 어플라이언스를 [최신 버전](https://docs.microsoft.com/azure/migrate/concepts-collector)으로 업그레이드 하세요.

최신 버전에서 문제가 계속 발생 하는 경우 수집기 컴퓨터에서 지정 된 vCenter Server 이름을 확인할 수 없거나 지정 된 포트가 잘못 되었을 수 있습니다. 기본적으로 포트를 지정 하지 않으면 수집기가 포트 번호 443에 연결을 시도 합니다.

1. 수집기 컴퓨터에서 *Servername*.com을 Ping 합니다.
2. 1 단계가 실패 하면 IP 주소를 사용 하 여 vCenter server에 연결 해 봅니다.
3. vCenter에 연결할 정확한 포트 번호를 식별합니다.
4. VCenter 서버가 실행 중인지 확인 합니다.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>기기가 Azure Migrate 프로젝트에 성공적으로 등록 되지 않았을 수 있습니다 (오류 ID: 60052).

이 오류는 어플라이언스를 등록 하는 데 사용 되는 Azure 계정에 권한이 부족 한 경우에 발생 합니다. 어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다. 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>기기가 Azure Migrate 프로젝트에 성공적으로 등록 되지 않았을 수 있습니다 (오류 ID: 60039).

어플라이언스를 등록 하도록 선택한 Azure Migrate 프로젝트를 찾을 수 없습니다. 이 경우 등록에 실패 합니다. Azure Portal로 이동 하 여 프로젝트가 리소스 그룹에 있는지 확인 합니다. 프로젝트가 존재 하지 않는 경우 리소스 그룹에 새 Azure Migrate 프로젝트를 만들고 어플라이언스를 다시 등록 합니다. 새 Azure Migrate 프로젝트를 만드는 방법에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) .

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Azure Key Vault 관리 작업이 실패 했습니다 (오류 ID: 60030, 60031).

어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다. 또한 계정에 오류 메시지에 지정 된 키 자격 증명 모음에 대 한 액세스 권한이 있는지 확인 한 후 작업을 다시 시도 하십시오. 문제가 지속되면 Microsoft 지원에 문의하세요. 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>오류로 인해 검색을 시작할 수 없습니다. 호스트 또는 클러스터의 지정 된 목록에 대 한 작업이 실패 했습니다 (오류 ID: 60028).

VM 정보에 액세스 하거나 검색 하는 데 문제가 있어 오류에 나열 된 호스트에서 검색을 시작할 수 없습니다. 나머지 호스트를 추가 했습니다. **호스트 추가** 옵션을 사용 하 여 오류에 나열 된 호스트를 다시 추가 합니다. 유효성 검사 오류가 있는 경우 수정 지침을 검토 하 여 오류를 수정 하 고 **검색 저장 및 시작** 옵션을 다시 시도 합니다.

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Azure AD 작업이 실패 했습니다. Azure AD 응용 프로그램을 만들거나 업데이트 하는 동안 오류가 발생 했습니다 (오류 ID: 60025).

이 오류는 검색을 시작하는 데 사용되는 Azure 사용자 계정이 어플라이언스 등록에 사용되는 계정과 다른 경우에 발생합니다. 다음 방법 중 하나를 수행할 수 있습니다.
1. 검색을 시작 하는 사용자 계정이 어플라이언스를 등록 하는 데 사용한 것과 동일한 지 확인 합니다.
1. 검색 작업이 실패 하는 다른 사용자 계정에 AAD 응용 프로그램 액세스 권한을 제공 합니다.
1. Azure Migrate 프로젝트에 대해 이전에 만든 리소스 그룹을 삭제 하 고 다시 시작할 다른 리소스 그룹을 만듭니다.

AAD 응용 프로그램 사용 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .

## <a name="discovery-issues"></a>검색 이슈

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>검색을 시작 했지만 Azure Portal에서 검색 된 Vm이 표시 되지 않습니다. **서버 평가** 및 **서버 마이그레이션** 타일에 "검색 중" 상태가 표시 됩니다.
어플라이언스에서 검색을 시작한 후 검색 된 컴퓨터가 Azure Portal에 표시 될 때까지 기다립니다. VMware 검색에는 약 15 분이 걸리고 Hyper-v 검색의 경우 추가 된 호스트 당 약 2 분이 소요 됩니다. 이러한 대기 기간이 끝난 후에도 "검색이 진행 중입니다."가 계속 표시 되는 경우 **서버** 탭에서 **새로 고침** 을 선택 합니다. 서버 평가 및 서버 마이그레이션 타일에서 검색 된 서버 수가 표시 됩니다.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>온-프레미스 환경을 지속적으로 검색 하지만 온-프레미스 환경에서 삭제 된 Vm이 포털에 계속 표시 되는 어플라이언스를 사용 하 고 있습니다.

어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 되는 데 최대 30 분이 걸립니다. 30 분이 지난 후에도 최신 정보가 표시 되지 않으면 다음 단계를 수행 하 여 데이터를 새로 고칩니다.

1. 서버 Azure Migrate**서버 평가**로 이동 하 고 **개요**를 선택 합니다. > 
2. **관리**에서를 선택 **에이전트 상태**
3. **에이전트 새로 고침**을 선택 합니다. 에이전트 목록 아래에이 옵션이 표시 됩니다.
4. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 Vm에 대 한 최신 정보가 표시 됩니다.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>포털의 온-프레미스 Vm에 대 한 최신 정보가 표시 되지 않습니다.

어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 되는 데 최대 30 분이 걸립니다. 30 분이 지난 후에도 최신 정보가 표시 되지 않으면 다음 단계를 수행 하 여 데이터를 새로 고칩니다.

1. 서버 Azure Migrate**서버 평가**로 이동 하 고 **개요**를 선택 합니다. > 
2. **관리**에서를 선택 **에이전트 상태**
3. **에이전트 새로 고침**을 선택 합니다. 에이전트 목록 아래에이 옵션이 표시 됩니다.
4. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 Vm에 대 한 최신 정보가 표시 됩니다.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>서버 이름을 확인할 수 없기 때문에 호스트나 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9 (오류 ID: 50004).
어플라이언스에 대 한 Azure DNS 서비스에서 사용자가 제공한 클러스터 또는 호스트 이름을 확인할 수 없는 경우이 오류가 발생 합니다. 클러스터에서이 오류가 표시 되 면 클러스터의 FQDN (정규화 된 도메인 이름)을 제공 해 보십시오.

클러스터의 호스트에 대해이 오류가 나타날 수도 있습니다. 이 경우 어플라이언스는 클러스터에 연결할 수 있지만 클러스터에서 Fqdn이 아닌 호스트 이름을 반환 합니다.

이 오류를 해결 하려면 IP 주소와 호스트 이름의 매핑을 추가 하 여 어플라이언스의 hosts 파일을 업데이트 합니다.
1. 관리자 권한으로 메모장을 엽니다. 그런 다음 C:\Windows\System32\Drivers\etc\hosts 파일을 엽니다.
2. 행에 IP 주소 및 호스트 이름을 추가 합니다. 이 오류가 표시 되는 각 호스트 또는 클러스터에 대해 반복 합니다.
3. Hosts 파일을 저장 한 후 닫습니다.
4. 어플라이언스 관리 앱을 사용 하 여 어플라이언스를 호스트에 연결할 수 있는지 여부를 확인할 수 있습니다. 30 분 후 Azure Portal에서 해당 호스트에 대 한 최신 정보를 볼 수 있습니다.


## <a name="assessment-issues"></a>평가 문제

### <a name="azure-readiness-issues"></a>Azure 준비 문제

문제점 | 업데이트 관리
--- | ---
지원되지 않는 부팅 유형 | Azure는 EFI 부팅 유형을 사용 하는 Vm을 지원 하지 않습니다. 마이그레이션을 실행 하기 전에 부팅 유형을 BIOS로 변환 하는 것이 좋습니다. <br/><br/>Azure Migrate Server Migration을 사용 하 여 이러한 Vm의 마이그레이션을 처리할 수 있습니다. 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환 합니다.
조건부로 지원되는 Windows OS | OS가 지원 종료 날짜를 통과 했으며 [Azure에서 지원](https://aka.ms/WSosstatement)하기 위해 CSA (사용자 지정 지원 계약)가 필요 합니다. Azure로 마이그레이션하기 전에 OS를 업그레이드 하는 것이 좋습니다.
지원되지 않는 Windows OS | Azure는 [선택한 WINDOWS OS 버전만](https://aka.ms/WSosstatement)지원 합니다. Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드 하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure 보증에는 [선택한 LINUX OS 버전만](../virtual-machines/linux/endorsed-distros.md)있습니다. Azure로 마이그레이션하기 전에 컴퓨터의 OS를 업그레이드 하는 것이 좋습니다.
보증되지 않은 Linux OS | 컴퓨터가 Azure에서 시작 될 수 있지만 Azure는 OS 지원을 제공 하지 않습니다. Azure로 마이그레이션하기 전에 OS를 [보증 Linux 버전](../virtual-machines/linux/endorsed-distros.md) 으로 업그레이드 하는 것이 좋습니다.
알 수 없는 운영 체제 | VCenter Server에서 VM의 운영 체제가 "기타"로 지정 되었습니다. 이 동작은 VM의 Azure 준비 상태를 확인 하는 Azure Migrate 차단 합니다. 컴퓨터를 마이그레이션하기 전에 컴퓨터의 OS가 Azure에서 [지원](https://aka.ms/azureoslist) 되는지 확인 합니다.
지원되지 않는 OS 비트 | 32 비트 OS를 사용 하는 Vm은 Azure에서 부팅 될 수 있지만, Azure로 마이그레이션하기 전에 VM의 OS를 64 비트로 업그레이드 하는 것이 좋습니다.
Microsoft Visual Studio 구독이 필요 합니다. | 컴퓨터에서 Visual Studio 구독을 통해서만 지원 되는 Windows 클라이언트 OS가 실행 되 고 있습니다.
필요한 저장소 성능에 대한 VM을 찾을 수 없음 | 컴퓨터에 필요한 저장소 성능 (초당 입/출력 작업 수 [IOPS] 및 처리량)이 Azure VM 지원을 초과 합니다. 마이그레이션을 시작하기 전에 컴퓨터의 저장소 요구 사항을 낮춰봅니다.
필요한 네트워크 성능에 대한 VM을 찾을 수 없음 | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정한 위치에서 VM을 찾을 수 없음 | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
하나 이상의 적합하지 않은 디스크 | VM에 연결 된 하나 이상의 디스크가 Azure 요구 사항을 충족 하지 않습니다. Azure Migrate: 서버 평가는 현재 디스크 울트라 SSD 지원 하지 않으며 프리미엄 managed disks (32 TB)의 디스크 용량에 따라 디스크를 평가 합니다.  VM에 연결 된 각 디스크에 대해 < 디스크 크기가 64 TB (울트라 SSD 디스크에서 지원 됨) 인지 확인 합니다. 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나 Azure에서 [여러 디스크를 사용 하 여](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) 저장소 제한을 더 높게 설정 합니다. 각 디스크에 필요한 성능 (IOPS 및 처리량)이 Azure [관리 가상 머신 디스크](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)에서 지원 되는지 확인 합니다.
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수가 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기가 한도 초과 | Azure Migrate: 서버 평가는 현재 울트라 SSD 디스크를 지원 하지 않으며 프리미엄 디스크 제한 (32 TB)에 따라 디스크를 평가 합니다. 그러나 Azure는 최대 64 TB 크기의 디스크를 지원 합니다 (울트라 SSD 디스크에서 지원 됨). 마이그레이션 전에 디스크를 64 TB 미만으로 축소 하거나 Azure에서 여러 디스크를 사용 하 고 [함께 스트라이프](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) 하 여 더 높은 저장소 용량을 확보 하세요.
지정한 위치에서 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 저장소 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어를 포함하는 VM과 메모리를 찾을 수 없음 | Azure에서 적합 한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>평가 속성에서 Azure 제품으로 EA (기업계약)를 지정할 수 없습니다.
Azure Migrate 서버 평가는 현재 EA (기업계약) 기반 가격 책정을 지원 하지 않습니다. 이 제한 사항을 해결 하려면 Azure 제품으로 **종 량** 제를 사용 하 고 **할인** 속성을 사용 하 여 사용자 지정 할인을 지정 합니다. [평가를 사용자 지정하는 방법에 대해 자세히 알아보세요](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>서버 평가에서 Linux Vm의 "조건부로 준비"를 표시 하는 이유는 무엇 인가요?
서버 평가에는 온-프레미스 Vm에 설치 된 Linux 운영 체제의 부 버전을 검색 하지 못하도록 하는 알려진 간격이 있습니다. 예를 들어 RHEL 6.10의 경우 현재 서버 평가는 RHEL 6을 OS 버전으로 검색 합니다. Azure는 특정 버전의 Linux만 보증 때문에 Linux Vm은 현재 서버 평가에서 조건부로 준비 된 것으로 표시 되어 있습니다. [Azure linux 지원 설명서](https://aka.ms/migrate/selfhost/azureendorseddistros)를 검토 하 여 온-프레미스 VM에서 실행 되는 Linux OS가 azure에서 보증 여부를 확인할 수 있습니다. 보증 배포를 확인 한 후에는이 경고를 무시 해도 됩니다.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>서버 평가에서 권장 하는 VM SKU가 온-프레미스에 할당 된 것 보다 많은 코어와 더 많은 메모리를 포함 하는 이유는 무엇 인가요?
Server Assessment의 권장 VM SKU는 평가 속성에 따라 달라집니다. 서버 평가에서 다음과 같은 두 종류의 평가를 만들 수 있습니다. *성능 기반* 및 *온-프레미스로* 성능 기반 평가의 경우 서버 평가는 온-프레미스 vm (CPU, 메모리, 디스크 및 네트워크 사용률)의 사용률 데이터를 고려 하 여 온-프레미스 Vm의 올바른 대상 VM SKU를 확인 합니다. 또한 성능 기반 크기 조정의 경우 편안 하 게 활용 하는 것이 효과적인 사용률을 결정 하는 것으로 간주 됩니다. 온-프레미스 크기 조정의 경우 성능 데이터를 고려 하지 않으며 온-프레미스에 할당 된 항목에 따라 대상 SKU를 사용 하는 것이 좋습니다.

예를 들어 4 개의 코어와 8gb의 메모리를 포함 하는 온-프레미스 VM과 50% CPU 사용률 및 50%의 메모리 사용률이 있고 평가에서 편안 하 게 1.3가 지정 되어 있다고 가정해 보겠습니다. 평가의 크기 조정 기준이 **온-프레미스**인 경우 4 개 코어와 8gb의 메모리를 사용 하는 AZURE VM SKU를 사용 하는 것이 좋습니다.

그러나 크기 조정 기준이 성능 기반 이라고 가정 합니다. 유효한 CPU 및 메모리 사용률 (4 개 코어의 50% * 1.3 = 2.6 코어 및 50% 8gb memory * 1.3 = 5.3 GB memory)을 기반으로 4 개 코어 (지원 되는 가장 가까운 코어 수)와 8gb의 메모리 (지원 되는 가장 가까운 메모리 크기)의 가장 저렴 한 VM SKU를 사용 하는 것이 좋습니다. [Server Assessment가 크기 조정을 수행하는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>서버 평가에서 권장 하는 디스크 SKU가 온-프레미스에 할당 된 것 보다 큰 이유는 무엇 인가요?
서버 평가의 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다. 크기 조정 기준이 **성능 기반** 이 고 저장소 유형이 **자동**으로 설정 된 경우 디스크의 IOPS 및 처리량 값은 대상 디스크 유형 (표준 HDD, 표준 SSD 또는 프리미엄 디스크)을 식별 하는 것으로 간주 됩니다. 그런 다음 디스크 유형의 디스크 SKU를 권장 하며,이 권장 사항은 온-프레미스 디스크의 크기 요구 사항을 고려 합니다. 크기 조정 기준이 **성능 기반**이며 저장소 유형이 **프리미엄**인 경우 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 AZURE의 premium disk SKU를 사용 하는 것이 좋습니다. 크기 조정 기준이 **온-프레미스** 이 고 저장소 유형이 **표준 HDD**, **표준 SSD**또는 **Premium**인 경우 동일한 논리를 사용 하 여 디스크 크기를 조정 합니다.

예를 들어 32 GB의 메모리를 포함 하는 온-프레미스 디스크가 있지만 디스크에 대 한 집계 된 읽기 및 쓰기 IOPS가 800 IOPS 인 경우 서버 평가는 더 높은 IOPS 요구 사항으로 인해 프리미엄 디스크 유형을 권장 하 고 다음을 지원할 수 있는 디스크 SKU를 권장 합니다. 필요한 IOPS 및 크기입니다. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 온-프레미스 디스크에 필요한 크기가 32 GB 이더라도 서버 평가는 온-프레미스 디스크의 높은 IOPS 요구 사항으로 인해 더 큰 디스크를 권장 합니다.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>내 평가 보고서에서 일부 Vm에 대해 "PercentageOfCoresUtilizedMissing" 또는 "PercentageOfMemoryUtilizedMissing"를 나타내는 이유는 무엇 인가요?
이러한 문제는 Azure Migrate 어플라이언스에서 온-프레미스 Vm에 대 한 성능 데이터를 수집할 수 없을 때 보고 됩니다. 이 동작은 평가를 만드는 동안 Vm이 꺼져 있는 경우 (지난 1 일/1 주/1 개월)에 발생할 수 있습니다. 기기가 꺼져 있으면 기기가 VM에 대 한 성능 데이터를 수집할 수 없습니다. 메모리 카운터만 없고 Hyper-v Vm을 평가 하려는 경우 이러한 Vm에서 동적 메모리를 사용 하도록 설정 했는지 확인 합니다. Azure Migrate 어플라이언스에서 동적 메모리를 사용 하지 않는 Vm에 대 한 메모리 사용률 데이터를 수집할 수 없는 알려진 문제가 있습니다. 이 문제는 VMware Vm이 아닌 Hyper-v Vm에만 영향을 줍니다. 성능 카운터가 없는 경우 Azure Migrate 서버 평가는 할당 된 코어 및 메모리를 대체 하 고 해당 VM 크기를 권장 합니다.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>서버 평가에서 예상 하는 계산 비용에 포함 된 VM의 OS 라이선스 비용은 어떻습니까?
서버 평가는 현재 Windows 컴퓨터에 대해서만 OS 라이선스 비용을 고려 합니다. Linux 컴퓨터에 대 한 OS 라이선스 비용은 현재 고려 되지 않습니다.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>성능 기록과 백분위 수 사용률이 크기 권장 사항에 미치는 영향은 무엇입니까?
이러한 속성은 성능 기반 크기 조정에만 적용 됩니다. Server Assessment는 온-프레미스 머신의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 이 성능 데이터는 다음과 같이 서버 평가에 의해 수집 됩니다.
- Azure Migrate 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 VMware Vm에 대해 실시간 사용률 데이터를 수집 하 고 Hyper-v Vm의 경우 30 초 마다 수집 합니다.
- 어플라이언스는 20 초 및 30 초 샘플을 롤업 하 여 10 분 마다 단일 데이터 요소를 만듭니다. 단일 데이터 요소를 만들기 위해 어플라이언스는 모든 20 초 및 30 초 샘플에서 최고 값을 선택 하 고 Azure에 보냅니다.
- Server Assessment에서 성능 기간 및 성능 기록 백분위수 값을 기준으로 평가를 작성할 때 대표 사용률 값이 식별됩니다. 예를 들어 성능 기록이 1 주일이 고 백분위 수 사용률이 95 인 경우 마지막 1 주에 대 한 10 분 샘플 요소를 모두 오름차순으로 정렬 하 고 95 번째 백분위 수를 대표 값으로 선택 Azure Migrate.
95 번째 백분위 수 값은 99 번째 백분위 수를 선택 하는 경우 포함 될 수 있는 이상 값을 무시 하도록 합니다. 기간에 대 한 피크 사용을 선택 하 고 이상 값을 누락 하지 않으려면 백분위 수 사용률에 대해 99 번째 백분위 수를 선택 해야 합니다.

## <a name="dependency-visualization-issues"></a>종속성 시각화 문제

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Azure Government 프로젝트에 대 한 종속성 시각화 기능을 찾을 수 없습니다.

Azure Migrate는 종속성 시각화 기능의 서비스 맵에 따라 달라 집니다. 서비스 맵 현재 Azure Government에서 사용할 수 없기 때문에 Azure Government에서이 기능을 사용할 수 없습니다.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>온-프레미스 VM에 MMA(Microsoft Monitoring Agent) 및 종속성 에이전트를 설치했는데 종속성이 Azure Migrate 포털에 표시됩니다.

에이전트를 설치한 후 Azure Migrate은 일반적으로 포털에서 종속성을 표시 하는 데 15-30 분 정도 걸립니다. 30 분 넘게 기다린 경우 다음 단계를 수행 하 여 MMA가 OMS 작업 영역과 통신할 수 있는지 확인 합니다.

Windows VM:
1. 제어판으로 이동 하 여 Microsoft Monitoring Agent를 시작 합니다.
2. **Microsoft Monitoring Agent 속성** 대화 상자의 **AZURE Log Analytics (OMS)** 탭에서 작업 영역의 **상태가** 녹색 인지 확인 합니다.
3. 상태가 녹색이 아니면 작업 영역을 제거했다가 MMA에 다시 추가해 봅니다.

      ![MMA 속성 대화 상자](./media/troubleshooting-general/mma-status.png)

Linux VM의 경우 MMA 및 종속성 에이전트의 설치 명령이 성공 했는지 확인 합니다.

### <a name="what-operating-systems-does-mma-support"></a>MMA에서 지 원하는 운영 체제는 무엇 인가요?

 [다음은 MMA에서 지 원하는 Windows 운영 체제의 목록](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)입니다.
다음은 [MMA에서 지원 되는 Linux 운영 체제의 목록](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)입니다.

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>종속성 에이전트에서 지 원하는 운영 체제는 무엇 인가요?

[다음은 종속성 에이전트에서 지 원하는 Windows 운영 체제의 목록](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)입니다. 다음은 [종속성 에이전트에서 지 원하는 Linux 운영 체제의 목록](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)입니다.

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>1 시간 이상 동안 Azure Migrate에서 종속성을 시각화할 수 없습니다.
Azure Migrate에서 최대 1 시간 동안 종속성을 시각화할 수 있습니다. Azure Migrate는 지난 달의 특정 날짜로 다시 이동할 수 있지만, 종속성을 시각화할 수 있는 최대 기간은 1 시간입니다.

예를 들어, 종속성 맵의 기간 기능을 사용 하 여 어제의 종속성을 볼 수 있지만이를 1 시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용하면 더 긴 기간 동안의 [종속성 데이터를 쿼리](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)할 수 있습니다.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Vm을 10 개 이상 포함 하는 그룹에 대 한 종속성을 시각화할 수 없습니다.
Vm을 10 개 이상 포함 하는 그룹 [에 대 한 종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 10 개 이상의 vm이 있는 그룹이 있는 경우 그룹을 더 작은 그룹으로 분할 하 고 종속성을 시각화 하는 것이 좋습니다.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>에이전트를 설치하고 종속성 시각화 그룹을 사용하여 그룹을 만들었습니다. 이제 사후 장애 조치 (failover) 후에 컴퓨터는 "종속성 보기" 대신 "에이전트 설치" 작업을 표시 합니다.
* 계획 되거나 계획 되지 않은 장애 조치 (failover) 후 온-프레미스 컴퓨터는 꺼지고 동일한 컴퓨터가 Azure에서 분리 됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다. 사용자가 온-프레미스 IP 주소를 유지 하도록 선택 했는지 여부에 따라 다른 IP 주소를 획득할 수 있습니다.

  MAC 주소와 IP 주소가 다를 경우 Azure Migrate 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않습니다. 대신, 종속성을 보는 대신 사용자에 게 에이전트를 설치 하도록 요청 합니다.
* 테스트 후 장애 조치 (failover) 후 온-프레미스 컴퓨터는 예상 대로 켜 집니다. Azure에서 분리 하는 동일한 컴퓨터는 다른 MAC 주소를 획득 하 고 다른 IP 주소를 획득할 수 있습니다. 사용자가 이러한 컴퓨터에서 나가는 Azure Monitor 로그 트래픽을 차단 하지 않으면 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않으며, 종속성을 보는 대신 사용자에 게 에이전트를 설치 하 라는 메시지를 표시 Azure Migrate.

## <a name="collect-azure-portal-logs"></a>Azure Portal 로그 수집

**Azure Portal 네트워크 트래픽 로그를 수집할 어떻게 할까요? 있나요?**

1. 브라우저를 열고 [포털로](https://portal.azure.com)이동 하 여 로그인 합니다.
2. F12 키를 눌러 개발자 도구를 시작 합니다. 필요한 경우 **탐색에서 항목 지우기** 설정을 선택 취소 합니다.
3. **네트워크** 탭을 선택 하 고 네트워크 트래픽 캡처를 시작 합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽이 캡처되고 있음을 나타냅니다. 빨간색 원이 나타나지 않으면 시작할 검정색 원을 선택 합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작 됩니다. 그렇지 않으면 녹색 재생 단추를 선택 합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서 마우스 오른쪽 단추를 클릭 하 고 **콘텐츠를 사용 하 여 HAR로 저장**을 선택 합니다. 이 작업은 로그를 압축 하 고 har 파일로 내보냅니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택 합니다. 이 작업은 로그를 압축 하 고 내보냅니다.
6. **콘솔** 탭을 선택 하 여 경고 또는 오류를 확인 합니다. 콘솔 로그를 저장하는 방법은 다음과 같습니다.
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. 다른 **이름으로 저장**을 선택 하 고 로그를 내보내고 압축 합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭 하 고 **모두 복사**를 선택 합니다.
7. 개발자 도구를 닫습니다.
