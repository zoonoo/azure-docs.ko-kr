---
title: Azure Migrate 서버 평가를 사용하여 물리적 서버 평가 준비
description: Azure Migrate 서버 평가를 사용하여 물리적 서버의 평가 및 Azure로 마이그레이션을 준비하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bf1b9859bf3dc4b8ac14d01b2bb6c8cf9c9899ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520862"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>물리적 서버의 평가 및 Azure로 마이그레이션 준비

이 문서에서는 [Azure Migrate](migrate-services-overview.md)를 사용하여 온-프레미스 물리적 서버의 평가를 준비하는 방법에 대해 설명합니다.

[Azure Migrate](migrate-overview.md)는 앱, 인프라 및 워크로드를 검색, 평가 및 Microsoft Azure로 마이그레이션하는 데 도움이 되는 도구의 허브를 제공합니다. 허브에는 Azure Migrate 도구와 타사 ISV(독립 소프트웨어 공급업체) 제품이 포함되어 있습니다. 

이 자습서는 Azure Migrate를 사용하여 물리적 서버를 평가하는 방법을 보여 주는 시리즈의 첫 번째 자습서입니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure를 준비합니다. Azure Migrate에서 사용할 Azure 계정 및 리소스에 대한 권한을 설정합니다.
> * 서버 평가를 위해 온-프레미스 물리적 서버를 준비합니다.


> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다. 자세한 지침은 물리적 서버 평가 방법을 검토하세요.


Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prepare-azure"></a>Azure 준비

### <a name="azure-permissions"></a>Azure 권한

Azure Migrate 배포를 위한 권한을 설정해야 합니다.

- Azure 계정에서 Azure Migrate 프로젝트를 만들 수 있는 권한 
- 계정에서 Azure Migrate 어플라이언스를 등록할 수 있는 권한. 어플라이언스는 Hyper-V 검색 및 마이그레이션에 사용됩니다. 어플라이언스를 등록하는 동안 Azure Migrate는 어플라이언스를 고유하게 식별하는 두 개의 Azure AD(Azure Active Directory) 앱을 만듭니다.
    - 첫 번째 앱은 Azure Migrate 서비스 엔드포인트와 통신합니다.
    - 두 번째 앱은 등록 중에 만든 Azure Key Vault에 액세스하여 Azure AD 앱 정보 및 어플라이언스 구성 설정을 저장합니다.



### <a name="assign-permissions-to-create-project"></a>프로젝트를 만들 수 있는 권한 할당

Azure Migrate 프로젝트를 만들 수 있는 권한이 있는지 확인합니다.

1. Azure Portal에서 구독을 열고, **액세스 제어(IAM)** 를 선택합니다.
2. **액세스 확인**에서 관련 계정을 찾아서 클릭하여 권한을 확인합니다.
3. **기여자** 또는 **소유자** 권한이 있어야 합니다.
    - Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다.
    - 구독 소유자가 아닌 경우 해당 역할을 할당해 주도록 소유자에게 문의합니다.


### <a name="assign-permissions-to-register-the-appliance"></a>어플라이언스를 등록할 수 있는 권한 할당

다음 방법 중 하나를 사용하여 Azure Migrate에서 어플라이언스 등록 중에 Azure AD 애플리케이션을 만들 수 있는 권한을 할당할 수 있습니다.

- 테넌트/글로벌 관리자는 Azure AD 앱을 만들고 등록할 수 있는 권한을 테넌트의 사용자에게 부여할 수 있습니다.
- 테넌트/글로벌 관리자는 권한이 있는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다.

주의해야 할 사항은 다음과 같습니다.

- 앱에는 위에서 설명한 구독에 대한 액세스 권한 이외의 다른 권한이 없습니다.
- 이러한 권한은 새 어플라이언스를 등록할 때만 필요합니다. 어플라이언스가 설정되면 해당 권한을 제거할 수 있습니다. 


#### <a name="grant-account-permissions"></a>계정 권한 부여

테넌트/글로벌 관리자는 다음과 같이 권한을 부여할 수 있습니다.

1. Azure AD에서 테넌트/글로벌 관리자는 **Azure Active Directory** > **사용자** > **사용자 설정**으로 이동해야 합니다.
2. 관리자는 **앱 등록**을 **예**로 설정해야 합니다.

    ![Azure AD 권한](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 이것이 중요한 내용이 포함되지 않는 기본 설정입니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>애플리케이션 개발자 역할 할당 

테넌트/글로벌 관리자는 애플리케이션 개발자 역할을 계정에 할당할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>물리적 서버 평가 준비

물리적 서버 평가를 준비하려면 물리적 서버 설정과 어플라이언스 배포에 대한 설정을 확인해야 합니다.

### <a name="verify-physical-server-settings"></a>물리적 서버 설정 확인

1. 서버 평가에 대한 [물리적 서버 요구 사항](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements)을 확인합니다.
2. 물리적 서버에서 [필요한 포트](migrate-support-matrix-hyper-v.md#assessment-port-requirements)가 열려 있는지 확인합니다.


### <a name="verify-appliance-settings"></a>어플라이언스 설정 확인

Azure Migrate 어플라이언스를 설정하고 다음 자습서에서 평가를 시작하기 전에 어플라이언스 배포를 준비합니다.

1. 어플라이언스 요구 사항을 [확인](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)합니다.
2. 어플라이언스에서 액세스해야 하는 Azure URL을 [검토](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)합니다.
3. 검색 및 평가 중에 어플라이언스가 수집할 데이터를 검토합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-hyper-v.md#assessment-port-requirements)하세요.


### <a name="set-up-an-account-for-physical-server-discovery"></a>물리적 서버 검색을 위한 계정 설정

Azure Migrate에는 온-프레미스 서버를 검색할 수 있는 권한이 필요합니다.

- **Windows:** 검색에 포함하려는 모든 Windows 서버에 로컬 사용자 계정을 설정합니다. 다음 그룹에 사용자 계정을 추가해야 합니다.       - Remote Desktop Users       - Performance Monitor Users       - Performance Log users
- **Linux:** 검색하려는 Linux 서버의 루트 계정이 필요합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.
 
> [!div class="checklist"] 
> * Azure 계정 권한을 설정합니다.
> * 물리적 서버 평가를 준비합니다.

Azure Migrate 프로젝트를 만들고 Azure로 마이그레이션을 위해 물리적 서버를 평가하려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"] 
> [물리적 서버 평가](./tutorial-assess-physical.md) 