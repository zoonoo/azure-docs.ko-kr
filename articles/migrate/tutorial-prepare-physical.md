---
title: Azure Migrate를 사용하여 평가/마이그레이션을 위한 물리적 서버 준비
description: Azure Migrate를 사용하여 물리적 서버의 평가/마이그레이션을 준비하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: ed648458416bacb091212bb569a27ecdf13fe8ee
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771277"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>물리적 서버의 평가 및 Azure로 마이그레이션 준비

이 문서에서는 [Azure Migrate](migrate-services-overview.md)를 사용하여 온-프레미스 물리적 서버의 평가를 준비하는 방법에 대해 설명합니다.

[Azure Migrate](migrate-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 

이 자습서는 Azure Migrate를 사용하여 물리적 서버를 평가하는 방법을 보여 주는 시리즈의 첫 번째 자습서입니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure를 준비합니다. Azure Migrate에서 사용할 Azure 계정 및 리소스에 대한 권한을 설정합니다.
> * 서버 평가를 위해 온-프레미스 물리적 서버를 준비합니다.


> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 물리적 서버 평가 방법을 검토하세요.


Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prepare-azure-for-server-assessment"></a>서버 평가를 위한 Azure 준비

Azure Migrate를 사용할 수 있도록 Azure를 설정합니다. 

**Task** | **세부 정보** 
--- | --- 
**Azure Migrate 프로젝트 만들기** | Azure 계정에는 프로젝트를 만들 수 있는 기여자 또는 소유자 권한이 있어야 합니다. 
**리소스 공급자 등록(평가 전용)** | Azure Migrate는 경량 Azure Migrate 어플라이언스를 사용하여 Azure Migrate:Server Assessment를 통해 머신을 검색하고 평가합니다.<br/><br/> 어플라이언스 등록 중에 리소스 공급자는 어플라이언스에서 선택한 구독에 등록됩니다. [자세히 알아보기](migrate-appliance-architecture.md#appliance-registration).<br/><br/> 리소스 공급자를 등록하려면 구독에 대한 기여자 또는 소유자 역할이 필요합니다.
**Azure AD 앱 만들기(평가 전용)** | 어플라이언스를 등록할 때 Azure Migrate는 어플라이언스에서 실행되는 에이전트와 Azure에서 실행되는 각 서비스 간의 통신에 사용되는 Azure AD(Azure Active Directory) 앱을 만듭니다. [자세히 알아보기](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Azure AD 앱(애플리케이션 개발자에서 사용 가능) 역할을 만들 수 있는 권한이 필요합니다.


### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당 

Azure Migrate 프로젝트를 만들 수 있는 권한이 있는지 확인합니다.

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.


### <a name="assign-permissions-to-register-the-appliance"></a>어플라이언스를 등록할 수 있는 권한 할당 

다음 방법 중 하나를 사용하여 어플라이언스 등록 중에 Azure AD 앱을 만들 수 있는 Azure Migrate에 대한 사용 권한을 할당할 수 있습니다.

- 테넌트/글로벌 관리자는 Azure AD 앱을 만들고 등록할 수 있는 권한을 테넌트의 사용자에게 부여할 수 있습니다.
- 테넌트/글로벌 관리자는 권한이 있는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다.

> [!NOTE]
> - 앱에는 위에서 설명한 구독에 대한 액세스 권한 이외의 다른 권한이 없습니다.
> - 이러한 권한은 새 어플라이언스를 등록할 때만 필요합니다. 어플라이언스가 설정되면 해당 권한을 제거할 수 있습니다.


#### <a name="grant-account-permissions"></a>계정 권한 부여

테넌트/글로벌 관리자는 다음과 같이 권한을 부여할 수 있습니다.

1. Azure AD에서 테넌트/글로벌 관리자는 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동해야 합니다.
2. 관리자는 **앱 등록**을 **예**로 설정해야 합니다.

    ![Azure AD 권한](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>애플리케이션 개발자 역할 할당

테넌트/글로벌 관리자는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>물리적 서버 마이그레이션을 위한 Azure 준비

Server Migration을 사용하여 물리적 서버를 마이그레이션할 Azure를 준비합니다.

**Task** | **세부 정보**
--- | ---
**Azure Migrate 프로젝트 만들기** | Azure 계정에는 프로젝트를 만들 수 있는 기여자 또는 소유자 권한이 있어야 합니다.
**Azure 계정에 대한 사용 권한 확인** | Azure 계정에는 VM을 만들고 Azure 관리 디스크에 쓸 수 있는 권한이 필요합니다.
**Azure 네트워크 만들기** | Azure의 네트워크를 설정합니다.


### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 권한 확인**에서 관련 계정을 찾아 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.


### <a name="assign-azure-account-permissions"></a>Azure 계정 권한 할당

Azure 계정에 Virtual Machine 기여자 역할을 할당합니다. 다음 항목에 대한 사용 권한을 제공합니다.
  - 선택한 리소스 그룹에 VM 만들기
  - 선택한 가상 네트워크에 VM 만들기
  - Azure 관리 디스크에 씁니다. 

### <a name="create-an-azure-network"></a>Azure 네트워크 만들기

Azure VNet(Virtual Network)을 [설정](../virtual-network/manage-virtual-network.md#create-a-virtual-network)합니다. Azure에 복제하면 Azure VM이 만들어지고 마이그레이션을 설정할 때 지정한 Azure VNet에 조인됩니다.


## <a name="prepare-for-physical-server-assessment"></a>물리적 서버 평가 준비

물리적 서버 평가를 준비하려면 물리적 서버 설정과 어플라이언스 배포에 대한 설정을 확인해야 합니다.

### <a name="verify-physical-server-settings"></a>물리적 서버 설정 확인

1. 서버 평가에 대한 [물리적 서버 요구 사항](migrate-support-matrix-physical.md#physical-server-requirements)을 확인합니다.
2. 물리적 서버에서 [필요한 포트](migrate-support-matrix-physical.md#port-access)가 열려 있는지 확인합니다.


### <a name="verify-appliance-settings"></a>어플라이언스 설정 확인

Azure Migrate 어플라이언스를 설정하고 다음 자습서에서 평가를 시작하기 전에 어플라이언스 배포를 준비합니다.

1. 물리적 서버의 어플라이언스 요구 사항을 [확인](migrate-appliance.md#appliance---physical)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다.
3. 검색 및 평가 중에 어플라이언스가 수집 업무를 수행하는지 [검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 포트 액세스 요구 사항 물리적 서버 평가를 [참조](migrate-support-matrix-physical.md#port-access)하세요.


### <a name="set-up-an-account-for-physical-server-discovery"></a>물리적 서버 검색을 위한 계정 설정

Azure Migrate에는 온-프레미스 서버를 검색할 수 있는 권한이 필요합니다.

- **Windows:** 검색하려는 모든 Windows 서버의 도메인 관리자 또는 로컬 관리자여야 합니다. 사용자 계정은 다음 그룹에 추가되어야 합니다. 원격 관리 사용자, 성능 모니터 사용자 및 성능 로그 사용자.
- **Linux:** 검색하려는 Linux 서버의 루트 계정이 필요합니다.

## <a name="prepare-for-physical-server-migration"></a>물리적 서버 마이그레이션 준비

물리적 서버의 마이그레이션에 대한 요구 사항을 검토합니다.

> [!NOTE]
> 물리적 서버를 마이그레이션할 때 Azure Migrate:Server Migration은 Azure Site Recovery 서비스의 에이전트 기반 재해 복구와 동일한 복제 아키텍처를 사용하며, 일부 구성 요소는 동일한 코드 베이스를 공유합니다. 일부 콘텐츠는 Site Recovery 설명서에 연결될 수 있습니다.

1. 마이그레이션을 위한 물리적 서버 요구 사항을 [검토](migrate-support-matrix-physical-migration.md#physical-server-requirements)합니다.
2. Azure Migrate:Server Migration은 물리적 서버 마이그레이션에 복제 서버를 사용합니다.
    - 복제 어플라이언스에 대한 배포 요구 사항 및 어플라이언스에 MySQL을 설치하기 위한 [옵션](migrate-replication-appliance.md#mysql-installation)을 [검토](migrate-replication-appliance.md#appliance-requirements)합니다.
    - 복제 어플라이언스가 퍼블릭 및 정부 클라우드에 액세스하는 데 필요한 [Azure URL](migrate-appliance.md#url-access)을 검토합니다.
    - 복제 어플라이언스에 대한 [포트](migrate-replication-appliance.md#port-access) 액세스 요구 사항을 검토합니다.
3. VM을 Azure로 마이그레이션하기 전에 VM에서 몇 가지 사항을 변경해야 합니다.
    - 이러한 변경은 마이그레이션을 시작하기 전에 수행해야 합니다. 변경하기 전에 VM을 마이그레이션하면 Azure에서 해당 VM이 부팅되지 않을 수 있습니다.
    - 필요한 [Windows](prepare-for-migration.md#windows-machines) 및 [Linux](prepare-for-migration.md#linux-machines) 변경 사항을 검토해야 합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure 계정 권한을 설정합니다.
> * 물리적 서버 평가를 준비합니다.

Azure Migrate 프로젝트를 만들고 Azure로 마이그레이션을 위해 물리적 서버를 평가하려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [물리적 서버 평가](./tutorial-assess-physical.md)
