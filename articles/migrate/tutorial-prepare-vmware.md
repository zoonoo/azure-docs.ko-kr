---
title: Azure Migrate를 사용하여 평가/마이그레이션을 위해 VMware VM 준비
description: Azure Migrate를 사용하여 VMware VM의 평가/마이그레이션을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677294"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>평가 후 Azure로 마이그레이션할 VMware VM 준비

이 문서는 [Azure Migrate](migrate-services-overview.md)를 사용하여 온-프레미스 VMware VM을 평가하고 Azure로 마이그레이션하기 위해 준비하는 데 유용합니다.



이 자습서는 VMware VM을 평가하고 마이그레이션하는 방법을 보여 주는 시리즈의 두 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate를 사용할 수 있게 Azure를 준비합니다.
> * Azure Migrate:Server Assessment 도구를 사용하여 VM을 평가하기 위한 VMware를 준비합니다.
> * Azure Migrate:Server Migration 도구를 사용하여 VM을 마이그레이션하기 위한 VMware를 준비합니다. 

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 배포를 설정하는 방법을 배우고 빠르게 개념을 증명하는 데 유용합니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prepare-azure"></a>Azure 준비

VMware VM을 평가하거나 마이그레이션하기 전에 Azure에서 이러한 작업을 수행하는 데 필요한 권한은 다음과 같습니다.

**Task** | **세부 정보** 
--- | --- 
**Azure Migrate 프로젝트 만들기** | Azure 계정에는 프로젝트를 만들 수 있는 기여자 또는 소유자 권한이 있어야 합니다. 
**리소스 공급자 등록** | Azure Migrate는 경량 Azure Migrate 어플라이언스를 사용하여 VMware VM을 검색 및 평가하고 Azure Migrate:Server Assessment를 사용하여 Azure로 마이그레이션합니다.<br/><br/> 어플라이언스 등록 중에 리소스 공급자는 어플라이언스에서 선택한 구독에 등록됩니다. [자세히 알아보기](migrate-appliance-architecture.md#appliance-registration).<br/><br/> 리소스 공급자를 등록하려면 구독에 대한 기여자 또는 소유자 역할이 필요합니다.
**Azure AD 앱 만들기** | 어플라이언스를 등록하면 Azure Migrate는 Azure AD(Azure Active Directory) 앱을 만듭니다. <br/><br/> - 첫 번째 앱은 어플라이언스에서 실행되는 에이전트 간의 통신과 Azure에서 실행되는 각각의 서비스에 사용됩니다.<br/><br/> - 두 번째 앱은 에이전트 없는 VMware VM 마이그레이션을 위해 사용자의 구독에서 생성된 KeyVault에 액세스하는 데만 사용됩니다. [자세히 알아보기](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Azure AD 앱(애플리케이션 개발자에서 사용 가능) 역할을 만들 수 있는 권한이 필요합니다.
**Key Vault 만들기** | 에이전트 없는 마이그레이션을 사용하여 VMware VM을 마이그레이션하기 위해 Azure Migrate에서 구독의 복제 스토리지 계정에 대한 액세스 키를 관리하는 Key Vault를 만듭니다.<br/><br/> 자격 증명 모음을 만들려면 Azure Migrate 프로젝트가 있는 리소스 그룹에 대한 역할 할당 권한이 필요합니다.




### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.

### <a name="assign-permissions-to-register-the-appliance"></a>어플라이언스를 등록할 수 있는 권한 할당

어플라이언스를 등록하려면 어플라이언스 등록 중에 Azure AD 앱을 만들기 위해 Azure Migrate에 대한 권한을 할당합니다. 다음 방법 중 하나를 사용하여 권한을 할당할 수 있습니다.

- **권한 부여**: 테넌트/글로벌 관리자는 Azure AD 앱을 만들고 등록할 수 있는 권한을 테넌트의 사용자에게 부여할 수 있습니다.
- **애플리케이션 개발자 역할 할당**: 테넌트/글로벌 관리자는 권한이 있는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다.

> [!NOTE]
> - 앱에는 위에서 설명한 구독에 대한 액세스 권한 이외의 다른 권한이 없습니다.
> - 이러한 권한은 새 어플라이언스를 등록할 때만 필요합니다. 어플라이언스가 설정되면 해당 권한을 제거할 수 있습니다.


#### <a name="grant-account-permissions"></a>계정 권한 부여

테넌트/글로벌 관리자가 권한을 부여하도록 하려면 다음을 수행합니다.

1. Azure AD에서 테넌트/글로벌 관리자는 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동해야 합니다.
2. 관리자는 **앱 등록**을 **예**로 설정해야 합니다. 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD 권한](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>애플리케이션 개발자 역할 할당

또는 테넌트/글로벌 관리자가 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다. 역할 할당에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-permissions-to-create-a-key-vault"></a>Key Vault를 만들 수 있는 권한 할당

Azure Migrate에서 Key Vault를 만들도록 설정하려면 다음과 같이 권한을 할당합니다.

1. Azure Portal의 리소스 그룹에서 **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.

    - 서버 평가를 실행하는 데는 **기여자** 권한으로 충분합니다.
    - 에이전트 없는 서버 마이그레이션을 실행하려면 **소유자**(또는 **기여자** 및 **사용자 액세스 관리자**) 권한이 있어야 합니다.

3. 필요한 권한이 없으면 리소스 그룹 소유자에게 요청합니다.



## <a name="prepare-for-vmware-vm-assessment"></a>VMware VM 평가 준비

VMware VM 평가를 준비하려면 다음을 수행해야 합니다.

- **VMware 설정 확인**. 마이그레이션하려는 vCenter Server 및 VM이 요구 사항을 충족하는지 확인합니다.
- **평가를 위한 계정 설정**. Azure Migrate는 이 계정을 사용하여 vCenter Server에 액세스하여 평가할 VM을 검색합니다.
- **어플라이언스 요구 사항 확인**. Azure Migrate 어플라이언스를 배포하기 전에 배포 요구 사항을 확인합니다.

### <a name="verify-vmware-settings"></a>VMware 설정 확인

1. 평가에 필요한 VMware 서버 요구 사항을 [확인](migrate-support-matrix-vmware.md#vmware-requirements)합니다.
2. 필요한 포트가 vCenter Server에서 열려 있는지 [확인](migrate-support-matrix-vmware.md#port-access)합니다.
3. vCenter Server에서 계정에 OVA 파일을 사용하여 VM을 만들 수 있는 권한이 있는지 확인합니다. OVA 파일을 사용하여 Azure Migrate 어플라이언스를 VMware VM으로 배포합니다.


### <a name="set-up-an-account-for-assessment"></a>평가를 위한 계정 설정

평가 및 에이전트 없는 마이그레이션을 위해 VM을 검색하려면 Azure Migrate에서 vCenter Server에 액세스해야 합니다.

- 애플리케이션을 검색하거나 에이전트 없는 방식으로 종속성을 시각화하려는 경우 **가상 머신** > **게스트 작업**에 대해 사용하도록 설정된 권한과 함께 읽기 전용 액세스 권한이 있는 vCenter Server 계정을 만듭니다.

  ![vCenter Server 계정 권한](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- 애플리케이션 검색 및 에이전트 없는 종속성 시각화를 수행하지 않으려는 경우 vCenter Server에 대한 읽기 전용 계정을 설정합니다.

### <a name="verify-appliance-settings-for-assessment"></a>평가를 위한 어플라이언스 설정 확인

Azure Migrate 어플라이언스를 설정하고 다음 자습서에서 평가를 시작하기 전에 어플라이언스 배포를 준비합니다.

1. Azure Migrate 어플라이언스 요구 사항을 [확인](migrate-appliance.md#appliance---vmware)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access)하세요.




## <a name="prepare-for-agentless-vmware-migration"></a>에이전트 없는 VMware 마이그레이션 준비

VMware VM의 [에이전트 없는 마이그레이션](server-migrate-overview.md)에 대한 요구 사항을 검토합니다.

1. VMware 서버 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)합니다.
2. Azure Migrate에서 vCenter Server에 액세스하는 데 필요한 [권한을 검토](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)합니다.
3. VMware VM 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)합니다.
4. Azure Migrate 어플라이언스 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance)합니다.
5. [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에 필요한 URL 액세스를 확인합니다.
6. [포트 액세스 ](migrate-support-matrix-vmware-migration.md#agentless-ports) 요구 사항을 검토합니다.

## <a name="prepare-for-agent-based-vmware-migration"></a>에이전트 기반 VMware 마이그레이션 준비

VMware VM의 [에이전트 기반 마이그레이션](server-migrate-overview.md)에 대한 요구 사항을 검토합니다.

1. VMware 서버 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)합니다.
2. Azure Migrate에서 vCenter Server에 액세스하는 데 필요한 [권한을 검토](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)합니다.
2. 마이그레이션하려는 각 VM에 대한 모바일 서비스 설치를 포함하여 VMware VM 요구 사항을 [검토](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms)합니다.
3. 에이전트 기반 마이그레이션은 복제 어플라이언스를 사용합니다.
    - 복제 어플라이언스 배포 요구 사항을 [검토](migrate-replication-appliance.md#appliance-requirements)합니다.
    - MySQL을 어플라이언스에 설치하는 [옵션을 검토](migrate-replication-appliance.md#mysql-installation)합니다.
    - [퍼블릭](migrate-replication-appliance.md#url-access) 및 [정부](migrate-replication-appliance.md#azure-government-url-access) 클라우드에 필요한 URL 액세스를 확인합니다.
    - 복제 어플라이언스에 대한 [포트 액세스](migrate-replication-appliance.md#port-access) 요구 사항을 검토합니다.
    
## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure 사용 권한 설정
> * VMware 평가 및 마이그레이션 준비


Azure Migrate 프로젝트를 설정하고 Azure로 마이그레이션하기 위해 VMware VM을 평가하려면 두 번째 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VMware VM 평가](./tutorial-assess-vmware.md)
