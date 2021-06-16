---
title: 프라이빗 엔드포인트
description: Azure Backup에 대한 프라이빗 엔드포인트를 만드는 프로세스와 프라이빗 엔드포인트를 사용하여 리소스의 보안을 유지하는 시나리오를 이해합니다.
ms.topic: conceptual
ms.date: 05/07/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a44d6d31edb6329c11103e99f0b21aa1ea686ca3
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678326"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup 프라이빗 엔드포인트

Azure Backup에서는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Recovery Services 자격 증명 모음에서 데이터를 안전하게 백업하고 복원할 수 있습니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 하나 이상 사용하여 VNet에 서비스를 효과적으로 제공합니다.

이 문서는 Azure Backup에 대한 프라이빗 엔드포인트를 만드는 프로세스와 프라이빗 엔드포인트를 사용하여 리소스의 보안을 유지하는 시나리오를 이해하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 프라이빗 엔드포인트는 자격 증명 모음에 등록된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 대한 항목을 보호하려면 프라이빗 엔드포인트를 만들어야 합니다.
- 한 개의 가상 네트워크에는 여러 Recovery Services 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함될 수 있습니다. 또한 하나의 Recovery Services 자격 증명 모음에는 여러 가상 네트워크에 이 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함될 수 있습니다. 그러나 자격 증명 모음에 대해 만들 수 있는 최대 프라이빗 엔드포인트 수는 12입니다.
- 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들면 자격 증명 모음이 잠깁니다. 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함된 네트워크 이외의 네트워크에서 액세스할 수 없습니다(백업 및 복원의 경우). 자격 증명 모음에 대한 모든 프라이빗 엔드포인트를 제거하는 경우 모든 네트워크에서 자격 증명 모음에 액세스할 수 있습니다.
- 백업을 위한 프라이빗 엔드포인트 연결에는 Azure Backup에서 스토리지에 사용하는 IP를 포함하여 서브넷에 총 11개의 개인 IP가 사용됩니다. 이 숫자는 특정 Azure 지역의 경우 더 클 수 있습니다(최대 25개). 따라서 백업용 프라이빗 엔드포인트를 만들려는 경우 사용할 수 있는 충분한 개인 IP가 있는 것이 좋습니다.
- Recovery Services 자격 증명 모음은 Azure Backup 및 Azure Site Recovery에서 모두 사용하지만 이 문서에서는 Azure Backup 전용 프라이빗 엔드포인트를 사용하는 방법을 설명합니다.
- Azure Active Directory는 현재 프라이빗 엔드포인트를 지원하지 않습니다. 따라서 Azure Active Directory가 지역에서 작동하는 데 필요한 IP 및 FQDN은 Azure VMs에서 데이터베이스의 백업을 수행하고 MARS 에이전트를 사용하여 백업할 때 보안 네트워크에서의 아웃바운드 액세스가 허용되어야 합니다. 해당하는 경우 Azure AD 액세스를 허용하기 위해 NSG 태그 및 Azure Firewall 태그를 사용할 수도 있습니다.
- 프라이빗 엔드포인트에서는 네트워크 정책을 사용하는 가상 네트워크가 지원되지 않습니다. 계속하려면 [네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)해야 합니다.
- 2020년 5월 1일 이전에 등록한 경우 구독에 Recovery Services 리소스 공급 기업을 다시 등록해야 합니다. 공급 기업을 다시 등록하려면 Azure Portal에서 구독으로 이동하고, 왼쪽 탐색 모음에서 **리소스 공급자** 로 이동한 다음 **Microsoft.RecoveryServices** 를 선택하고, **다시 등록** 을 선택합니다.
- 자격 증명 모음에 프라이빗 엔드포인트를 사용하도록 설정된 경우 SQL 및 SAP HANA 데이터베이스 백업에 대한 [지역 간 복원](backup-create-rs-vault.md#set-cross-region-restore)이 지원되지 않습니다.
- 프라이빗 엔드포인트를 사용하여 이미 Recovery Services 자격 증명 모음을 새 테넌트로 이동하는 경우 자격 증명 모음의 관리 ID를 다시 만들고 구성하고 필요에 따라 새 프라이빗 엔드포인트를 만들기 위해 Recovery Services 자격 증명 모음을 업데이트해야 합니다. 이 작업을 수행하지 않으면 백업 및 복원 작업이 실패하기 시작합니다. 또한 구독 내에서 설정된 모든 RBAC(역할 기반 액세스 제어) 권한을 다시 구성해야 합니다.

## <a name="recommended-and-supported-scenarios"></a>권장 및 지원되는 시나리오

자격 증명 모음에 프라이빗 엔드포인트를 사용하도록 설정하면 Azure VM 및 MARS 에이전트 백업에서만 SQL 및 SAP HANA 워크로드의 백업 및 복원에 사용됩니다. 다른 워크로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 그러나 프라이빗 엔드포인트는 필요하지 않습니다. 프라이빗 엔드포인트는 SQL 및 SAP HANA 워크로드 백업 및 MARS 에이전트를 사용한 백업 외에도 Azure VM 백업을 위한 파일 복구를 수행하는 데 사용됩니다. 자세한 내용은 다음 표를 참조하세요.

| Azure VM에서 워크로드 백업(SQL, SAP HANA), MARS 에이전트를 사용하여 백업 | 가상 네트워크에서 Azure Backup 또는 Azure Storage에 대해 허용 목록에 IP/FQDN을 추가하지 않고도 백업 및 복원을 허용하려면 프라이빗 엔드포인트를 사용하는 것이 좋습니다. 해당 시나리오에서는 SQL Database를 호스트하는 VMs에서 Azure AD IP 또는 FQDN에 연결할 수 있는지 확인합니다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 백업**                                         | VM 백업을 위해 IP 또는 FQDN에 액세스하지 않아도 됩니다. 따라서 디스크 백업 및 복원용 프라이빗 엔드포인트가 필요하지 않습니다.  <br><br>   그러나 프라이빗 엔드포인트가 포함된 자격 증명 모음에서 파일 복구는 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함된 가상 네트워크로 제한됩니다. <br><br>    ACL로 지정된 비관리 디스크를 사용하는 경우 디스크가 포함된 스토리지 계정이 **신뢰할 수 있는 Microsoft 서비스** 에 대한 액세스를 허용하는지 확인합니다(ACL로 지정된 경우). |
| **Azure Files 백업**                                      | Azure Files 백업은 로컬 스토리지 계정에 저장됩니다. 따라서 백업 및 복원을 위한 프라이빗 엔드포인트가 필요하지 않습니다. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>백업용 프라이빗 엔드포인트 만들기 시작

다음 섹션에서는 가상 네트워크 내에서 Azure Backup용 프라이빗 엔드포인트를 만들고 사용하는 단계에 대해 설명합니다.

>[!IMPORTANT]
> 이 문서에 설명된 것과 동일한 순서로 단계를 수행하는 것이 좋습니다. 그렇게 하지 않으면 자격 증명 모음이 프라이빗 엔드포인트를 사용할 수 없게 되어 새 자격 증명 모음으로 프로세스를 다시 시작해야 하는 상황이 될 수 있습니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

백업용 프라이빗 엔드포인트는 보호된 항목이 없는(또는 이전에 보호하거나 등록하려고 시도한 항목이 없는) Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 시작할 새 자격 증명 모음을 만드는 것이 좋습니다. 새 자격 증명 모음을 만드는 방법에 대한 자세한 내용은 [Recovery Services 자격 증명 모음 만들기 및 구성](backup-create-rs-vault.md)을 참조하세요.

Azure Resource Manager 클라이언트를 사용하여 자격 증명 모음을 만드는 방법을 알아보려면 [이 섹션](#create-a-recovery-services-vault-using-the-azure-resource-manager-client)을 참조하세요. 이렇게 하면 관리 ID가 이미 설정된 자격 증명 모음이 만들어집니다.

## <a name="enable-managed-identity-for-your-vault"></a>자격 증명 모음에 관리 ID 사용

관리 ID를 사용하면 자격 증명 모음을 사용하여 프라이빗 엔드포인트를 만들고 사용할 수 있습니다. 이 섹션에서는 자격 증명 모음에 관리 ID를 사용하는 방법을 설명합니다.

1. Recovery Services 자격 증명 모음 -> **ID** 로 이동합니다.

    ![ID 상태를 켜기로 변경](./media/private-endpoints/identity-status-on.png)

1. **상태** 를 **켜기** 로 변경하고 **저장** 을 선택합니다.

1. 자격 증명 모음의 관리 ID인 **개체 ID** 가 생성됩니다.

    >[!NOTE]
    >일단 사용하도록 설정하면 관리 ID를 일시적이라도 사용하지 **않도록** 설정하면 안 됩니다. 관리 ID를 사용하지 않도록 설정하면 일관성이 없는 동작이 발생할 수 있습니다.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>자격 증명 모음에 필요한 프라이빗 엔드포인트를 만들 수 있는 권한 부여

Azure Backup에 대한 필수 프라이빗 엔드포인트를 만들려면 자격 증명 모음(자격 증명 모음의 관리 ID)에 다음 리소스 그룹에 대한 권한이 있어야 합니다.

- 대상 VNet이 포함된 리소스 그룹
- 프라이빗 엔드포인트를 만들 리소스 그룹
- 대로 프라이빗 DNS 영역이 포함된 리소스 그룹([여기](#create-private-endpoints-for-azure-backup)에 자세히 설명됨)

위의 세 개 리소스 그룹에 대한 **참가자** 역할을 자격 증명 모음(관리 ID)에 부여하는 것이 좋습니다. 다음 단계에서는 특정 리소스 그룹에 대해 이 작업을 수행하는 방법을 설명합니다(이 작업은 세 개 리소스 그룹 각각에 대해 수행해야 함).

1. 리소스 그룹으로 이동하고 왼쪽 막대에서 **액세스 제어(IAM)** 로 이동합니다.
1. **액세스 제어** 에서 **역할 할당 추가** 로 이동합니다.

    ![역할 할당 추가](./media/private-endpoints/add-role-assignment.png)

1. **역할 할당 추가** 창에서 **역할** 로 **참가자** 를 선택하고 자격 증명 모음 **이름** 을 **주체** 로 사용합니다. 자격 증명 모음을 선택하고 완료되면 **저장** 을 선택합니다.

    ![역할 및 주체 선택](./media/private-endpoints/choose-role-and-principal.png)

더 세부적인 수준에서 사용 권한을 관리하려면 [수동으로 역할 및 권한 만들기](#create-roles-and-permissions-manually)를 참조하세요.

## <a name="create-private-endpoints-for-azure-backup"></a>Azure Backup에 대한 프라이빗 엔드포인트 만들기

이 섹션에서는 자격 증명 모음에 대한 프라이빗 엔드포인트를 만드는 방법을 설명합니다.

1. 위에서 만든 자격 증명 모음으로 이동하고 왼쪽 탐색 모음에서 **프라이빗 엔드포인트 연결** 로 이동합니다. 맨 위에서 **+프라이빗 엔드포인트** 를 선택하여 이 자격 증명 모음에 대한 새 프라이빗 엔드포인트 만들기를 시작합니다.

    ![새 프라이빗 엔드포인트 만들기](./media/private-endpoints/new-private-endpoint.png)

1. **프라이빗 엔드포인트 만들기** 프로세스에서 프라이빗 엔드포인트 연결을 만드는 방법에 대한 세부 정보를 지정해야 합니다.
  
    1. **기본 사항**: 프라이빗 엔드포인트에 대한 기본 세부 정보를 입력합니다. 지역은 자격 증명 모음 및 백업은 리소스와 동일해야 합니다.

        ![기본 세부 정보 입력](./media/private-endpoints/basics-tab.png)

    1. **리소스**: 이 탭에서 연결을 만들려는 PaaS 리소스를 선택해야 합니다. 원하는 구독에 대한 리소스 종류에서 **Microsoft.RecoveryServices/vaults** 를 선택합니다. 완료되면 Recovery Services 자격 증명 모음 이름을 **Resource** 로, **AzureBackup** 이름을 **Target sub-resource** 로 선택합니다.

        ![연결에 대한 리소스를 선택합니다.](./media/private-endpoints/resource-tab.png)

    1. **구성**: 구성에서 프라이빗 엔드포인트를 만들 가상 네트워크 및 서브넷을 지정합니다. VM이 있는 Vnet이 됩니다.

        비공개로 연결하려면 필수 DNS 레코드가 필요합니다. 네트워크 설정에 따라 다음 중 하나를 선택할 수 있습니다.

          - 프라이빗 DNS 영역과 프라이빗 엔드포인트 통합: 통합하려는 경우 **예** 를 선택합니다.
          - 사용자 지정 DNS 서버 사용: 사용자의 DNS 서버를 사용하려는 경우 **아니요** 를 선택합니다.

        이 두 가지에 대한 DNS 레코드를 관리하는 방법은 [나중에 설명](#manage-dns-records)합니다.

          ![가상 네트워크 및 서브넷 지정](./media/private-endpoints/configuration-tab.png)

    1. 필요에 따라 프라이빗 엔드포인트에 대한 **태그** 를 추가할 수 있습니다.
    1. 세부 정보 입력을 완료한 후 **검토 + 만들기** 를 계속합니다. 유효성 검사가 완료되면 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다.

## <a name="approve-private-endpoints"></a>프라이빗 엔드포인트 승인

프라이빗 엔드포인트를 만드는 사용자가 Recovery Services 자격 증명 모음의 소유자이기도 한 경우 위에서 만든 프라이빗 엔드포인트는 자동으로 승인됩니다. 그렇지 않으면 자격 증명 모음 소유자가 프라이빗 엔드포인트를 승인해야 해당 엔드포인트를 사용할 수 있습니다. 이 섹션에서는 Azure Portal을 통해 프라이빗 엔드포인트를 수동으로 승인하는 방법에 대해 설명합니다.

Azure Resource Manager 클라이언트를 사용하여 프라이빗 엔드포인트를 승인하려면 [Azure Resource Manager 클라이언트를 사용하여 프라이빗 엔드포인트의 수동 승인](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client)을 참조하세요.

1. Recovery Services 자격 증명 모음에서 왼쪽 표시줄의 **프라이빗 엔드포인트 연결** 로 이동합니다.
1. 승인하려는 프라이빗 엔드포인트를 선택합니다.
1. 위쪽 표시줄에서 **승인** 을 선택합니다. 엔드포인트 연결을 거부하거나 삭제하려는 경우 **거부** 또는 **제거** 를 선택할 수도 있습니다.

    ![프라이빗 엔드포인트 승인](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>DNS 레코드 관리

앞에서 설명한 것처럼 비공개로 연결하려면 프라이빗 DNS 영역 또는 서버에 필수 DNS 레코드가 필요합니다. 사용자의 네트워크 기본 설정에 따라 프라이빗 엔드포인트를 Azure 프라이빗 DNS 영역에 직접 통합하거나 사용자 지정 DNS 서버를 사용하여 이 작업을 수행할 수 있습니다. 백업, Blob, 큐의 세 가지 서비스 모두에 대해 이 작업을 수행해야 합니다.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Azure 프라이빗 DNS 영역과 프라이빗 엔드포인트를 통합하는 경우

프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합하도록 선택하는 경우 백업에서 필수 DNS 레코드를 추가합니다. 프라이빗 엔드포인트의 **DNS 구성** 에서 사용되는 프라이빗 DNS 영역을 볼 수 있습니다. 프라이빗 DNS 영역이 없는 경우 프라이빗 엔드포인트를 만들 때 자동으로 만들어집니다. 그러나 아래에 설명된 대로 가상 네트워크(백업할 리소스가 포함됨)가 세 개의 프라이빗 DNS 영역 모두에 올바르게 연결되어 있는지 확인해야 합니다.

![Azure 프라이빗 DNS 영역의 DNS 구성](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>프라이빗 DNS 영역에서 가상 네트워크 링크의 유효성 검사

위에 나열된 **각 프라이빗 DNS** 영역(백업, Blob, 큐)에 대해 다음을 수행합니다.

1. 왼쪽 탐색 모음에서 해당하는 **가상 네트워크 링크** 옵션으로 이동합니다.
1. 다음과 같이 프라이빗 엔드포인트를 만든 가상 네트워크에 대한 항목을 볼 수 있습니다.

    ![프라이빗 엔드포인트에 대한 가상 네트워크](./media/private-endpoints/virtual-network-links.png)

1. 항목이 표시되지 않으면 가상 네트워크 링크를 포함하지 않는 모든 DNS 영역에 가상 네트워크 링크를 추가합니다.

    ![가상 네트워크 링크 추가](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>사용자 지정 DNS 서버 또는 호스트 파일을 사용하는 경우

사용자 지정 DNS 서버를 사용하는 경우 필요한 DNS 영역을 만들고 프라이빗 엔드포인트에 필요한 DNS 레코드를 DNS 서버에 추가해야 합니다. Blob 및 큐에 조건부 전달자를 사용할 수도 있습니다.

#### <a name="for-the-backup-service"></a>백업 서비스의 경우

1. DNS 서버에서 다음 명명 규칙에 따라 백업용 DNS 영역을 만듭니다.

    |영역 |서비스 |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > 위의 텍스트에서는 `<geo>`는 지역 코드(예: 미국 동부 및 북유럽의 경우 각각 *eus* 및 *ne*)를 나타냅니다. 지역 코드에 대해서는 다음 목록을 참조하세요.
    >
    > - [모든 퍼블릭 클라우드](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [중국](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [독일](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. 다음으로 필수 DNS 레코드를 추가해야 합니다. 백업 DNS 영역에 추가해야 하는 레코드를 보려면 위에서 만든 프라이빗 엔드포인트로 이동한 다음 왼쪽 탐색 모음 아래에 있는 **DNS 구성** 옵션으로 이동합니다.

    ![사용자 지정 DNS 서버에 대한 DNS 구성](./media/private-endpoints/custom-dns-configuration.png)

1. 백업용 DNS 영역에 A 유형 레코드로 표시되는 각 FQDN 및 IP에 대한 항목을 한 개 추가합니다. 이름 확인에 호스트 파일을 사용하는 경우 다음 형식에 따라 호스트 파일에 각 IP 및 FQDN에 대한 해당 항목을 만듭니다.

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>위의 스크린샷에 표시된 대로 FQDN은 `xxxxxxxx.<geo>.backup.windowsazure.com`을 보여 주고 `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`은 보여 주지 않습니다. 이런 경우 지정된 형식에 따라 `.privatelink.`를 포함했는지(및 필요한 경우 추가했는지) 확인합니다.

#### <a name="for-blob-and-queue-services"></a>Blob 및 큐 서비스의 경우

Blob 및 큐의 경우 조건부 전달자를 사용하거나 DNS 서버에서 DNS 영역을 만들 수 있습니다.

##### <a name="if-using-conditional-forwarders"></a>조건부 전달자를 사용하는 경우

조건부 전달자를 사용하는 경우 다음과 같이 Blob 및 큐 FQDN에 대한 전달자를 추가합니다.

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>프라이빗 DNS 영역을 사용하는 경우

Blob 및 큐에 DNS 영역을 사용하는 경우 먼저 DNS 영역을 만들고 나중에 필수 A 레코드를 추가해야 합니다.

|영역 |서비스  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | 큐        |

현재 사용자 지정 DNS 서버를 사용하는 경우 Blob 및 큐에 대한 영역만 만듭니다. DNS 레코드 추가는 나중에 다음 두 단계에서 수행됩니다.

1. 첫 번째 백업 인스턴스를 등록할 때 즉, 처음으로 백업을 구성할 때
1. 첫 번째 백업을 실행할 때

위의 단계는 다음 섹션에 수행합니다.

## <a name="use-private-endpoints-for-backup"></a>백업에 프라이빗 엔드포인트 사용

VNet의 자격 증명 모음에 대해 만들어진 프라이빗 엔드포인트가 승인되면 해당 엔드포인트를 사용하여 백업 및 복원을 시작할 수 있습니다.

>[!IMPORTANT]
>계속하기 전에 문서의 위에서 언급한 단계를 모두 완료했는지 확인합니다. 요약하면 다음 검사 목록의 단계를 완료했어야 합니다.
>
>1. (새) Recovery Services 자격 증명 모음 만들기
>2. 시스템이 할당한 관리 ID를 사용하도록 자격 증명 모음 설정
>3. 자격 증명 모음의 관리 ID에 관련 권한 할당
>4. 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기
>5. 프라이빗 엔드포인트 승인(자동 승인되지 않은 경우)
>6. 모든 DNS 레코드가 적절히 추가되었는지 확인(다음 섹션에서 설명하는 사용자 지정 서버의 Blob 및 큐 레코드 제외)

### <a name="check-vm-connectivity"></a>VM 연결 확인

잠긴 네트워크의 VM에서 다음을 확인합니다.

1. VM에 AAD에 대한 액세스 권한이 있어야 합니다.
2. VM에서 백업 URL(`xxxxxxxx.privatelink.<geo>.backup. windowsazure.com`)에 대해 **nslookup** 을 실행하여 연결을 확인합니다. 그러면 가상 네트워크에 할당된 개인 IP가 반환됩니다.

### <a name="configure-backup"></a>백업 구성

위의 검사 목록 및 액세스가 성공적으로 완료되었는지 확인한 후 자격 증명 모음에 워크로드에 대한 백업을 계속 구성할 수 있습니다. 사용자 지정 DNS 서버를 사용하는 경우 첫 번째 백업을 구성한 후에 사용할 수 있는 Blob 및 큐에 대한 DNS 항목을 추가해야 합니다.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>첫 번째 등록 후 Blob 및 큐(사용자 지정 DNS 서버/호스트 파일만 해당)에 대한 DNS 레코드

프라이빗 엔드포인트 사용 자격 증명 모음에서 하나 이상의 리소스에 대한 백업을 구성한 후에 아래에 설명된 대로 Blob 및 큐의 필수 DNS 레코드를 추가합니다.

1. 리소스 그룹으로 이동한 다음 만든 프라이빗 엔드포인트를 검색합니다.
1. 사용자가 지정한 프라이빗 엔드포인트 이름 외에도 만들고 있는 두 개 이상의 프라이빗 엔드포인트가 표시됩니다. 엔드포인트는 `<the name of the private endpoint>_ecs`로 시작하고 맨 끝에 `_blob` 및 `_queue`가 각각 추가됩니다.

    ![프라이빗 엔드포인트 리소스](./media/private-endpoints/private-endpoint-resources.png)

1. 각 프라이빗 엔드포인트로 이동합니다. 두 프라이빗 엔드포인트 각각에 대한 DNS 구성 옵션에서 FQDN 및 IP 주소를 사용하는 레코드를 볼 수 있습니다. 앞에서 설명한 것 외에도 이 둘을 모두 사용자 지정 DNS 서버에 추가합니다.
호스트 파일을 사용하는 경우 다음 형식에 따라 각 IP/FQDN의 호스트 파일에 해당 항목을 만듭니다.

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Blob DNS 구성](./media/private-endpoints/blob-dns-configuration.png)

위의 항목 외에도 첫 번째 백업 후에 다른 항목이 필요합니다. 이러한 항목에 대해서는 [나중에](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup) 설명합니다.

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Azure VM에서 워크로드 백업 및 복원(SQL 및 SAP HANA)

프라이빗 엔드포인트를 만들고 승인한 후에는 프라이빗 엔드포인트를 사용하기 위해 클라이언트 쪽에서 다른 변경이 필요하지 않습니다(이 섹션의 뒷부분에서 설명하는 SQL 가용성 그룹을 사용하지 않는 경우). 보안 네트워크에서 자격 증명 모음으로의 모든 통신 및 데이터 전송은 프라이빗 엔드포인트를 통해 수행됩니다. 그러나 서버(SQL 또는 SAP HANA)를 등록한 후에 자격 증명 모음에 대한 프라이빗 엔드포인트를 제거하는 경우 컨테이너를 자격 증명 모음에 다시 등록해야 합니다. 보호를 중지할 필요는 없습니다.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>첫 번째 백업 후 Blob(사용자 지정 DNS 서버/호스트 파일만 해당)에 대한 DNS 레코드

첫 번째 백업을 실행하고 사용자 지정 DNS 서버를 사용하는 경우(조건부 전달 제외) 백업이 실패할 수 있습니다. 이 경우 다음을 수행합니다.

1. 리소스 그룹으로 이동한 다음 만든 프라이빗 엔드포인트를 검색합니다.
1. 앞에서 설명한 세 개의 프라이빗 엔드포인트 외에도 이제 이름이 `<the name of the private endpoint>_prot`로 시작하는 네 번째 프라이빗 엔드포인트가 표시되 고 맨 끝에 `_blob`이 붙습니다.

    !["prot" 접미사를 사용하는 프라이빗 엔드포인트](./media/private-endpoints/private-endpoint-prot.png)

1. 이 새 프라이빗 엔드포인트로 이동합니다. DNS 구성 옵션에서 FQDN 및 IP 주소를 포함하는 레코드를 볼 수 있습니다. 앞에서 설명한 것 외에도 레코드를 프라이빗 DNS 서버에 추가합니다.

    호스트 파일을 사용하는 경우 다음 형식에 따라 각 IP 및 FQDN의 호스트 파일에 해당 항목을 만듭니다.

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>이 시점에서 자격 증명 모음 백업 및 스토리지 URL에서 작업을 수행하면 VM에서 **nslookup** 을 실행하고 개인 IP 주소를 확인할 수 있습니다.

### <a name="when-using-sql-availability-groups"></a>SQL 가용성 그룹을 사용하는 경우

SQL AG(가용성 그룹)를 사용하는 경우 아래에 설명된 대로 사용자 지정 AG DNS에서 조건부 전달을 프로비저닝해야 합니다.

1. 도메인 컨트롤러에 로그인합니다.
1. DNS 애플리케이션에서 필요에 따라 호스트 IP 168.63.129.16 또는 사용자 지정 DNS 서버 IP 주소에 세 개의 DNS 영역(백업, Blob, 큐) 모두에 대한 조건부 전달자를 추가합니다. 다음 스크린샷은 Azure 호스트 IP에 전달하는 경우를 보여 줍니다. 고유한 DNS 서버를 사용하는 경우 DNS 서버의 IP로 바꿉니다.

    ![DNS 관리자의 조건부 전달자](./media/private-endpoints/dns-manager.png)

    ![새 조건부 전달자](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>MARS 에이전트를 통해 백업 및 복원

MARS 에이전트를 사용하여 온-프레미스 리소스를 백업하는 경우 (백업할 리소스가 포함된) 온-프레미스 네트워크는 자격 증명 모음의 프라이빗 엔드포인트를 포함하는 Azure VNet과 피어링되므로 사용할 수 있습니다. 그러면 MARS 에이전트를 계속 설치하고 여기에 설명된 대로 백업을 구성할 수 있습니다. 다만 백업을 위한 모든 통신은 피어링된 네트워크를 통해 수행되어야 합니다.

하지만 MARS 에이전트를 등록한 후에 자격 증명 모음의 프라이빗 엔드포인트를 제거하는 경우 컨테이너를 자격 증명 모음에 다시 등록해야 합니다. 보호를 중지할 필요는 없습니다.

## <a name="deleting-private-endpoints"></a>프라이빗 엔드포인트 삭제

프라이빗 엔드포인트를 삭제하는 방법에 대해 알아보려면 [이 섹션](/rest/api/virtualnetwork/privateendpoints/delete)을 참조하세요.

## <a name="additional-topics"></a>추가 항목

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용하여 Recovery Services 자격 증명 모음 만들기

Azure Resource Manager 클라이언트를 사용하여 Recovery Services 자격 증명 모음을 만들고 관리 ID를 사용하도록 설정할 수 있습니다(나중에 표시되는 대로 관리 ID를 사용하도록 설정해야 함). 이 작업을 수행하는 샘플이 아래에 공유되어 있습니다.

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

위의 JSON 파일에는 다음 콘텐츠가 있어야 합니다.

요청 JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

응답 JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>이 예제에서 Azure Resource Manager 클라이언트를 통해 만든 자격 증명 모음이 이미 시스템이 할당한 관리 ID로 생성되었습니다.

### <a name="managing-permissions-on-resource-groups"></a>리소스 그룹에 대한 사용 권한 관리

자격 증명 모음에 대한 관리 ID에는 프라이빗 엔드포인트가 생성될 가상 네트워크 및 리소스 그룹에 대한 다음 권한이 있어야 합니다.

- `Microsoft.Network/privateEndpoints/*` 리소스 그룹에서 프라이빗 엔드포인트에 CRUD를 수행하는 데 필요합니다. 리소스 그룹에서 할당해야 합니다.
- `Microsoft.Network/virtualNetworks/subnets/join/action` 개인 IP가 프라이빗 엔드포인트와 연결되는 가상 네트워크에 필요합니다.
- `Microsoft.Network/networkInterfaces/read` 프라이빗 엔드포인트에 대해 생성된 네트워크 인터페이스를 가져오기 위해 리소스 그룹에 필요합니다.
- 프라이빗 DNS 영역 참가자 역할 이 역할은 이미 있으며 `Microsoft.Network/privateDnsZones/A/*` 및 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` 사용 권한을 제공하는 데 사용할 수 있습니다.

다음 방법 중 하나를 사용하여 필요한 권한이 있는 역할을 만들 수 있습니다.

#### <a name="create-roles-and-permissions-manually"></a>수동으로 역할 및 권한 만들기

다음 JSON 파일을 만들고 섹션의 끝에 있는 PowerShell 명령을 사용하여 역할을 만듭니다.

//PrivateEndpointContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//NetworkInterfaceReaderRoleDef.json

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

//PrivateEndpointSubnetContributorRoleDef.json

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>스크립트 사용

1. Azure Portal에서 **Cloud Shell** 을 시작하고 PowerShell 창에서 **파일 업로드** 를 선택합니다.

    ![PowerShell 창에서 파일 업로드 선택](./media/private-endpoints/upload-file-in-powershell.png)

1. [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1) 스크립트를 업로드합니다.

1. 홈 폴더로 이동합니다(예: `cd /home/user`).

1. 다음 스크립트를 실행합니다.

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    매개 변수는 다음과 같습니다.

    - **subscription**: **자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 리소스 그룹 및 자격 증명 모음의 프라이빗 엔드포인트가 연결되는 서브넷이 있는 SubscriptionId

    - **vaultPEResourceGroup**: 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 리소스 그룹

    - **vaultPESubnetResourceGroup**: 프라이빗 엔드포인트가 가입될 서브넷의 리소스 그룹

    - **vaultMsiName**: **VaultName** 과 동일한 자격 증명 모음의 MSI 이름

1. 인증을 완료하면 스크립트가 위에 제공된 지정된 구독의 컨텍스트를 사용합니다. 테넌트에서 누락된 경우 적절한 역할을 만들고 자격 증명 모음의 MSI에 역할을 할당합니다.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Azure PowerShell을 사용하여 프라이빗 엔드포인트 만들기

#### <a name="auto-approved-private-endpoints"></a>자동 승인된 프라이빗 엔드포인트

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용하여 프라이빗 엔드포인트 수동 승인

1. **GetVault** 를 사용하여 프라이빗 엔드포인트에 대한 프라이빗 엔드포인트 연결 ID를 가져옵니다.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    그러면 프라이빗 엔드포인트 연결 ID가 반환됩니다. 다음과 같이 연결 ID의 첫 번째 부분을 사용하여 연결 이름을 검색할 수 있습니다.

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 아래 샘플에 나와 있는 대로 **프라이빗 엔드포인트 연결 ID**(및 필요한 경우 **프라이빗 엔드포인트 이름**)를 응답에서 가져온 후 다음 JSON 및 Azure Resource Manager URI에서 바꾸고 상태를 "Approved/Rejected/Disconnected"로 변경해 보세요.

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>질문과 대답

17. 기존 백업 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 수 있나요?<br>
A. 아니요. 프라이빗 엔드포인트는 새 백업 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에는 보호된 항목이 없어야 합니다. 실제로 프라이빗 엔드포인트를 만들기 전에 자격 증명 모음에 대한 항목을 보호하려는 시도를 할 수 없습니다.

17. 자격 증명 모음에 대한 항목을 보호하려고 했지만 실패했고 자격 증명 모음에 보호된 항목이 아직 포함되어 있지 않습니다. 이 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 수 있나요?<br>
A. 아니요, 자격 증명 모음은 과거에 항목을 보호하려고 시도한 적이 없어야 합니다.

17. 백업 및 복원을 위해 프라이빗 엔드포인트를 사용하는 자격 증명 모음이 있습니다. 백업 항목이 보호된 경우에도 나중에 이 자격 증명 모음에 대한 프라이빗 엔드포인트를 추가하거나 제거할 수 있나요?<br>
A. 예. 자격 증명 모음 및 보호된 백업 항목에 대한 프라이빗 엔드포인트를 이미 만든 경우 나중에 필요에 따라 프라이빗 엔드포인트를 추가하거나 제거할 수 있습니다.

17. Azure Backup에 대한 프라이빗 엔드포인트를 Azure Site Recovery에도 사용할 수 있나요?<br>
A. 아니요, 백업용 프라이빗 엔드포인트는 Azure Backup에만 사용할 수 있습니다. 서비스에서 지원하는 경우 Azure Site Recovery에 대한 새 프라이빗 엔드포인트를 만들어야 합니다.

17. 이 문서의 단계 중 하나를 누락했고 데이터 원본을 보호하는 데 문제가 발생했습니다. 여전히 프라이빗 엔드포인트를 사용할 수 있나요?<br>
A. 이 문서의 단계를 따르지 않고 항목 보호를 계속하면 자격 증명 모음에서 프라이빗 엔드포인트를 사용하지 못할 수 있습니다. 따라서 항목 보호를 계속하기 전에 검사 목록을 참조하는 것이 좋습니다.

17. Azure 프라이빗 DNS 영역 또는 통합된 프라이빗 DNS 영역을 사용하는 대신 자체 DNS 서버를 사용할 수 있나요?<br>
A. 예, 고유한 DNS 서버를 사용할 수 있습니다. 그러나 이 섹션에서 제안한 대로 필수 DNS 레코드가 모두 추가되었는지 확인합니다.

17. 이 문서의 프로세스를 따른 후 서버에서 추가 단계를 수행해야 하나요?<br>
A. 이 문서에 자세히 설명된 프로세스를 따른 후에는 백업 및 복원에 프라이빗 엔드포인트를 사용하기 위해 추가 작업을 수행할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 모든 보안 기능](security-overview.md)에 대한 정보 읽기.
