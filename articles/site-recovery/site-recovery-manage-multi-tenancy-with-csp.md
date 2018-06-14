---
title: CSP(클라우드 솔루션 공급자) 프로그램을 사용하여 Azure Site Recovery에서 다중 테넌트 관리 | Microsoft Docs
description: CSP를 통해 테넌트 구독을 만들고 관리하며 다중 테넌트 설정에서 Azure Site Recovery를 배포하는 방법을 설명합니다
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 6dc8b573e66eaae1b5cb923ae72333fb959d0969
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767638"
---
# <a name="manage-multi-tenancy-with-the-cloud-solution-provider-csp-program"></a>CSP(클라우드 솔루션 공급자) 프로그램을 사용하여 다중 테넌트 관리

[CSP 프로그램](https://partner.microsoft.com/en-US/cloud-solution-provider)은 Office 365, Enterprise Mobility Suite 및 Microsoft Azure 등 모든 Microsoft 클라우드 서비스에 대한 더 나은 협력 사례를 발전시키고 있습니다. CSP를 통해 파트너는 고객과 종단 간 관계를 보유하게 되고 기본 관계 접점이 될 수 있습니다. 파트너는 고객을 위해 Azure 구독을 배포하고 이러한 구독과 고객의 부가 가치 사용자 지정 옵션을 결합할 수 있습니다.

파트너는 Azure Site Recovery와 함께 CSP를 통해 고객을 위한 완전한 재해 복구 솔루션을 직접 관리할 수 있습니다. 또는 CSP를 사용하여 Site Recovery 환경을 설정할 수 있으며 고객은 셀프 서비스 방식으로 고유한 재해 복구 요구를 관리할 수 있습니다. 두 시나리오 모두에서 파트너는 Site Recovery와 고객 간의 매개체입니다. 파트너가 고객 관계 서비스를 제공하고 Site Recovery 사용에 대해 고객에게 비용을 청구합니다.

이 문서에서는 파트너가 다중 테넌트 VMware 설정에서 CSP를 통해 테넌트 구독을 만들고 관리하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독, Azure Virtual Network 및 저장소 계정을 포함하는 Azure 리소스를 [준비](tutorial-prepare-azure.md)합니다.
- VMware 온-프레미스 VMware 서버 및 VM을 [준비](tutorial-prepare-on-premises-vmware.md)합니다.
- 각 테넌트에 대해, 테넌트 VM 및 테넌트의 vCenter와 통신할 수 있는 별도의 관리 서버를 만듭니다. 파트너만이 이 서버에 대한 액세스 권한이 있습니다. 다양한 다중 테넌트 환경에 대한 자세한 내용은 [다중 테넌트 VMware](site-recovery-multi-tenant-support-vmware-using-csp.md) 지침을 참조하세요.

## <a name="create-a-tenant-account"></a>테넌트 계정 만들기

1. [Microsoft 파트너 센터](https://partnercenter.microsoft.com/)를 통해 CSP 계정에 로그인합니다.

2. **대시보드** 메뉴에서 **고객**을 선택합니다.

    ![Microsoft 파트너 센터 고객 링크](./media/site-recovery-manage-multi-tenancy-with-csp/csp-dashboard-display.png)

3. 열린 페이지에서 **고객 추가** 단추를 클릭합니다.

    ![[고객 추가] 단추](./media/site-recovery-manage-multi-tenancy-with-csp/add-new-customer.png)

4. **새 고객** 페이지에서 테넌트에 대한 계정 세부 정보를 모두 입력하고 **다음: 구독**을 클릭합니다.

    ![[계정 정보] 페이지](./media/site-recovery-manage-multi-tenancy-with-csp/customer-add-filled.png)

5. 구독 선택 페이지에서 **Microsoft Azure** 확인란을 선택합니다. 지금 또는 나중에 다른 구독을 추가할 수 있습니다.

    ![[Microsoft Azure 구독] 확인란](./media/site-recovery-manage-multi-tenancy-with-csp/azure-subscription-selection.png)

6. **검토** 페이지에서 테넌트 세부 정보를 확인한 후 **제출**을 클릭합니다.

    ![[검토] 페이지](./media/site-recovery-manage-multi-tenancy-with-csp/customer-summary-page.png)  

    테넌트 계정을 만들면 확인 페이지가 나타나며 여기에 구독에 대한 기본 계정 및 암호 정보가 표시됩니다.

7. 정보를 저장하고 나중에 필요하면 Azure Portal 로그인 페이지를 통해 암호를 변경합니다.  

    이 정보를 테넌트와 있는 그대로 공유하거나 필요할 경우 별도의 계정을 만들고 공유할 수 있습니다.

## <a name="access-the-tenant-account"></a>테넌트 계정 액세스

“1단계: 테넌트 계정 만들기”에 설명된 대로 Microsoft 파트너 센터 대시보드를 통해 테넌트 구독에 액세스할 수 있습니다.

1. **고객** 페이지로 이동한 후 테넌트 계정의 이름을 클릭합니다.

2. 테넌트 계정의 **구독** 페이지에서 기존 계정 구독을 모니터링하고 필요에 따라 구독을 더 추가할 수 있습니다. 테넌트의 재해 복구 작업을 관리하려면 **모든 리소스(Azure Portal)** 를 선택합니다.

    ![모든 리소스 링크](./media/site-recovery-manage-multi-tenancy-with-csp/all-resources-select.png)  

    **모든 리소스**를 클릭하면 테넌트의 Azure 구독에 대한 액세스 권한이 부여됩니다. Azure Portal의 맨 위 오른쪽에 있는 Azure Active Directory 링크를 클릭하여 액세스를 확인할 수 있습니다.

    ![Azure Active Directory 링크](./media/site-recovery-manage-multi-tenancy-with-csp/aad-admin-display.png)

이제 Azure Portal을 통해 테넌트에 대한 모든 사이트 복구 작업을 수행하고 재해 복구 작업을 관리할 수 있습니다. 관리 재해 복구를 위해 CSP를 통해 테넌트 구독에 액세스하려면 이전에 설명된 과정을 따릅니다.

## <a name="deploy-resources-to-the-tenant-subscription"></a>리소스를 테넌트 구독에 배포
1. Azure Portal에서 리소스 그룹을 만들고 일반 프로세스에 대한 Recovery Services 자격 증명 모음을 배포합니다.

2. 자격 증명 모음 등록 키를 다운로드합니다.

3. 자격 증명 모음 등록 키를 사용하여 테넌트용 CS를 등록합니다.

4. 두 가지 액세스 계정 즉, vCenter 액세스 계정과 VM 액세스 계정에 대한 자격 증명을 입력합니다.

    ![관리자 구성 서버 계정](./media/site-recovery-manage-multi-tenancy-with-csp/config-server-account-display.png)

## <a name="register-site-recovery-infrastructure-to-the-recovery-services-vault"></a>Site Recovery 인프라를 Recovery Services 자격 증명 모음에 등록
1. Azure Portal의 앞에서 만든 자격 증명 모음에서 vCenter 서버를 “3단계: 리소스를 테넌트 구독에 배포”에 등록한 CS에 등록합니다. 이를 위해 vCenter 액세스 계정을 사용합니다.
2. 일반 프로세스에 대한 Site Recovery를 위해 “준비 인프라” 프로세스를 완료합니다.
3. 이제 VM을 복제할 준비가 되었습니다. **복제** 옵션 아래에 있는 **가상 머신 선택** 블레이드에 테넌트의 VM만 표시되는지 확인합니다.

    ![가상 머신 선택 블레이드의 테넌트 VM 목록](./media/site-recovery-manage-multi-tenancy-with-csp/tenant-vm-display.png)

## <a name="assign-tenant-access-to-the-subscription"></a>구독에 테넌트 액세스 할당

셀프 서비스 재해 복구의 경우 “1단계: 테넌트 계정 만들기” 섹션의 6단계에서 설명한 것처럼 테넌트에 계정 정보를 제공합니다. 파트너가 재해 복구 인프라를 설정한 후 이 작업을 수행합니다. 재해 복구 형식이 관리형 또는 셀프 서비스 형태인지 관계없이 파트너는 CSP 포털을 통해 테넌트 구독을 액세스해야 합니다. 파트너 소유 자격 증명 모음을 설정하고 테넌트 구독에 인프라를 등록합니다.

파트너는 다음을 수행하여 CSP 포털을 통해 새 사용자를 테넌트 구독에 추가할 수도 있습니다.

1. 테넌트의 CSP 구독 페이지로 이동하고 **사용자 및 라이선스** 옵션을 선택합니다.

    ![테넌트의 CSP 구독 페이지](./media/site-recovery-manage-multi-tenancy-with-csp/users-and-licences.png)

    이제 관련 세부 정보를 입력하거나 권한을 선택하거나 사용자 목록을 CSV 파일로 업로드하여 새 사용자를 만들 수 있습니다.

2. 새 사용자를 만든 후 Azure Portal로 돌아가 **구독** 블레이드에서 관련 구독을 선택합니다.

3. 열리는 새 블레이드에서 **Access Control(IAM)** 을 선택하고 **추가**를 클릭하여 관련 액세스 수준을 가진 사용자를 추가합니다.      
    CSP 포털을 통해 만들어진 사용자는 액세스 수준을 클릭하면 열리는 블레이드에 자동으로 표시됩니다.

    ![사용자 추가](./media/site-recovery-manage-multi-tenancy-with-csp/add-user-subscription.png)

    대부분의 관리 작업은 *참여자* 역할로 충분합니다. 이 액세스 수준을 가진 사용자는 액세스 수준 변경(*소유자* 수준 액세스가 필요)을 제외하고 구독에 관한 모든 작업을 수행할 수 있습니다.

  또한 Azure Site Recovery에는 필요에 따라 액세스 수준을 더 제한하는 데 사용할 수 있는 3개의 [미리 정의된 사용자 역할](site-recovery-role-based-linked-access-control.md)이 있습니다.

## <a name="next-steps"></a>다음 단계
  Azure Site Recovery 배포를 관리하는 역할 기반 액세스 제어에 대해 [자세히 알아봅니다](site-recovery-role-based-linked-access-control.md).

  [다중 테넌트 VMware 환경 관리](site-recovery-multi-tenant-support-vmware-using-csp.md)
