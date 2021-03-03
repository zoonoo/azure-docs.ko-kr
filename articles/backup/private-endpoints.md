---
title: 프라이빗 엔드포인트
description: Azure Backup에 대 한 개인 끝점을 만드는 프로세스와 전용 끝점을 사용 하 여 리소스의 보안을 유지 하는 시나리오를 이해 합니다.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 9363aaf45a7c092d8a773a07803c8c1bce1eedd7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728215"
---
# <a name="private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점

Azure Backup를 사용 하면 [개인 끝점](../private-link/private-endpoint-overview.md)을 사용 하 여 Recovery Services 자격 증명 모음에서 데이터를 안전 하 게 백업 하 고 복원할 수 있습니다. 개인 끝점은 VNet의 개인 IP 주소를 하나 이상 사용 하 여 서비스를 VNet에 효과적으로 제공 합니다.

이 문서는 Azure Backup에 대 한 개인 끝점을 만드는 프로세스와 개인 끝점을 사용 하 여 리소스의 보안을 유지 하는 시나리오를 이해 하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 개인 끝점은 자격 증명 모음에 등록 된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 대 한 항목을 보호 하려고 하기 전에 개인 끝점을 만들어야 합니다.
- 하나의 가상 네트워크는 여러 Recovery Services 자격 증명 모음에 대 한 개인 끝점을 포함할 수 있습니다. 또한 하나의 Recovery Services 자격 증명 모음에 대해 여러 가상 네트워크에서 개인 끝점을 포함할 수 있습니다. 그러나 자격 증명 모음에 대해 만들 수 있는 최대 개인 끝점 수는 12입니다.
- 자격 증명 모음에 대 한 개인 끝점을 만들면 자격 증명 모음이 잠깁니다. 자격 증명 모음에 대 한 개인 끝점을 포함 하는 네트워크에서 네트워크를 통해 액세스할 수 없습니다 (백업 및 복원의 경우). 자격 증명 모음에 대 한 모든 개인 끝점을 제거 하는 경우 모든 네트워크에서 자격 증명 모음에 액세스할 수 있습니다.
- 백업용 개인 끝점 연결에는 저장소에 대 한 Azure Backup에서 사용 하는 것을 포함 하 여 서브넷에 총 11 개의 개인 Ip가 사용 됩니다. 이 숫자는 특정 Azure 지역에 대해 더 높을 수 있습니다 (최대 25 개). 따라서 백업용 개인 끝점을 만들려고 할 때 사용할 수 있는 개인 Ip가 충분 하다는 것이 좋습니다.
- Recovery Services 자격 증명 모음은 (둘 다) Azure Backup 및 Azure Site Recovery에서 사용 되지만이 문서에서는 Azure Backup 전용 전용 끝점을 사용 하는 방법을 설명 합니다.
- Azure Active Directory는 현재 개인 끝점을 지원 하지 않습니다. 따라서 지역에서 작업을 수행 하는 데 Azure Active Directory 필요한 Ip 및 Fqdn은 Azure Vm에서 데이터베이스의 백업을 수행 하 고 MARS 에이전트를 사용 하 여 백업할 때 보안 네트워크에서의 아웃 바운드 액세스를 허용 해야 합니다. 해당 하는 경우 Azure AD에 대 한 액세스를 허용 하기 위해 NSG 태그 및 Azure 방화벽 태그를 사용할 수도 있습니다.
- 네트워크 정책을 사용 하는 가상 네트워크는 전용 끝점에 대해 지원 되지 않습니다. 계속 하기 전에 네트워크 정책을 사용 하지 않도록 설정 해야 합니다.
- 1 2020 년 5 월 이전에 등록 한 경우 구독에 Recovery Services 리소스 공급자를 다시 등록 해야 합니다. 공급자를 다시 등록 하려면 Azure Portal의 구독으로 이동 하 여 왼쪽 탐색 모음에서 **리소스 공급자** 로 이동 하 고, **Microsoft recoveryservices** 를 선택 하 고, **다시 등록** 을 선택 합니다.
- 자격 증명 모음에서 전용 끝점을 사용 하는 경우 SQL 및 SAP HANA 데이터베이스 백업에 대 한 [지역 간 복원이](backup-create-rs-vault.md#set-cross-region-restore) 지원 되지 않습니다.
- 이미 개인 끝점을 사용 하는 Recovery Services 자격 증명 모음을 새 테 넌 트로 이동 하는 경우 자격 증명 모음 관리 id를 다시 구성 하 고 필요에 따라 새 개인 끝점을 만들려면 (새 테 넌 트에 있어야 함) Recovery Services 자격 증명 모음을 업데이트 해야 합니다. 이 작업을 수행 하지 않으면 백업 및 복원 작업에 실패 하 게 됩니다. 또한 구독 내에서 설정 된 모든 RBAC (역할 기반 액세스 제어) 권한을 다시 구성 해야 합니다.

## <a name="recommended-and-supported-scenarios"></a>권장 및 지원 되는 시나리오

개인 끝점은 자격 증명 모음에 대해 사용 하도록 설정 되어 있지만 Azure VM 및 MARS 에이전트 백업 에서만 SQL 및 SAP HANA 워크 로드의 백업 및 복원에 사용 됩니다. 다른 워크 로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 단, 개인 끝점은 필요 하지 않습니다. MARS 에이전트를 사용 하 여 SQL 및 SAP HANA 작업과 백업 백업 외에도 개인 끝점은 Azure VM 백업에 대 한 파일 복구를 수행 하는 데 사용 됩니다. 자세한 내용은 다음 표를 참조하세요.

| Azure VM에서 워크 로드 백업 (SQL, SAP HANA), MARS 에이전트를 사용 하 여 백업 | 가상 네트워크에서 Azure Backup 또는 Azure Storage에 대해 허용 목록에 Ip/Fqdn을 추가 하지 않고도 백업 및 복원을 허용 하려면 개인 끝점을 사용 하는 것이 좋습니다. 이 시나리오에서는 SQL 데이터베이스를 호스트 하는 Vm이 Azure AD Ip 또는 Fqdn에 도달할 수 있는지 확인 합니다. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Azure VM 백업**                                         | VM 백업에는 Ip 또는 Fqdn에 대 한 액세스를 허용 하지 않아도 됩니다. 따라서 디스크의 백업 및 복원을 위한 개인 끝점은 필요 하지 않습니다.  <br><br>   그러나 개인 끝점을 포함 하는 자격 증명 모음에서 파일 복구는 자격 증명 모음에 대 한 개인 끝점을 포함 하는 가상 네트워크로 제한 됩니다. <br><br>    ACL'ed 관리 되지 않는 디스크를 사용 하는 경우 디스크를 포함 하는 저장소 계정에서 ACL'ed 경우 **신뢰할 수 있는 Microsoft 서비스** 에 대 한 액세스를 허용 하는지 확인 합니다. |
| **백업 Azure Files**                                      | Azure Files 백업은 로컬 저장소 계정에 저장 됩니다. 따라서 백업 및 복원에 대 한 개인 끝점은 필요 하지 않습니다. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>백업용 개인 끝점 만들기 시작

다음 섹션에서는 가상 네트워크 내에서 Azure Backup에 대 한 개인 끝점을 만들고 사용 하는 단계에 대해 설명 합니다.

>[!IMPORTANT]
> 이 문서에 설명 된 것과 동일한 순서로 단계를 수행 하는 것이 좋습니다. 이렇게 하지 않으면 개인 끝점을 사용 하 고 새 자격 증명 모음을 사용 하 여 프로세스를 다시 시작 해야 하는 자격 증명 모음이 호환 되지 않는 것으로 이어질 수 있습니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

백업에 대 한 개인 끝점은 보호 된 항목이 없는 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다 (또는 이전에 보호 또는 등록 하려고 시도한 항목이 없는 경우). 따라서 시작할 새 자격 증명 모음을 만드는 것이 좋습니다. 새 자격 증명 모음을 만드는 방법에 대 한 자세한 내용은  [Recovery Services 자격 증명 모음 만들기 및 구성](backup-create-rs-vault.md)을 참조 하세요.

Azure Resource Manager 클라이언트를 사용 하 여 자격 증명 모음을 만드는 방법을 알아보려면 [이 섹션](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) 을 참조 하세요. 이렇게 하면 관리 되는 id가 이미 설정 된 자격 증명 모음이 만들어집니다.

## <a name="enable-managed-identity-for-your-vault"></a>자격 증명 모음에 관리 되는 Id 사용

관리 id를 사용 하면 자격 증명 모음이 개인 끝점을 만들고 사용할 수 있습니다. 이 섹션에서는 자격 증명 모음에 관리 되는 id를 사용 하도록 설정 하는 방법을 설명 합니다.

1. Recovery Services 자격 증명 모음 > **id** 로 이동 합니다.

    ![Id 상태를 설정으로 변경](./media/private-endpoints/identity-status-on.png)

1. **상태** 를 **켜기** 로 변경 하 고 **저장** 을 선택 합니다.

1. 자격 증명 모음의 관리 되는 id 인 **개체 ID** 가 생성 됩니다.

    >[!NOTE]
    >일단 사용 하도록 설정 하면 관리 되는 Id를 일시적으로 **사용 하지 않도록 설정 해야 합니다** . 관리 id를 사용 하지 않도록 설정 하면 일관 되지 않은 동작이 발생할 수 있습니다.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>자격 증명 모음에 필요한 개인 끝점을 만들 수 있는 권한 부여

Azure Backup에 대 한 필수 개인 끝점을 만들려면 자격 증명 모음 (자격 증명 모음의 관리 Id)에 다음 리소스 그룹에 대 한 권한이 있어야 합니다.

- 대상 VNet을 포함 하는 리소스 그룹입니다.
- 개인 끝점을 만들 리소스 그룹입니다.
- [여기](#create-private-endpoints-for-azure-backup) 에 자세히 설명 된 대로 사설 DNS 영역을 포함 하는 리소스 그룹

이러한 세 개의 리소스 그룹에 대 한 **참가자** 역할을 자격 증명 모음 (관리 id)에 부여 하는 것이 좋습니다. 다음 단계에서는 특정 리소스 그룹에 대해이 작업을 수행 하는 방법을 설명 합니다 .이 작업은 세 가지 리소스 그룹 각각에 대해 수행 해야 합니다.

1. 리소스 그룹으로 이동 하 여 왼쪽 막대에서 **Access Control (IAM)** 로 이동 합니다.
1. **Access Control** 한 후 **역할 할당 추가** 로 이동 합니다.

    ![역할 할당 추가](./media/private-endpoints/add-role-assignment.png)

1. **역할 할당 추가** 창에서 **역할** 로 **참가자** 를 선택 하 고 자격 증명 모음 **이름을** **주** 서버로 사용 합니다. 자격 증명 모음을 선택 하 고 완료 되 면 **저장** 을 선택 합니다.

    ![역할 및 주체 선택](./media/private-endpoints/choose-role-and-principal.png)

더 세부적인 수준에서 사용 권한을 관리 하려면 [수동으로 역할 및 권한 만들기](#create-roles-and-permissions-manually)를 참조 하세요.

## <a name="create-private-endpoints-for-azure-backup"></a>Azure Backup에 대 한 개인 끝점 만들기

이 섹션에서는 자격 증명 모음에 대 한 개인 끝점을 만드는 방법을 설명 합니다.

1. 위에서 만든 자격 증명 모음으로 이동 하 여 왼쪽 탐색 모음에서 **개인 끝점 연결** 로 이동 합니다. 위쪽에서 **+ 개인 끝점** 을 선택 하 여이 자격 증명 모음에 대 한 새 개인 끝점 만들기를 시작 합니다.

    ![새 개인 끝점 만들기](./media/private-endpoints/new-private-endpoint.png)

1. **개인 끝점 만들기** 프로세스에서 개인 끝점 연결을 만드는 방법에 대 한 세부 정보를 지정 해야 합니다.
  
    1. **기본 사항**: 개인 끝점에 대 한 기본 세부 정보를 입력 합니다. 지역은 자격 증명 모음 및 백업 되는 리소스와 동일 해야 합니다.

        ![기본 정보 입력](./media/private-endpoints/basics-tab.png)

    1. **리소스**:이 탭에서 연결을 만들려는 PaaS 리소스를 선택 해야 합니다. 원하는 구독의 리소스 유형에 서 **Microsoft RecoveryServices/자격 증명 모음** 을 선택 합니다. 작업이 완료 되 면 **리소스** 및 **azurebackup** 의 Recovery Services 자격 증명 모음 이름을 **대상 하위 리소스로** 선택 합니다.

        ![연결에 대 한 리소스를 선택 합니다.](./media/private-endpoints/resource-tab.png)

    1. **구성**: 구성에서 개인 끝점을 만들 가상 네트워크 및 서브넷을 지정 합니다. 이는 VM이 있는 Vnet입니다.

        개인적으로 연결 하려면 필요한 DNS 레코드가 필요 합니다. 네트워크 설정에 따라 다음 중 하나를 선택할 수 있습니다.

          - 개인 DNS 영역에 개인 끝점 통합: 통합 하려는 경우 **예** 를 선택 합니다.
          - 사용자 지정 DNS 서버 사용: 사용자의 DNS 서버를 사용 하려는 경우 **아니요** 를 선택 합니다.

        이러한 두 가지 모두에 대 한 DNS 레코드를 관리 하는 방법에 대해 [설명](#manage-dns-records)합니다.

          ![가상 네트워크 및 서브넷 지정](./media/private-endpoints/configuration-tab.png)

    1. 필요에 따라 개인 끝점에 대 한 **태그** 를 추가할 수 있습니다.
    1. 자세히 입력을 완료 한 후 계속 **검토 + 만들기** 를 계속 합니다. 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.

## <a name="approve-private-endpoints"></a>개인 끝점 승인

개인 끝점을 만드는 사용자가 Recovery Services 자격 증명 모음의 소유자 이기도 한 경우 위에서 만든 개인 끝점은 자동으로 승인 됩니다. 그렇지 않으면 자격 증명 모음의 소유자가 개인 끝점을 사용 하려면 먼저 승인 해야 합니다. 이 섹션에서는 Azure Portal를 통한 개인 끝점의 수동 승인에 대해 설명 합니다.

Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점을 승인 하려면 [Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점의 수동 승인](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) 을 참조 하세요.

1. Recovery Services 자격 증명 모음에서 왼쪽 표시줄의 **개인 끝점 연결** 로 이동 합니다.
1. 승인 하려는 개인 끝점 연결을 선택 합니다.
1. 위쪽 막대에서 **승인** 을 선택 합니다. 끝점 연결을 거부 하거나 삭제 하려는 경우 **거부** 또는 **제거** 를 선택할 수도 있습니다.

    ![개인 끝점 승인](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>DNS 레코드 관리

앞서 설명한 것 처럼 개인용으로 연결 하려면 개인 DNS 영역 또는 서버에 필요한 DNS 레코드가 필요 합니다. 사용자의 네트워크 기본 설정에 따라 개인 끝점을 Azure 개인 DNS 영역에 직접 통합 하거나 사용자 지정 DNS 서버를 사용 하 여이를 달성할 수 있습니다. 백업, Blob 및 큐의 세 가지 서비스 모두에 대해이 작업을 수행 해야 합니다.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>Azure 사설 DNS 영역에 개인 끝점을 통합 하는 경우

개인 끝점을 개인 DNS 영역과 통합 하도록 선택 하는 경우 Backup에서 필요한 DNS 레코드를 추가 합니다. 개인 끝점의 **DNS 구성** 에서 사용 되는 개인 DNS 영역을 볼 수 있습니다. 이러한 DNS 영역이 없는 경우 개인 끝점을 만들 때 자동으로 만들어집니다. 그러나 아래에 설명 된 것 처럼 가상 네트워크 (백업할 리소스 포함)가 세 개의 개인 DNS 영역 모두에 올바르게 연결 되어 있는지 확인 해야 합니다.

![Azure 개인 DNS 영역의 DNS 구성](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>사설 DNS 영역에서 가상 네트워크 링크 유효성 검사

위에 나열 된 **각 개인 DNS** 영역 (백업, Blob 및 큐)에 대해 다음을 수행 합니다.

1. 왼쪽 탐색 모음에서 해당 하는 **가상 네트워크 링크** 옵션으로 이동 합니다.
1. 다음과 같이 개인 끝점을 만든 가상 네트워크에 대 한 항목을 볼 수 있습니다.

    ![전용 끝점에 대 한 가상 네트워크](./media/private-endpoints/virtual-network-links.png)

1. 항목이 표시 되지 않으면 가상 네트워크 링크를 포함 하지 않는 모든 DNS 영역에 추가 합니다.

    ![가상 네트워크 링크 추가](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>사용자 지정 DNS 서버 또는 호스트 파일을 사용 하는 경우

사용자 지정 DNS 서버를 사용 하는 경우 필요한 DNS 영역을 만들고 개인 끝점에 필요한 DNS 레코드를 DNS 서버에 추가 해야 합니다. Blob 및 큐의 경우 조건부 전달자를 사용할 수도 있습니다.

#### <a name="for-the-backup-service"></a>백업 서비스의 경우

1. DNS 서버에서 다음 명명 규칙에 따라 백업용 DNS 영역을 만듭니다.

    |영역 |서비스 |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > 위의 텍스트에서는 `<geo>` 지역 코드 (예: *EUS* , 미국 동부 및 서유럽  의 경우)를 참조 합니다. 지역 코드는 다음 목록을 참조 하세요.
    >
    > - [모든 공용 클라우드](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [중국](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [독일](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. 다음으로 필요한 DNS 레코드를 추가 해야 합니다. 백업 DNS 영역에 추가 해야 하는 레코드를 보려면 위에서 만든 개인 끝점으로 이동 하 여 왼쪽 탐색 모음 아래에 있는 **DNS 구성** 옵션으로 이동 합니다.

    ![사용자 지정 DNS 서버에 대 한 DNS 구성](./media/private-endpoints/custom-dns-configuration.png)

1. 백업할 DNS 영역에 유형 레코드로 표시 되는 각 FQDN 및 IP에 대해 하나의 항목을 추가 합니다. 이름 확인을 위해 호스트 파일을 사용 하는 경우 다음 형식에 따라 각 IP 및 FQDN에 대 한 호스트 파일에 해당 항목을 만듭니다.

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>위의 스크린샷에 표시 된 것 처럼 Fqdn은 표시 `xxxxxxxx.<geo>.backup.windowsazure.com` 되지 않습니다 `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . 이러한 경우에는 `.privatelink.` 지정 된 형식에 따라를 포함 하 고 필요에 따라 추가 합니다.

#### <a name="for-blob-and-queue-services"></a>Blob 및 큐 서비스의 경우

Blob 및 큐의 경우 조건부 전달자를 사용 하거나 DNS 서버에서 DNS 영역을 만들 수 있습니다.

##### <a name="if-using-conditional-forwarders"></a>조건부 전달자를 사용 하는 경우

조건부 전달자를 사용 하는 경우 다음과 같이 blob 및 큐 Fqdn에 대 한 전달자를 추가 합니다.

|FQDN  |IP  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>개인 DNS 영역을 사용 하는 경우

Blob 및 큐에 DNS 영역을 사용 하는 경우 먼저 이러한 DNS 영역을 만들고 나중에 필요한 A 레코드를 추가 해야 합니다.

|영역 |서비스  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | 큐        |

현재 사용자 지정 DNS 서버를 사용 하는 경우에만 blob 및 큐에 대 한 영역을 만듭니다. DNS 레코드 추가는 다음 두 단계에서 나중에 수행 됩니다.

1. 첫 번째 백업 인스턴스를 등록 하는 경우 (즉, 처음으로 백업을 구성 하는 경우)
1. 첫 번째 백업을 실행 하는 경우

다음 섹션에서 이러한 단계를 수행 합니다.

## <a name="use-private-endpoints-for-backup"></a>백업에 개인 끝점 사용

VNet에서 자격 증명 모음에 대해 만들어진 개인 끝점이 승인 되 면이를 사용 하 여 백업 및 복원을 수행할 수 있습니다.

>[!IMPORTANT]
>계속 하기 전에 문서에서 위에서 언급 한 모든 단계를 완료 했는지 확인 합니다. 이를 요약 하려면 다음 검사 목록의 단계를 완료 해야 합니다.
>
>1. (새) Recovery Services 자격 증명 모음을 만듦
>2. 시스템 할당 관리 Id를 사용 하도록 자격 증명 모음 설정
>3. 자격 증명 모음의 관리 Id에 대 한 관련 권한 할당
>4. 자격 증명 모음에 대 한 개인 끝점을 만들었습니다.
>5. 자동 승인 되지 않은 경우 개인 끝점 승인 됨
>6. 모든 DNS 레코드가 적절히 추가 되었는지 확인 (다음 섹션에서 설명 하는 사용자 지정 서버의 blob 및 큐 레코드 제외)

### <a name="check-vm-connectivity"></a>VM 연결 확인

잠긴 네트워크의 VM에서 다음을 확인 합니다.

1. VM은 AAD에 대 한 액세스 권한이 있어야 합니다.
2. VM에서 백업 URL ()에 대해 **nslookup** 를 실행 `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` 하 여 연결을 보장 합니다. 그러면 가상 네트워크에 할당 된 개인 IP가 반환 됩니다.

### <a name="configure-backup"></a>백업 구성

위의 검사 목록 및 액세스가 성공적으로 완료 되었는지 확인 한 후에는 계속 작업을 자격 증명 모음에 백업 하도록 구성할 수 있습니다. 사용자 지정 DNS 서버를 사용 하는 경우 첫 번째 백업을 구성한 후에 사용할 수 있는 blob 및 큐에 대 한 DNS 항목을 추가 해야 합니다.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>첫 번째 등록 후 blob 및 큐에 대 한 DNS 레코드 (사용자 지정 DNS 서버/호스트 파일에만 해당)

개인 끝점 사용 자격 증명 모음에서 하나 이상의 리소스에 대 한 백업을 구성한 후에는 아래에 설명 된 대로 blob 및 큐에 필요한 DNS 레코드를 추가 합니다.

1. 리소스 그룹으로 이동 하 여 만든 개인 끝점을 검색 합니다.
1. 사용자가 지정한 개인 끝점 이름 외에도 두 개의 개인 끝점을 만들 수 있습니다. 및로 시작 하 `<the name of the private endpoint>_ecs` 고 각각 및가 접미사로 사용 됩니다 `_blob` `_queue` .

    ![개인 끝점 리소스](./media/private-endpoints/private-endpoint-resources.png)

1. 이러한 각 개인 끝점으로 이동 합니다. 두 개인 끝점 각각에 대 한 DNS 구성 옵션에서 및 FQDN 및 IP 주소를 사용 하는 레코드를 볼 수 있습니다. 앞에서 설명한 것 외에도 둘 다를 사용자 지정 DNS 서버에 추가 합니다.
호스트 파일을 사용 하는 경우 다음 형식에 따라 각 IP/FQDN의 호스트 파일에 해당 항목을 만듭니다.

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![Blob DNS 구성](./media/private-endpoints/blob-dns-configuration.png)

위의 항목 외에도 첫 번째 백업 후에 다른 항목이 필요 합니다 .이에 대해서는 [나중에](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)설명 합니다.

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Azure VM에서 워크 로드의 백업 및 복원 (SQL 및 SAP HANA)

개인 끝점을 만들고 승인한 후에는 클라이언트 쪽에서 개인 끝점을 사용 하는 데 다른 변경이 필요 하지 않습니다 (이 섹션의 뒷부분에서 설명 하는 SQL 가용성 그룹을 사용 하지 않는 경우). 보안 네트워크에서 자격 증명 모음으로의 모든 통신 및 데이터 전송은 개인 끝점을 통해 수행 됩니다. 그러나 서버 (SQL 또는 SAP HANA)를 등록 한 후에 자격 증명 모음에 대 한 개인 끝점을 제거 하는 경우 컨테이너를 자격 증명 모음에 다시 등록 해야 합니다. 보호를 중지할 필요가 없습니다.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>첫 번째 백업 후 blob에 대 한 DNS 레코드 (사용자 지정 DNS 서버/호스트 파일에만 해당)

첫 번째 백업을 실행 하 고 사용자 지정 DNS 서버를 사용 하는 경우 (조건부 전달 제외) 백업이 실패할 수 있습니다. 이 경우 다음을 수행 합니다.

1. 리소스 그룹으로 이동 하 여 만든 개인 끝점을 검색 합니다.
1. 앞에서 설명한 세 개의 개인 끝점 외에도 이제 이름이로 시작 하는 네 번째 개인 끝점이 표시 `<the name of the private endpoint>_prot` 되 고 접미사가 붙습니다 `_blob` .

    !["Prot.13" 접미사를 사용 하는 개인 endpoing](./media/private-endpoints/private-endpoint-prot.png)

1. 이 새 개인 끝점으로 이동 합니다. DNS 구성 옵션에서 FQDN 및 IP 주소를 포함 하는 레코드를 볼 수 있습니다. 앞에서 설명한 것 외에도 이러한 서버를 개인 DNS 서버에 추가 합니다.

    호스트 파일을 사용 하는 경우 다음 형식에 따라 각 IP 및 FQDN에 대 한 호스트 파일에 해당 항목을 만듭니다.

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>이 시점에서, 자격 증명 모음 백업 및 저장소 Url에서 작업을 수행 하면 VM에서 **nslookup** 을 실행 하 고 개인 IP 주소를 확인할 수 있습니다.

### <a name="when-using-sql-availability-groups"></a>SQL 가용성 그룹을 사용 하는 경우

AG (SQL 가용성 그룹)를 사용 하는 경우 아래에 설명 된 대로 사용자 지정 AG DNS에서 조건부 전달을 프로 비전 해야 합니다.

1. 도메인 컨트롤러에 로그인 합니다.
1. DNS 응용 프로그램에서 필요에 따라 호스트 IP 168.63.129.16 또는 사용자 지정 DNS 서버 IP 주소에 세 개의 DNS 영역 (백업, Blob 및 큐)에 대 한 조건부 전달자를 추가 합니다. 다음 스크린샷에는 Azure 호스트 IP로 전달 하는 경우를 보여 줍니다. 자체 DNS 서버를 사용 하는 경우을 DNS 서버의 IP로 바꿉니다.

    ![DNS 관리자의 조건부 전달자](./media/private-endpoints/dns-manager.png)

    ![새 조건부 전달자](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>MARS 에이전트를 통해 백업 및 복원

MARS 에이전트를 사용 하 여 온-프레미스 리소스를 백업 하는 경우 온-프레미스 네트워크 (백업할 리소스 포함)가 자격 증명 모음에 대 한 개인 끝점을 포함 하는 Azure VNet과 피어 링 하 여 사용할 수 있도록 해야 합니다. 그러면 MARS 에이전트를 계속 설치 하 고 여기에 설명 된 대로 백업을 구성할 수 있습니다. 그러나 백업에 대 한 모든 통신은 피어 링 네트워크를 통해 수행 되어야 합니다.

하지만 MARS 에이전트가 등록 된 후 자격 증명 모음에 대 한 개인 끝점을 제거 하는 경우 컨테이너를 자격 증명 모음에 다시 등록 해야 합니다. 보호를 중지할 필요가 없습니다.

## <a name="additional-topics"></a>추가 항목

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음 만들기

Azure Resource Manager 클라이언트를 사용 하 여 Recovery Services 자격 증명 모음을 만들고 관리 되는 id를 사용 하도록 설정할 수 있습니다 (나중에 표시 되는 대로 관리 되는 id를 사용 하도록 설정 해야 함). 이 작업을 수행 하는 샘플은 아래와 같습니다.

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

위의 JSON 파일에는 다음 내용이 있어야 합니다.

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
>이 예에서는 Azure Resource Manager 클라이언트를 통해 만든 자격 증명 모음이 이미 시스템 할당 관리 id로 생성 되었습니다.

### <a name="managing-permissions-on-resource-groups"></a>리소스 그룹에 대 한 사용 권한 관리

자격 증명 모음에 대 한 관리 되는 Id에는 개인 끝점이 생성 될 가상 네트워크 및 리소스 그룹에 대 한 다음 권한이 있어야 합니다.

- `Microsoft.Network/privateEndpoints/*` 리소스 그룹의 전용 끝점에 대 한 CRUD를 수행 하는 데 필요 합니다. 리소스 그룹에 할당 되어야 합니다.
- `Microsoft.Network/virtualNetworks/subnets/join/action` 개인 IP가 개인 끝점과 연결 되는 가상 네트워크에 필요 합니다.
- `Microsoft.Network/networkInterfaces/read` 이는 전용 끝점에 대해 생성 된 네트워크 인터페이스를 가져오기 위해 리소스 그룹에 필요 합니다.
- 영역 참가자 역할 사설 DNS이 역할은 이미 존재 하며 `Microsoft.Network/privateDnsZones/A/*` 및 사용 권한을 제공 하는 데 사용할 수 있습니다 `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` .

다음 방법 중 하나를 사용 하 여 필요한 권한이 있는 역할을 만들 수 있습니다.

#### <a name="create-roles-and-permissions-manually"></a>수동으로 역할 및 사용 권한 만들기

다음 JSON 파일을 만들고 섹션의 끝에 있는 PowerShell 명령을 사용 하 여 역할을 만듭니다.

PrivateEndpointContributorRoleDef.js

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

NetworkInterfaceReaderRoleDef.js

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

PrivateEndpointSubnetContributorRoleDef.js

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

1. Azure Portal에서 **Cloud Shell** 를 시작 하 고 PowerShell 창에서 **파일 업로드** 를 선택 합니다.

    ![PowerShell 창에서 파일 업로드를 선택 합니다.](./media/private-endpoints/upload-file-in-powershell.png)

1. 다음 스크립트를 업로드 합니다. [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. 홈 폴더로 이동 합니다 (예:). `cd /home/user`

1. 다음 스크립트를 실행합니다.

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    매개 변수는 다음과 같습니다.

    - **구독**: * * SubscriptionId 자격 증명 모음에 대 한 개인 끝점을 만들 리소스 그룹 및 자격 증명 모음의 개인 끝점이 연결 되는 서브넷

    - **vaultPEResourceGroup**: 자격 증명 모음에 대 한 개인 끝점을 만들 리소스 그룹입니다.

    - **vaultPESubnetResourceGroup**: 개인 끝점이 가입 될 서브넷의 리소스 그룹입니다.

    - **vaultMsiName**: **VaultName** 와 동일한 자격 증명 모음 MSI의 이름

1. 인증을 완료 하면 스크립트가 위에 제공 된 지정 된 구독의 컨텍스트를 사용 합니다. 테 넌 트에서 누락 된 경우 적절 한 역할을 만들고 자격 증명 모음 MSI에 역할을 할당 합니다.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 개인 끝점 만들기

#### <a name="auto-approved-private-endpoints"></a>자동 승인 된 개인 끝점

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

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Azure Resource Manager 클라이언트를 사용 하 여 개인 끝점의 수동 승인

1. **Getvault** 를 사용 하 여 개인 끝점에 대 한 개인 끝점 연결 ID를 가져옵니다.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    그러면 개인 끝점 연결 ID가 반환 됩니다. 연결 ID의 첫 번째 부분을 사용 하 여 연결 이름을 다음과 같이 검색할 수 있습니다.

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. 아래 샘플과 같이 **개인 끝점 연결 ID** (및 필요한 경우 **개인 끝점 이름**)를 응답에서 가져온 후 다음 JSON 및 Azure Resource Manager URI로 바꾸고 상태를 "승인 됨/거부 됨/연결 끊김"으로 변경 해 보세요.

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

17. 기존 백업 자격 증명 모음에 대 한 개인 끝점을 만들 수 있나요?<br>
A. 아니요. 개인 끝점은 새 백업 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 보호 된 항목이 없어야 합니다. 실제로 개인 끝점을 만들기 전에 자격 증명 모음에 대 한 항목을 보호 하려는 시도는 없습니다.

17. 자격 증명 모음에 대 한 항목을 보호 하려고 했지만 실패 했으며 자격 증명 모음에 보호 된 항목이 아직 포함 되어 있지 않습니다. 이 자격 증명 모음에 대 한 개인 끝점을 만들 수 있나요?<br>
A. 아니요, 자격 증명 모음에서 과거에 항목을 보호 하려고 시도 하지 않아야 합니다.

17. 백업 및 복원을 위해 개인 끝점을 사용 하는 자격 증명 모음이 있습니다. 백업 항목이 보호 된 경우에도 나중에이 자격 증명 모음에 대 한 개인 끝점을 추가 하거나 제거할 수 있나요?<br>
A. 예. 자격 증명 모음 및 보호 된 백업 항목에 대 한 개인 끝점을 이미 만든 경우 나중에 필요에 따라 개인 끝점을 추가 하거나 제거할 수 있습니다.

17. Azure Backup에 대 한 개인 끝점을 Azure Site Recovery에도 사용할 수 있나요?<br>
A. 아니요, 백업용 개인 끝점은 Azure Backup에만 사용할 수 있습니다. 서비스에서 지 원하는 경우 Azure Site Recovery에 대 한 새 개인 끝점을 만들어야 합니다.

17. 이 문서의 단계 중 하나가 누락 되 고 내 데이터 원본을 보호 하는 데 문제가 발생 했습니다. 여전히 개인 끝점을 사용할 수 있나요?<br>
A. 이 문서의 단계를 따르지 않고 항목 보호를 계속 하면 자격 증명 모음이 개인 끝점을 사용할 수 없게 될 수 있습니다. 따라서 항목 보호를 계속 하기 전에이 검사 목록을 참조 하는 것이 좋습니다.

17. Azure 개인 DNS 영역 또는 통합 된 사설 DNS 영역을 사용 하는 대신 자체 DNS 서버를 사용할 수 있나요?<br>
A. 예, 사용자의 DNS 서버를 사용할 수 있습니다. 그러나이 섹션에서는 필요한 모든 DNS 레코드가 제안 된 대로 추가 되었는지 확인 합니다.

17. 이 문서의 프로세스를 수행한 후 서버에서 추가 단계를 수행 해야 하나요?<br>
A. 이 문서에 자세히 설명 된 프로세스를 수행한 후에는 백업 및 복원에 개인 끝점을 사용 하기 위해 추가 작업을 수행할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup의 모든 보안 기능](security-overview.md) 에 대해 읽기