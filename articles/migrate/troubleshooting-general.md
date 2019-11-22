---
title: Azure Migrate 문제 해결 | Microsoft Docs
description: Azure Migrate 서비스의 알려진 문제에 대 한 개요 및 일반적인 오류에 대 한 문제 해결 팁을 제공 합니다.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 12f8f64c051d33ac2518edbe8b937521318a9e71
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284494"
---
# <a name="troubleshoot-azure-migrate"></a>Azure Migrate 문제 해결

[Azure Migrate](migrate-services-overview.md) 는 타사 ISV (독립 소프트웨어 공급 업체) 제품 뿐만 아니라 평가 및 마이그레이션에 대 한 도구 허브를 제공 합니다. 이 문서는 Azure Migrate, Azure Migrate 서버 평가 및 Azure Migrate 서버 마이그레이션과 관련 된 문제를 해결 하는 데 도움이 됩니다.


## <a name="find-a-project"></a>프로젝트 찾기

Azure Migrate의 [두 가지 버전이](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) 있습니다.


현재 버전의 Azure Migrate에서 Azure Migrate 프로젝트를 만든 경우 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드 > **서버**에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

    ![기존 Azure Migrate 프로젝트로 전환](./media/troubleshooting-general/switch-project.png)

3. 적절 한 구독 및 Azure Migrate 프로젝트를 선택 합니다.


이전 버전의 Azure Migrate에서 프로젝트를 만든 경우 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드의 이전 버전에서 프로젝트를 만든 경우 이전 프로젝트를 참조 하는 배너가 나타납니다. 배너를 선택 합니다.

    ![기존 프로젝트 액세스](./media/troubleshooting-general/access-existing-projects.png)

3. 이전 프로젝트의 목록을 검토 합니다.


## <a name="create-additional-projects"></a>추가 프로젝트 만들기

다음과 같이 새 Azure Migrate 프로젝트를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Migrate**를 검색 합니다.
2. Azure Migrate 대시보드 > **서버**에서 오른쪽 위 모서리에 있는 **변경** 을 선택 합니다.

   ![Azure Migrate 프로젝트 변경](./media/troubleshooting-general/switch-project.png)

3. 새 프로젝트를 만들려면 **여기를 클릭**하십시오 .를 선택 합니다.

   ![두 번째 Azure Migrate 프로젝트 만들기](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>지원 되는 지역 검토

[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) 및 [hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)에 대해 지원 되는 지역을 검토 합니다.

## <a name="delete-projectsworkspaces"></a>프로젝트/작업 영역 삭제

Azure Migrate 프로젝트 및 Log Analytics 작업 영역을 삭제 하는 경우 다음을 참고 하십시오.

- Azure Migrate 프로젝트를 삭제 하면 프로젝트 *와* 검색 된 컴퓨터에 대 한 메타 데이터가 삭제 됩니다.
- 서버 평가 도구에 Log Analytics 작업 영역을 연결한 경우 작업 영역이 자동으로 삭제 되지 않습니다.
- 여러 시나리오에 동일한 Log Analytics 작업 영역을 사용할 수 있습니다.
- Log Analytics 작업 영역을 삭제 하려면 해당 작업 영역을 수동으로 수행 해야 합니다.


### <a name="delete-a-project"></a>프로젝트 삭제

Azure Migrate의 현재 버전에서 프로젝트를 삭제 하려면 다음을 수행 합니다.

1. 프로젝트가 생성 된 Azure 리소스 그룹을 엽니다.
2. 리소스 그룹 페이지에서 **숨겨진 유형 표시**를 선택 합니다.
3. 삭제할 마이그레이션 프로젝트를 선택 합니다. 리소스 종류는 Microsoft. 마이그레이션/migrateprojects 이며 삭제 합니다.

이전 버전의 Azure Migrate에서 프로젝트를 삭제 하려면 다음을 수행 합니다.

1. 프로젝트가 생성 된 Azure 리소스 그룹을 엽니다.
2. 삭제할 마이그레이션 프로젝트를 선택 합니다. 리소스 종류는 마이그레이션 프로젝트 이며 삭제 합니다.


### <a name="delete-a-workspace"></a>작업 영역 삭제

프로젝트에 연결된 Log Analytics 작업 영역을 찾습니다.
* Azure Migrate 프로젝트를 삭제 하지 않은 경우 **Essentials** > **Server 평가**에서 작업 영역에 대 한 링크를 찾을 수 있습니다.
       ![LA 작업 영역](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>"요청에 사용자 id 헤더가 포함 되어야 합니다." 오류

프로젝트를 만들 때이 오류는 조직의 Azure Active Directory (Azure AD) 테 넌 트에 액세스할 수 없음을 나타낼 수 있습니다.

- Azure AD 테 넌 트에 처음 추가 하는 경우 테 넌 트에 가입 하 라는 전자 메일 초대를 받게 됩니다.
- 초대를 수락 하 여 테 넌 트에 성공적으로 추가 합니다.
    - 전자 메일을 볼 수 없는 경우 테 넌 트에 대 한 액세스 권한이 있는 사용자에 게 연락 하 여 사용자에 게 초대를 다시 [보내도록](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) 요청 합니다.
    - 초대 전자 메일을 받은 후이를 열고 초대를 수락 하기 위한 링크를 선택 합니다. 그런 다음 Azure Portal 로그 아웃 하 고 다시 로그인 합니다. 브라우저 새로 고침은 작동 하지 않습니다. 그런 다음 마이그레이션 프로젝트 만들기를 시작할 수 있습니다.


## <a name="error-invalid-ovf-manifest-entry"></a>"잘못 된 오류 매니페스트 항목" 오류

"제공 된 매니페스트 파일이 잘못 되었습니다. 잘못 된 위치 매니페스트 항목입니다." 오류가 표시 되 면 다음을 수행 합니다.

1. 해당 해시 값을 확인 하 여 Azure Migrate 어플라이언스 OVA 파일이 올바르게 다운로드 되었는지 확인 합니다. [자세히 알아봅니다](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). 해시 값이 일치 하지 않으면 OVA 파일을 다시 다운로드 하 고 배포를 다시 시도 합니다.
2. 그래도 배포가 실패 하 고 VMware vSphere 클라이언트를 사용 하 여 파일을 배포 하는 경우 vSphere 웹 클라이언트를 통해 배포 해 보세요. 그래도 배포가 실패 하면 다른 웹 브라우저를 사용해 보세요.
3. VSphere 웹 클라이언트를 사용 하 고 vCenter Server 6.5 또는 6.7에 배포 하려는 경우 ESXi 호스트에 직접 OVA를 배포 해 보세요.
   - 웹 클라이언트 (https://<*호스트 IP 주소*>/uiu)와 함께 ESXi 호스트에 직접 연결 (vCenter Server 대신) 합니다.
   - **Home** > **Inventory**에서 **파일** > 배포 파일 **템플릿 배포**를 선택 합니다. OVA로 이동 하 여 배포를 완료 합니다.
4. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

## <a name="appliance-cant-connect-to-the-internet"></a>어플라이언스에서 인터넷에 연결할 수 없음

이 문제는 어플라이언스 컴퓨터가 프록시 뒤에 있는 경우에 발생할 수 있습니다.

- 프록시에 필요한 경우 권한 부여 자격 증명을 제공 해야 합니다.
- URL 기반 방화벽 프록시를 사용 하 여 아웃 바운드 연결을 제어 하는 경우 다음 Url을 허용 목록에 추가 합니다.

    - [VMware 평가를 위한 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Hyper-v 평가를 위한 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [VMware 에이전트 없는 마이그레이션의 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [VMware 에이전트 기반 마이그레이션의 URL](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Hyper-v 마이그레이션의 Url](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- 가로채기 프록시를 사용 하 여 인터넷에 연결 하는 경우 [다음 단계](https://docs.microsoft.com/azure/migrate/concepts-collector)를 사용 하 여 프록시 인증서를 어플라이언스 VM으로 가져옵니다.

## <a name="errordatetime-synchronization"></a>오류: 날짜/시간 동기화

날짜 및 시간 동기화 (802)에 대 한 오류는 서버 클록이 현재 시간과 5 분 넘게 동기화 되지 않았을 수 있음을 나타냅니다. 현재 시간과 일치 하도록 수집기 VM의 클록 시간을 변경 합니다.

1. VM에서 관리자 명령 프롬프트를 엽니다.
2. 표준 시간대를 확인 하려면 **w32tm/crers**를 실행 합니다.
3. 시간을 동기화 하려면 **w32tm/resync**를 실행 합니다.


## <a name="error-unabletoconnecttoserver"></a>오류: UnableToConnectToServer

이 연결 오류가 발생 하는 경우 vCenter Server *Servername*: 9443에 연결 하지 못할 수 있습니다. 오류 세부 정보는 메시지를 수락할 수*있는 https://:* 9443/sdk에서 수신 대기 하는 끝점이 없음을 표시 합니다.

- 최신 버전의 어플라이언스를 실행 하 고 있는지 확인 합니다. 그렇지 않은 경우 어플라이언스를 [최신 버전](https://docs.microsoft.com/azure/migrate/concepts-collector)으로 업그레이드 하세요.
- 최신 버전에서 문제가 계속 발생 하는 경우 기기가 지정 된 vCenter Server 이름을 확인할 수 없거나 지정 된 포트가 잘못 되었을 수 있습니다. 기본적으로 포트를 지정 하지 않으면 수집기가 포트 번호 443에 연결을 시도 합니다.

    1. 어플라이언스에서 *Servername*.com을 Ping 합니다.
    2. 1 단계가 실패 하면 IP 주소를 사용 하 여 vCenter server에 연결 해 봅니다.
    3. VCenter Server 연결할 올바른 포트 번호를 식별 합니다.
    4. VCenter Server 실행 중인지 확인 합니다.


## <a name="error-appliance-might-not-be-registered"></a>오류: 기기가 등록 되지 않았을 수 있습니다.

- 오류 60052, "어플라이언스를 Azure Migrate 프로젝트에 성공적으로 등록 하지 못할 수 있습니다."는 어플라이언스를 등록 하는 데 사용 된 Azure 계정에 권한이 부족 한 경우 발생 합니다.
    - 어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다.
    - 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .
- 오류 60039, "어플라이언스를 등록 하는 데 사용 된 Azure Migrate 프로젝트를 찾을 수 없어 등록이 실패 하는 경우" 기기가 Azure Migrate 프로젝트에 성공적으로 등록 되지 않을 수 있습니다. "가 발생할 수 있습니다.
    - Azure Portal에서 프로젝트가 리소스 그룹에 있는지 여부를 확인 합니다.
    - 프로젝트가 존재 하지 않는 경우 리소스 그룹에 새 Azure Migrate 프로젝트를 만들고 어플라이언스를 다시 등록 합니다. 새 프로젝트를 만드는 [방법에 대해 알아봅니다](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) .

## <a name="error-key-vault-management-operation-failed"></a>오류: Key Vault 관리 작업에 실패 했습니다.

60030 또는 60031 오류 "Azure Key Vault 관리 작업에 실패 했습니다."가 표시 되 면 다음을 수행 합니다.
- 어플라이언스를 등록 하는 데 사용 되는 Azure 사용자 계정에 구독에 대 한 참가자 이상의 권한이 있는지 확인 합니다.
- 계정에 오류 메시지에 지정 된 키 자격 증명 모음에 대 한 액세스 권한이 있는지 확인 한 후 작업을 다시 시도 하십시오.
- 문제가 지속되면 Microsoft 지원에 문의하세요.
- 필요한 Azure 역할 및 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .

## <a name="fix-discovery-couldnt-be-initiated"></a>Fix: 검색을 시작할 수 없습니다.

오류 60028: "오류로 인해 검색을 시작할 수 없습니다. 지정 된 호스트 또는 클러스터 목록에 대 한 작업이 실패 했습니다. "는 VM 정보에 액세스 하거나 검색 하는 데 문제가 있어 오류에 나열 된 호스트에서 검색을 시작할 수 없음을 나타냅니다. 나머지 호스트를 추가 했습니다.

- **호스트 추가** 옵션을 사용 하 여 오류에 나열 된 호스트를 다시 추가 합니다.
- 유효성 검사 오류가 있는 경우 수정 지침을 검토 하 여 오류를 수정 하 고 **검색 저장 및 시작** 옵션을 다시 시도 합니다.

## <a name="fix-azure-ad-operation-failed-60025"></a>해결 방법: Azure AD 작업이 실패 했습니다. (60025)

오류 60025: "Azure AD 작업이 실패 했습니다. Azure AD 응용 프로그램을 만들거나 업데이트 하는 동안 발생 한 오류는 검색을 시작 하는 데 사용 되는 Azure 사용자 계정이 어플라이언스를 등록 하는 데 사용 된 계정과 다를 때 발생 합니다. 다음 중 하나를 수행합니다.

- 검색을 시작 하는 사용자 계정이 어플라이언스를 등록 하는 데 사용한 것과 동일한 지 확인 합니다.
- 검색 작업이 실패 하는 사용자 계정에 Azure Active Directory 응용 프로그램 액세스 권한을 제공 합니다.
- Azure Migrate 프로젝트에 대해 이전에 만든 리소스 그룹을 삭제 합니다. 다시 시작할 다른 리소스 그룹을 만듭니다.
- Azure Active Directory 응용 프로그램 사용 권한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) .


## <a name="discovered-vms-not-in-portal"></a>검색 된 Vm이 포털에 없음

검색을 시작 하 여 **서버 평가** 및 **서버 마이그레이션을** 진행 중인 **검색**을 표시 하지만 아직 포털에서 vm이 표시 되지 않도록 하려면 다음을 참고 하세요.

- 어플라이언스에서 검색을 시작한 후 VMware VM 검색에 대해 15 분 정도 걸리며, Hyper-v VM 검색에 추가 된 각 호스트에 대해 약 2 분이 소요 됩니다.
- 이러한 대기 기간 후에도 계속 **진행 중인 검색** 을 확인 하는 경우 **서버** 탭에서 **새로 고침** 을 선택 합니다. **서버 평가** 및 **서버 마이그레이션의**검색 된 서버 수가 표시 됩니다.


## <a name="deleted-vms-in-the-portal"></a>포털에서 Vm 삭제 됨

온-프레미스 환경을 지속적으로 검색 하지만 삭제 된 Vm이 포털에 계속 표시 되는 어플라이언스를 배포한 경우에는 다음 사항에 유의 하세요.  

- 어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 되는 데 최대 30 분이 걸립니다.
- 30 분 후에 최신 정보가 표시 되지 않으면 다음 단계를 수행 하 여 데이터를 새로 고칩니다.

    1. 서버 ** > ** **Azure Migrate 서버 평가**에서 **개요**를 선택 합니다.
    2. **관리**에서를 선택 **에이전트 상태**
    3. **에이전트 새로 고침**을 선택 합니다.
    1. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 최신 정보가 표시 됩니다.

## <a name="vm-information-isnt-in-the-portal"></a>포털에 VM 정보가 없습니다.

- 어플라이언스에 의해 수집 된 검색 데이터가 포털에 반영 되는 데 최대 30 분이 걸립니다.
- 30 분 후에 최신 정보가 표시 되지 않으면 다음 단계를 수행 하 여 데이터를 새로 고칩니다.

    1. 서버 ** > ** **Azure Migrate 서버 평가**에서 **개요**를 선택 합니다.
    2. **관리**에서를 선택 **에이전트 상태**
    3. **에이전트 새로 고침**을 선택 합니다.
    1. 새로 고침 작업이 완료 될 때까지 기다립니다. 이제 최신 정보가 표시 됩니다.


## <a name="fix-cant-connect-to-host-or-cluster"></a>해결 방법: 호스트 또는 클러스터에 연결할 수 없습니다.

오류 50004: "서버 이름을 확인할 수 없기 때문에 호스트 또는 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9 "는 어플라이언스에 대 한 Azure DNS 서비스에서 사용자가 제공한 클러스터 또는 호스트 이름을 확인할 수 없을 때 발생할 수 있습니다.

- 클러스터에이 오류가 표시 되 면 클러스터 FQDN을 설정 합니다.
- 클러스터의 호스트에 대해이 오류가 나타날 수도 있습니다. 이는 어플라이언스를 클러스터에 연결할 수 있지만 클러스터에서 Fqdn이 아닌 호스트 이름을 반환 한다는 것을 나타냅니다. 이 오류를 해결 하려면 IP 주소와 호스트 이름의 매핑을 추가 하 여 어플라이언스의 hosts 파일을 업데이트 합니다.
    1. 관리자 권한으로 메모장을 엽니다.
    2. C:\Windows\System32\Drivers\etc\hosts 파일을 엽니다.
    3. 행에 IP 주소 및 호스트 이름을 추가 합니다. 이 오류가 표시 되는 각 호스트 또는 클러스터에 대해 반복 합니다.
    4. Hosts 파일을 저장 한 후 닫습니다.
    5. 어플라이언스 관리 앱을 사용 하 여 어플라이언스를 호스트에 연결할 수 있는지 여부를 확인 합니다. 30 분 후 Azure Portal에 이러한 호스트에 대 한 최신 정보가 표시 됩니다.

## <a name="application-discovery-issues"></a>응용 프로그램 검색 문제

응용 프로그램 검색은 현재 VMware Vm에 대해서만 지원 됩니다. Hyper-v Vm 및 물리적 서버에 대 한 지원은 나중에 사용 하도록 설정 됩니다. 응용 프로그램을 검색 하려면 어플라이언스에서 VM 자격 증명을 제공 해야 합니다. VCenter Server 및 VMware Vm에 필요한 액세스 권한에 대해 자세히 알아보세요. 다음 문제 중 하나로 인해 검색이 실패할 수 있습니다. 문제를 해결 하려면 아래에서 설명한 권장 작업을 검토 하세요.

**오류 코드** | **메시지** | **가능한 원인** | **권장 작업**
--- | --- | --- | ---
10000 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 서버에서 실행 중인 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | 설치 된 응용 프로그램의 검색은 Windows 및 Linux 서버 에서만 지원 됩니다.
10001 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 이는 어플라이언스에 누락 된 파일이 있기 때문에 발생 하는 내부 오류입니다. | Microsoft 지원에 문의 하세요.
10002 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | Azure Migrate 어플라이언스의 검색 에이전트가 제대로 작동 하지 않는 경우 발생할 수 있습니다. | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
10003 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 검색 에이전트가 제대로 작동 하지 않는 경우 이러한 문제가 발생할 수 있습니다. | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
10004 | < Windows/Linux > 컴퓨터에 대해 설치 된 응용 프로그램을 검색할 수 없습니다. |  < Windows/Linux > 컴퓨터에 액세스 하기 위한 자격 증명을 Azure Migrate 어플라이언스에서 제공 하지 않았습니다. | < Windows/Linux > 컴퓨터에 대 한 액세스 권한이 있는 Azure Migrate 어플라이언스에 자격 증명을 추가 하세요.
10005 | 온-프레미스 서버에 액세스할 수 없습니다. | 이는 서버에 액세스 하기 위해 컴퓨터에 제공 된 자격 증명이 잘못 된 경우에 발생할 수 있습니다. | 어플라이언스에 제공 된 자격 증명을 업데이트 하 고 자격 증명을 사용 하 여 서버에 액세스할 수 있는지 확인 하세요.
10006 | 온-프레미스 서버에 액세스할 수 없습니다. | 서버에서 실행 중인 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | 설치 된 응용 프로그램의 검색은 Windows 및 Linux 서버 에서만 지원 됩니다.
9000 | VM에 설치 된 응용 프로그램을 검색할 수 없습니다. | VMware 도구가 설치 되어 있지 않거나 손상 되었을 수 있습니다. | VMware 도구를 VM에 설치/다시 설치 하 고 실행 중인지 확인 합니다.
9001 | VM에 설치 된 응용 프로그램을 검색할 수 없습니다. | VMware 도구가 설치 되어 있지 않거나 손상 되었을 수 있습니다. | VMware 도구를 VM에 설치/다시 설치 하 고 실행 중인지 확인 합니다.
9002 | VM에 설치 된 응용 프로그램을 검색할 수 없습니다. | VMware 도구가 실행 되 고 있지 않을 수 있습니다. | VMware 도구를 VM에 설치/다시 설치 하 고 실행 중인지 확인 합니다.
9003 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 서버에서 실행 중인 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | 설치 된 응용 프로그램의 검색은 Windows 및 Linux 서버 에서만 지원 됩니다.
9004 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | VM이 꺼진 경우에 발생할 수 있습니다. | 서버에서 설치 된 응용 프로그램을 검색 하려면 VM이 켜 졌는 지 확인 합니다.
9005 | VM에 설치 된 응용 프로그램을 검색할 수 없습니다. | 이는 VM에서 실행 중인 운영 체제가 Windows 또는 Linux가 아닌 경우에 발생할 수 있습니다. | 설치 된 응용 프로그램의 검색은 Windows 및 Linux 서버 에서만 지원 됩니다.
9006 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 검색 에이전트가 제대로 작동 하지 않는 경우 이러한 문제가 발생할 수 있습니다. | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
9007 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 검색 에이전트가 제대로 작동 하지 않는 경우 이러한 문제가 발생할 수 있습니다. | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
9008 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 내부 오류로 인해 문제가 발생할 수 있습니다.  | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
9009 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 서버의 Windows UAC (사용자 계정 컨트롤) 설정이 제한적이 고 설치 된 응용 프로그램의 검색을 방해 하는 경우이 문제가 발생할 수 있습니다. | 서버에서 ' 사용자 계정 컨트롤 ' 설정을 검색 하 고 서버에서 하위 두 수준 중 하나에 있는 UAC 설정을 구성 합니다.
9010 | 서버에 설치 된 응용 프로그램을 검색할 수 없습니다. | 내부 오류로 인해 문제가 발생할 수 있습니다.  | 이 문제는 24 시간 이내에 자동으로 해결 됩니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
8084 | VMware 오류로 인해 응용 프로그램을 검색할 수 없습니다. <Exception from VMware> | Azure Migrate 어플라이언스는 VMware Api를 사용 하 여 응용 프로그램을 검색 합니다. 응용 프로그램을 검색 하는 동안 vCenter Server에서 throw 된 예외로 인해이 문제가 발생할 수 있습니다. VMware의 오류 메시지는 포털에 표시 된 오류 메시지에 표시 됩니다. | [Vmware 설명서](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)를 검토 하 고 오류 메시지를 검색 한 다음 vmware 문서의 문제 해결 단계에 따라 문제를 해결 합니다. 그래도 문제를 해결할 수 없는 경우 Microsoft 지원에 연결 합니다.


## <a name="fix-assessment-readiness"></a>평가 준비 상태 수정

평가 준비 문제를 다음과 같이 수정 합니다.

**문제점** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 EFI 부팅 유형을 사용 하는 Vm을 지원 하지 않습니다. 마이그레이션을 실행 하기 전에 부팅 유형을 BIOS로 변환 하는 것이 좋습니다. <br/><br/>Azure Migrate Server Migration을 사용 하 여 이러한 Vm의 마이그레이션을 처리할 수 있습니다. 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환 합니다.
조건부로 지원 되는 Windows 운영 체제 | 운영 체제에서 지원 종료 날짜를 통과 했으며 [Azure에서 지원](https://aka.ms/WSosstatement)하기 위해 CSA (사용자 지정 지원 계약)가 필요 합니다. Azure로 마이그레이션하기 전에 업그레이드 하는 것이 좋습니다.
지원 되지 않는 Windows 운영 체제 | Azure는 [선택한 WINDOWS OS 버전만](https://aka.ms/WSosstatement)지원 합니다. Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드 하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure 보증에는 [선택한 LINUX OS 버전만](../virtual-machines/linux/endorsed-distros.md)있습니다. Azure로 마이그레이션하기 전에 컴퓨터를 업그레이드 하는 것이 좋습니다.
보증되지 않는 Linux OS | 컴퓨터가 Azure에서 시작 될 수 있지만 Azure는 운영 체제를 지원 하지 않습니다. Azure로 마이그레이션하기 전에 [보증 Linux 버전](../virtual-machines/linux/endorsed-distros.md) 으로 업그레이드 하는 것이 좋습니다.
알 수 없는 운영 체제 | VCenter Server에서 VM의 운영 체제가 "기타"로 지정 되었습니다. 이 동작은 VM의 Azure 준비 상태를 확인 하는 Azure Migrate 차단 합니다. 컴퓨터를 마이그레이션하기 전에 Azure에서 운영 체제가 [지원](https://aka.ms/azureoslist) 되는지 확인 합니다.
지원 되지 않는 비트 버전 | 32 비트 운영 체제를 사용 하는 Vm은 Azure에서 부팅 될 수 있지만, Azure로 마이그레이션하기 전에 64 비트로 업그레이드 하는 것이 좋습니다.
Microsoft Visual Studio 구독이 필요 합니다. | 컴퓨터에서 Visual Studio 구독을 통해서만 지원 되는 Windows 클라이언트 운영 체제를 실행 하 고 있습니다.
필요한 저장소 성능에 대한 VM을 찾을 수 없음 | 컴퓨터에 필요한 저장소 성능 (초당 입/출력 작업 수 [IOPS] 및 처리량)이 Azure VM 지원을 초과 합니다. 마이그레이션을 시작하기 전에 컴퓨터의 스토리지 요구 사항을 낮춰봅니다.
필요한 네트워크 성능에 대한 VM을 찾을 수 없음 | 컴퓨터에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 컴퓨터의 네트워킹 요구 사항을 낮춰봅니다.
지정한 위치에서 VM을 찾을 수 없음 | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
하나 이상의 적합하지 않은 디스크 | VM에 연결 된 하나 이상의 디스크가 Azure 요구 사항을 충족 하지 않습니다. 은<br/><br/> Azure Migrate: 서버 평가는 현재 울트라 SSD 디스크를 지원 하지 않으며 프리미엄 관리 디스크에 대 한 디스크 제한 (32 TB)에 따라 디스크를 평가 합니다.<br/><br/> VM에 연결 된 각 디스크에 대해 디스크 크기가 64 TB < (울트라 SSD 디스크에서 지원 됨) 확인 합니다.<br/><br/> 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나, Azure에서 여러 디스크를 사용 하 고 여러 디스크를 [함께 스트라이프](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) 하 여 저장소 제한을 더 늘려야 합니다. 각 디스크에 필요한 성능 (IOPS 및 처리량)이 Azure [관리 가상 머신 디스크](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)에서 지원 되는지 확인 합니다.
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 컴퓨터에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure Migrate: 서버 평가는 현재 울트라 SSD 디스크를 지원 하지 않으며 프리미엄 디스크 제한 (32 TB)에 따라 디스크를 평가 합니다.<br/><br/> 그러나 Azure는 최대 64 TB 크기의 디스크를 지원 합니다 (울트라 SSD 디스크에서 지원 됨). 마이그레이션을 수행 하기 전에 디스크를 64 TB 미만으로 축소 하거나 Azure에서 여러 디스크를 사용 하 고 [함께 스트라이프](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) 하 여 더 높은 저장소 제한을 얻습니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 스토리지 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure에서 적합 한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 컴퓨터의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터에 대 한 적합성을 확인할 수 없습니다. | 그룹에 대한 새 평가를 만들어 봅니다.

## <a name="linux-vms-are-conditionally-ready"></a>Linux Vm은 "조건부로 준비" 됩니다.

서버 평가는 서버 평가의 알려진 격차 때문에 Linux Vm을 "조건부로 준비"로 표시 합니다.

- 이 차이는 온-프레미스 Vm에 설치 된 Linux OS의 부 버전을 검색 하는 것을 방지 합니다.
- 예를 들어 RHEL 6.10의 경우 현재 서버 평가는 OS 버전으로 RHEL 6만 검색 합니다.
-  Azure는 특정 버전의 Linux만 보증 때문에 Linux Vm은 현재 서버 평가에서 조건부로 준비 된 것으로 표시 되어 있습니다.
- [Azure linux 지원](https://aka.ms/migrate/selfhost/azureendorseddistros)을 검토 하 여 온-프레미스 VM에서 실행 되는 Linux OS가 azure에서 보증 여부를 확인할 수 있습니다.
-  보증 배포를 확인 한 후에는이 경고를 무시 해도 됩니다.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Azure Sku가 온-프레미스 크기 조정을 초과 합니다.

Azure Migrate 서버 평가는 평가 유형에 따라 현재 온-프레미스 할당 보다 더 많은 코어 및 메모리를 사용 하는 Azure VM Sku를 권장할 수 있습니다.


- VM SKU 권장 사항은 평가 속성에 따라 달라 집니다.
- 이는 서버 평가에서 수행 하는 평가 유형 ( *성능 기반*또는 *온-프레미스)* 의 영향을 받습니다.
- 성능 기반 평가의 경우 서버 평가는 온-프레미스 vm (CPU, 메모리, 디스크 및 네트워크 사용률)의 사용률 데이터를 고려 하 여 온-프레미스 Vm의 올바른 대상 VM SKU를 확인 합니다. 또한 효과적인 사용률을 결정할 때 편안 하 게 요소를 추가 합니다.
- 온-프레미스 크기 조정의 경우 성능 데이터를 고려 하지 않으며 대상 SKU는 온-프레미스 할당을 기반으로 하는 것이 좋습니다.

권장 구성에 영향을 줄 수 있는 방법을 보여 주기 위해 예를 살펴보겠습니다.

50% CPU 사용률과 50%의 메모리 사용률과 지정 된 1.3 편안 하 게 코어와 8 GB의 메모리가 있는 온-프레미스 VM이 있습니다.

-  평가를 **온-프레미스로**사용 하는 경우 4 개 코어와 8gb의 메모리를 사용 하는 AZURE VM SKU를 사용 하는 것이 좋습니다.
- 평가는 유효한 CPU 및 메모리 사용률 50 (4 개 코어 * 1.3 = 2.6 코어 및 50% 8gb 메모리 * 1.3 = 5.3 GB 메모리)을 기반으로 하 여 성능에 따라 결정 됩니다. 4 개 코어 (지원 되는 가장 가까운 코어 수)와 8GB의 메모리 (가장 근접 하 게 지원 됨)의 VM SKU가 가장 저렴 합니다. 메모리 크기)를 권장 합니다.
- 평가 크기 조정에 대해 [자세히 알아보세요](concepts-assessment-calculation.md#sizing) .

## <a name="azure-disk-skus-bigger-than-on-premises"></a>온-프레미스 보다 큰 Azure disk Sku

Azure Migrate Server 평가는 평가 유형에 따라 더 큰 디스크를 권장할 수 있습니다.
- 서버 평가의 디스크 크기 조정은 두 가지 평가 속성인 크기 조정 조건 및 저장소 유형에 따라 달라 집니다.
- 크기 조정 기준이 성능에 **따라 결정**되 고 저장소 유형이 **자동**으로 설정 된 경우 대상 디스크 유형 (표준 HDD, 표준 SSD 또는 프리미엄)을 식별할 때 디스크의 IOPS 및 처리량 값이 고려 됩니다. 그런 다음 디스크 유형의 디스크 SKU를 권장 하며 권장 사항은 온-프레미스 디스크의 크기 요구 사항을 고려 합니다.
- 크기 조정 기준이 **성능 기반**이며 저장소 유형이 **프리미엄**인 경우 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 AZURE의 premium disk SKU를 사용 하는 것이 좋습니다. 크기 조정 기준이 **온-프레미스** 이 고 저장소 유형이 **표준 HDD**, **표준 SSD**또는 **Premium**인 경우 동일한 논리를 사용 하 여 디스크 크기를 조정 합니다.

예를 들어 32 GB의 메모리를 포함 하는 온-프레미스 디스크가 있지만 디스크에 대 한 집계 된 읽기/쓰기 IOPS는 800 IOPS 인 경우 서버 평가는 프리미엄 디스크 (IOPS 요구 사항)를 권장 하 고 r을 지원할 수 있는 디스크 SKU를 권장 합니다. e IOPS 및 크기. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 온-프레미스 디스크에 필요한 크기가 32 GB 이더라도 서버 평가는 온-프레미스 디스크의 높은 IOPS 요구 사항으로 인해 더 큰 디스크를 권장 합니다.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>해결 방법: 사용 된 코어 또는 메모리 누락 비율

Azure Migrate 어플라이언스에서 관련 온-프레미스 Vm에 대 한 성능 데이터를 수집할 수 없는 경우 서버 평가 보고서는 "PercentageOfCoresUtilizedMissing" 또는 "PercentageOfMemoryUtilizedMissing"입니다.

- 평가 기간 동안 Vm이 꺼져 있는 경우이 문제가 발생할 수 있습니다. 기기가 꺼져 있으면 기기가 VM에 대 한 성능 데이터를 수집할 수 없습니다.
- 메모리 카운터만 없고 Hyper-v Vm을 평가 하려는 경우 이러한 Vm에서 동적 메모리를 사용 하도록 설정 했는지 확인 합니다. Hyper-v Vm에 대 한 알려진 문제는 Azure Migrate 어플라이언스는 동적 메모리를 사용 하지 않는 Vm에 대 한 메모리 사용률 데이터를 수집할 수 없습니다.
- 성능 카운터가 없는 경우 Azure Migrate 서버 평가는 할당 된 코어 및 메모리를 대체 하 고 해당 VM 크기를 권장 합니다.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>VM OS 라이선스 비용이 비용 평가에 포함 되나요?

Azure Migrate Server 평가는 현재 Windows 컴퓨터에 대해서만 운영 체제 라이선스 비용을 고려 합니다. Linux 컴퓨터에 대 한 라이선스 비용은 현재 고려 되지 않습니다.

## <a name="performance-history-and-percentile-use"></a>성능 기록 및 백분위 수 사용

이러한 속성은 Azure Migrate Server 평가에서 성능 기반 크기 조정에만 적용 됩니다.

Server Assessment는 온-프레미스 머신의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 이 성능 데이터는 다음과 같이 서버 평가에 의해 수집 됩니다.
- Azure Migrate 어플라이언스는 온-프레미스 환경을 지속적으로 프로 파일링 하 여 20 초 마다 VMware Vm에 대해 실시간 사용률 데이터를 수집 하 고, Hyper-v Vm의 경우 30 초 마다 수집 합니다.
- 어플라이언스는 20 또는 30 초 샘플을 롤업 하 여 10 분 마다 단일 데이터 요소를 만듭니다. 단일 데이터 요소를 만들기 위해 어플라이언스는 모든 20 초 및 30 초 샘플에서 최고 값을 선택 하 고 Azure에 보냅니다.
- Server Assessment에서 성능 기간 및 성능 기록 백분위수 값을 기준으로 평가를 작성할 때 대표 사용률 값이 식별됩니다. 예를 들어 성능 기록이 1 주일이 고 백분위 수 사용률이 95 인 경우 마지막 1 주에 대 한 10 분 샘플 요소를 모두 오름차순으로 정렬 하 고 95 번째 백분위 수를 대표 값으로 선택 Azure Migrate.
- 95 번째 백분위 수 값은 99 번째 백분위 수를 선택 하는 경우 포함 될 수 있는 이상 값을 무시 하도록 합니다.
- 기간에 대 한 피크 사용을 선택 하 고 이상 값을 누락 하지 않으려면 백분위 수 사용률에 대해 99 번째 백분위 수를 선택 해야 합니다.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Azure Government에 대 한 종속성 시각화를 찾을 수 없습니다.

Azure Migrate는 종속성 시각화 기능의 서비스 맵에 따라 달라 집니다. 서비스 맵 현재 Azure Government에서 사용할 수 없기 때문에 Azure Government에서이 기능을 사용할 수 없습니다.

## <a name="dependencies-dont-show-after-installing-agents"></a>에이전트 설치 후 종속성이 표시 되지 않음

온-프레미스 Vm에서 종속성 시각화 에이전트를 설치 하 고 나면 Azure Migrate는 일반적으로 포털에서 종속성을 표시 하는 데 15-30 분이 소요 됩니다. 30 분 넘게 대기한 경우 Microsoft Monitoring Agent (MMA)가 Log Analytics 작업 영역에 연결할 수 있는지 확인 합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작 합니다.
2. **Microsoft Monitoring Agent 속성** > **AZURE Log Analytics (OMS)** 에서 작업 영역에 대 한 **상태가** 녹색 인지 확인 합니다.
3. 상태가 녹색이 아닌 경우 작업 영역을 제거 하 고 다시 MMA에 추가 하십시오.

      ![MMA 속성 대화 상자](./media/troubleshooting-general/mma-status.png)

Linux Vm의 경우 MMA 및 종속성 에이전트의 설치 명령이 성공 했는지 확인 합니다.

## <a name="supported-mma-os"></a>지원 되는 MMA OS

 지원 되는 [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)및 [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) 운영 체제를 검토 합니다.

## <a name="supported-dependency-agent-os"></a>지원 되는 종속성 에이전트 OS

지원 되는 [Windows 및 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 운영 체제를 검토 합니다.

## <a name="dependencies-for-more-than-an-hour"></a>1 시간 이상 종속성

Azure Migrate는 지난 달의 특정 날짜로 다시 이동할 수 있지만, 종속성을 시각화할 수 있는 최대 기간은 1 시간입니다.

예를 들어, 종속성 맵의 기간 기능을 사용 하 여 어제 종속성을 볼 수 있지만 1 시간 동안만 볼 수 있습니다.

그러나 Azure Monitor 로그를 사용하면 더 긴 기간 동안의 [종속성 데이터를 쿼리](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)할 수 있습니다.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>10 개 이상의 Vm이 있는 그룹에 대 한 종속성을 시각화할 수 없습니다.

Azure Migrate Server 평가에서는 최대 10 개의 Vm이 있는 [그룹에 대 한 종속성을 시각화할](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) 수 있습니다. 규모가 큰 그룹의 경우 종속성을 시각화 하기 위해 Vm을 더 작은 그룹으로 분할 하는 것이 좋습니다.

## <a name="machines-show-install-agent-not-view-dependencies"></a>컴퓨터에 "에이전트 설치"가 "종속성 보기"가 표시 되지 않음

종속성 시각화를 사용 하 여 Azure에 컴퓨터를 마이그레이션한 후에는 다음과 같은 동작으로 인해 컴퓨터에서 "종속성 보기" 대신 "에이전트 설치" 작업을 표시할 수 있습니다.


- Azure로 마이그레이션한 후 온-프레미스 컴퓨터는 꺼지고 동일한 Vm이 Azure에서 분리 됩니다. 이러한 컴퓨터는 다른 MAC 주소를 갖습니다.
- 컴퓨터에는 온-프레미스 IP 주소를 보유 했는지 여부에 따라 다른 IP 주소를 사용할 수도 있습니다.
- MAC 주소와 IP 주소가 온-프레미스와 다른 경우 Azure Migrate 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않습니다. 이 경우 종속성을 보는 대신 에이전트를 설치 하는 옵션이 표시 됩니다.
- Azure로 테스트를 마이그레이션한 후 온-프레미스 컴퓨터는 정상적으로 켜 집니다. Azure에서 분리 하는 동일한 컴퓨터는 다른 MAC 주소를 획득 하 고 다른 IP 주소를 획득할 수 있습니다. 이러한 컴퓨터에서 나가는 Azure Monitor 로그 트래픽을 차단 하지 않는 한 Azure Migrate 온-프레미스 컴퓨터를 서비스 맵 종속성 데이터와 연결 하지 않으므로 종속성을 볼 수 있는 대신 에이전트를 설치 하는 옵션이 표시 됩니다.


## <a name="collect-network-traffic-logs-in-portal"></a>포털에서 네트워크 트래픽 로그 수집

다음과 같이 로그를 수집 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작 합니다. 필요한 경우 **탐색에서 항목 지우기** 설정을 선택 취소 합니다.
3. **네트워크** 탭을 선택 하 고 네트워크 트래픽 캡처를 시작 합니다.
   - 크롬에서 **Preserve log**를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽이 캡처되고 있음을 나타냅니다. 빨간색 원이 나타나지 않으면 시작할 검정색 원을 선택 합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작 됩니다. 그렇지 않으면 녹색 재생 단추를 선택 합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서 마우스 오른쪽 단추를 클릭 하 고 **콘텐츠를 사용 하 여 HAR로 저장**을 선택 합니다. 이 작업은 로그를 압축 하 고 har 파일로 내보냅니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택 합니다. 이 작업은 로그를 압축 하 고 내보냅니다.
6. **콘솔** 탭을 선택 하 여 경고 또는 오류를 확인 합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. 다른 **이름으로 저장**을 선택 하 고 로그를 내보내고 압축 합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭 하 고 **모두 복사**를 선택 합니다.
7. 개발자 도구를 닫습니다.
