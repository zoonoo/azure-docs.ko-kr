---
title: Azure Site Recovery에서 프라이빗 엔드포인트에 대한 복제 사용
description: 이 문서에서는 Site Recovery를 사용하여 Azure 지역 간에 프라이빗 엔드포인트를 사용하는 VM에 대해 복제를 구성하는 방법을 설명합니다.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629830"
---
# <a name="replicate-machines-with-private-endpoints"></a>프라이빗 엔드포인트를 사용하여 머신 복제

Azure Site Recovery를 사용하면 격리된 가상 네트워크 내에서 머신을 복제하기 위해 [Azure Private Link](../private-link/private-endpoint-overview.md) 프라이빗 엔드포인트를 사용할 수 있습니다. 복구 자격 증명 모음에 대한 프라이빗 엔드포인트 액세스는 모든 Azure 상용 및 정부 지역에서 지원됩니다.

이 문서에서는 다음 단계를 수행하는 데 필요한 지침을 제공합니다.

- 머신을 보호하기 위해 Azure Backup Recovery Services 자격 증명 모음을 만듭니다.
- 자격 증명 모음에 대해 관리 ID를 사용하도록 설정하고, 원본에서 대상 위치로 트래픽을 복제하기 위해 고객 스토리지 계정에 액세스하는 데 필요한 권한을 부여합니다. 스토리지에 대한 관리 ID 액세스는 자격 증명 모음에 대한 프라이빗 링크 액세스를 설정하는 경우에 필요합니다.
- 프라이빗 엔드포인트에 대해 DNS 변경 필요
- 가상 네트워크 내에서 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기 및 승인
- 스토리지 계정에 대한 프라이빗 엔드포인트를 만듭니다. 필요에 따라 스토리지에 대한 퍼블릭 또는 방화벽 적용 액세스를 계속 허용할 수 있습니다. Azure Site Recovery에서 스토리지에 액세스하기 위해 프라이빗 엔드포인트를 반드시 만들 필요는 없습니다.
  
다음은 복제 워크플로가 프라이빗 엔드포인트에 따라 변경되는 방식에 대한 참조 아키텍처입니다.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="프라이빗 엔드포인트를 사용하는 Site Recovery에 대한 참조 아키텍처입니다.":::

## <a name="prerequisites-and-caveats"></a>필수 구성 요소 및 주의 사항

- 프라이빗 엔드포인트는 자격 증명 모음에 등록된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 **자격 증명 모음에 항목을 추가하기 전에** 프라이빗 엔드포인트를 만들어야 합니다. [프라이빗 엔드포인트](https://azure.microsoft.com/pricing/details/private-link/)에 대한 가격 책정 구조를 검토합니다.
- 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 자격 증명 모음은 잠기며 **프라이빗 엔드포인트가 있는 네트워크 이외의 네트워크에서 액세스할 수 없습니다**.
- 현재 Azure Active Directory는 프라이빗 엔드포인트를 지원하지 않습니다. 따라서 Azure Active Directory가 지역에서 작동하는 데 필요한 IP 및 정규화된 도메인 이름은 보안 네트워크에서의 아웃바운드 액세스를 허용해야 합니다. 해당하는 경우 네트워크 보안 그룹 태그 "Azure Active Directory" 및 Azure Firewall 태그를 사용하여 Azure Active Directory에 대한 액세스를 허용할 수도 있습니다.
- 원본 머신과 복구 머신 모두의 서브넷에는 **7개 이상의 IP 주소가 필요** 합니다. 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 Site Recovery는 마이크로 서비스에 액세스하기 위해 5개의 프라이빗 링크를 만듭니다. 또한 복제를 사용하도록 설정하면 원본 및 대상 지역 연결을 위해 두 개의 추가 프라이빗 링크가 추가됩니다.
- 원본 및 복구 서브넷 모두에 **하나의 추가 IP 주소가 필요** 합니다. 이 IP 주소는 프라이빗 엔드포인트를 사용하여 캐시 스토리지 계정에 연결해야 하는 경우에만 필요합니다.
  스토리지에 대한 프라이빗 엔드포인트는 범용 v2 형식에서만 만들 수 있습니다. [GPv2의 데이터 전송](https://azure.microsoft.com/pricing/details/storage/page-blobs/)에 대한 가격 책정 구조를 검토합니다.

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Site Recovery에 대한 프라이빗 엔드포인트 만들기 및 사용

 이 섹션에서는 가상 네트워크 내에서 Azure Site Recovery에 대한 프라이빗 엔드포인트를 만들고 사용하는 단계에 대해 설명합니다.

> [!NOTE]
> 제공된 것과 동일한 순서로 이러한 단계를 수행하는 것이 좋습니다. 그렇게 하지 않으면 자격 증명 모음이 프라이빗 엔드포인트를 사용할 수 없게 되어 새 자격 증명 모음으로 프로세스를 다시 시작해야 하는 상황이 될 수 있습니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 머신의 복제 정보를 포함하는 엔터티로, Site Recovery 작업을 트리거하는 데 사용됩니다. 자세한 내용은 [Recovery Services 자격 증명 모음 만들기](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)를 참조하세요.

## <a name="enable-the-managed-identity-for-the-vault"></a>자격 증명 모음에 관리 ID를 사용하도록 설정합니다.

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 자격 증명 모음에서 고객의 스토리지 계정에 액세스할 수 있습니다. 시나리오 요구 사항에 따라 Site Recovery에서는 원본 스토리지, 대상 스토리지 및 캐시/로그 스토리지 계정에 액세스해야 합니다.
관리 ID 액세스는 자격 증명 모음에 프라이빗 링크 서비스를 사용하는 경우에 필요합니다.

1. Recovery Services 자격 증명 모음으로 이동합니다. _설정_ 에서 **ID** 를 선택합니다.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Azure Portal 및 Recovery Services 페이지를 표시합니다.":::

1. **상태** 를 _켜기_ 로 변경하고 **저장** 을 선택합니다.

1. 자격 증명 모음이 이제 Azure Active Directory에 등록되었음을 나타내는 **개체 ID** 가 생성됩니다.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기

Azure 가상 머신에 대한 장애 조치(failover) 및 장애 복구(failback)를 모두 사용하도록 설정하려면 자격 증명 모음에 대해 두 개의 프라이빗 엔드포인트가 필요합니다. 원본 네트워크에 있는 머신의 보호를 위한 하나의 프라이빗 엔드포인트와 복구 네트워크에서 장애 조치된 머신을 다시 보호하기 위한 또 다른 프라이빗 엔드포인트가 있습니다.

이 설정 프로세스 중에도 대상 지역에 복구 가상 네트워크를 만들어야 합니다.

포털의 Private Link 센터 또는 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)을 사용하여 원본 가상 네트워크 내에 자격 증명 모음에 대한 첫 번째 프라이빗 엔드포인트를 만듭니다. 복구 네트워크 내에 자격 증명 모음에 대한 두 번째 프라이빗 엔드포인트를 만듭니다. 원본 네트워크에서 프라이빗 엔드포인트를 만드는 단계는 다음과 같습니다. 동일한 지침을 반복하여 두 번째 프라이빗 엔드포인트를 만듭니다.

1. Azure Portal 검색 창에서 "Private Link"를 검색하고 선택합니다. 이 작업을 수행하면 Private Link 센터로 이동됩니다.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Azure Portal에서 Private Link 센터를 검색하는 방법을 표시합니다.":::

1. 왼쪽 탐색 모음에서 **프라이빗 엔드포인트** 를 선택합니다. 프라이빗 엔드포인트 페이지에서 **\+추가** 를 선택하여 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기를 시작합니다.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Private Link 센터에서 프라이빗 엔드포인트를 만드는 방법을 표시합니다.":::

1. "프라이빗 엔드포인트 만들기" 환경에서 프라이빗 엔드포인트 연결을 만드는 방법에 대한 세부 정보를 지정해야 합니다.

   1. **기본 사항**: 프라이빗 엔드포인트에 대한 기본 세부 정보를 입력합니다. 지역은 원본 머신과 동일해야 합니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Azure Portal에서 프라이빗 엔드포인트를 만들기 위한 기본 탭, 프로젝트 세부 정보, 구독 및 기타 관련 필드를 표시합니다.":::

   1. **리소스**: 이 탭을 사용하려면 연결을 만들 PaaS(Platform as a Service) 리소스를 지정해야 합니다. 선택한 구독에 대한 **리소스 종류** 에서 _Microsoft.RecoveryServices/vaults_ 를 선택합니다. 그런 다음, **리소스** 에 대한 Recovery Services 자격 증명 모음의 이름을 선택하고 _Azure Site Recovery_ 를 **대상 하위 리소스** 로 설정합니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Azure Portal의 프라이빗 엔드포인트에 연결하기 위한 리소스 탭, 리소스 종류, 리소스 및 대상 하위 리소스 필드를 표시합니다.":::

   1. **구성**: 구성에서 프라이빗 엔드포인트를 만들 가상 네트워크 및 서브넷을 지정합니다. 이 가상 네트워크는 가상 머신이 있는 네트워크입니다. **예** 를 선택하여 프라이빗 DNS 영역과의 통합을 사용하도록 설정합니다. 이미 생성된 DNS 영역을 선택하거나 새로 만듭니다. **예** 를 선택하면 자동으로 영역을 원본 가상 네트워크에 연결하고, 프라이빗 엔드포인트에 대해 만든 새 IP 및 정규화된 도메인 이름의 DNS 확인에 필요한 DNS 레코드를 추가합니다.

      동일한 자격 증명 모음에 연결하는 모든 새 프라이빗 엔드포인트에 대한 새 DNS 영역을 만들도록 선택해야 합니다. 기존 프라이빗 DNS 영역을 선택하는 경우 이전 CNAME 레코드를 덮어씁니다. 계속하기 전에 [프라이빗 엔드포인트 지침](../private-link/private-endpoint-overview.md#private-endpoint-properties)을 참조하세요.

      환경에서 허브 및 스포크 모델을 사용하는 경우 모든 가상 네트워크 간에 피어링을 사용하도록 설정했으므로 전체 설정에 대해 하나의 프라이빗 엔드포인트와 하나의 프라이빗 DNS 영역만 있으면 됩니다. 자세한 내용은 [프라이빗 엔드포인트 DNS 통합](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)을 참조하세요.

      프라이빗 DNS 영역을 수동으로 만들려면 [수동으로 프라이빗 DNS 영역 만들기 및 DNS 레코드 추가](#create-private-dns-zones-and-add-dns-records-manually)의 단계를 따르세요.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Azure Portal에서 프라이빗 엔드포인트를 구성하는 데 사용할 네트워킹 및 DNS 통합 필드가 있는 구성 탭을 표시합니다.":::

   1. **태그**: 필요에 따라 프라이빗 엔드포인트에 대한 태그를 추가할 수 있습니다.

   1. **검토 \+ 만들기**: 유효성 검사가 완료되면 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다.

프라이빗 엔드포인트를 만들 때 5개의 정규화된 도메인 이름이 프라이빗 엔드포인트에 추가됩니다. 이러한 링크를 통해 가상 네트워크의 머신은 자격 증명 모음 컨텍스트에서 필요한 모든 Site Recovery 마이크로 서비스에 액세스할 수 있습니다. 나중에 복제를 사용하도록 설정하면 동일한 프라이빗 엔드포인트에 두 개의 정규화된 도메인 이름이 추가됩니다.

5개의 도메인 이름은 다음과 같은 패턴으로 형식이 지정됩니다.

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Site Recovery를 위한 프라이빗 엔드포인트 승인

프라이빗 엔드포인트를 만드는 사용자가 Recovery Services 자격 증명 모음의 소유자이기도 한 경우, 위에서 만든 프라이빗 엔드포인트는 몇 분 안에 자동으로 승인됩니다. 그렇지 않으면 자격 증명 모음 소유자가 프라이빗 엔드포인트를 승인해야 해당 엔드포인트를 사용할 수 있습니다. 요청된 프라이빗 엔드포인트 연결을 승인하거나 거부하려면 복구 자격 증명 모음 페이지의 "설정"에서 **프라이빗 엔드포인트 연결** 로 이동합니다.

계속하기 전에 프라이빗 엔드포인트 리소스로 이동하여 연결 상태를 검토할 수 있습니다.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Azure Portal에 있는 자격 증명 모음의 프라이빗 엔드포인트 연결 페이지와 연결 목록을 표시합니다.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(선택 사항) 캐시 스토리지 계정에 대한 프라이빗 엔드포인트 만들기

Azure Storage에 대한 프라이빗 엔드포인트를 사용할 수 있습니다. Azure Site Recovery 복제를 위해 스토리지 액세스에 대한 프라이빗 엔드포인트를 만드는 것은 _선택 사항_ 입니다. 스토리지에 대한 프라이빗 엔드포인트를 만들 때 다음 요구 사항이 적용됩니다.

- 원본 가상 네트워크에 캐시/로그 스토리지 계정에 대한 프라이빗 엔드포인트가 필요합니다.
- 복구 네트워크에서 장애 조치된 머신을 다시 보호할 때 두 번째 프라이빗 엔드포인트가 필요합니다. 이 프라이빗 엔드포인트는 대상 지역에서 만든 새 스토리지 계정에 대한 것입니다.

> [!NOTE]
> 스토리지 계정에서 프라이빗 엔드포인트를 사용하도록 설정하지 않은 경우에도 성공적으로 보호됩니다. 그러나 복제 트래픽은 Azure Site Recovery 퍼블릭 엔드포인트로 전송됩니다. 복제 트래픽이 프라이빗 링크를 통해 전달되도록 하려면 프라이빗 엔드포인트에서 스토리지 계정을 사용하도록 설정해야 합니다.

> [!NOTE]
> **범용 v2** 스토리지 계정에서만 스토리지에 대한 프라이빗 엔드포인트를 만들 수 있습니다. 가격 책정에 대한 내용은 [표준 페이지 Blob 가격](https://azure.microsoft.com/pricing/details/storage/page-blobs/)을 참조하세요.

[프라이빗 스토리지 만들기에 대한 지침](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)에 따라 프라이빗 엔드포인트를 사용하여 스토리지 계정을 만듭니다. 프라이빗 DNS 영역과 통합하려면 **예** 를 선택합니다. 이미 생성된 DNS 영역을 선택하거나 새로 만듭니다.

## <a name="grant-required-permissions-to-the-vault"></a>자격 증명 모음에 필요한 권한 부여

가상 머신에서 Managed Disks를 사용하는 경우 캐시 스토리지 계정에만 관리 ID 권한을 부여하면 됩니다. 가상 머신에서 비관리 디스크를 사용하는 경우 원본, 캐시 및 대상 스토리지 계정에 대해 관리 ID 권한을 부여해야 합니다. 이 경우 대상 스토리지 계정을 미리 만들어야 합니다.

가상 머신의 복제를 사용하도록 설정하기 전에 스토리지 계정 유형에 따라 자격 증명 모음의 관리 ID에 다음 역할 권한이 있어야 합니다.

- Resource Manager 기반 스토리지 계정(표준 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager 기반 스토리지 계정(프리미엄 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 소유자](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 클래식 스토리지 계정:
  - [클래식 Storage 계정 기여자](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [클래식 스토리지 계정 키 운영자 서비스 역할](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

다음 단계에서는 스토리지 계정에 역할 할당을 한 번에 하나씩 추가하는 방법을 설명합니다.

1. 스토리지 계정으로 이동하여 페이지의 왼쪽에 있는 **액세스 제어(IAM)** 로 이동합니다.

1. **액세스 제어(IAM)** 의 "역할 할당 추가" 상자에서 **추가** 를 선택합니다.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="스토리지 계정에 대한 액세스 제어(IAM) 페이지와 Azure Portal의 '역할 할당 추가' 단추를 표시합니다.":::

1. "역할 할당 추가" 측면 페이지의 **역할** 드롭다운에서 위의 목록의 역할을 선택합니다. 자격 증명 모음의 **이름** 을 입력하고 **저장** 을 선택합니다.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Azure Portal에서 역할 및 해당 역할을 부여할 보안 주체를 선택하는 옵션과 스토리지 계정에 대한 액세스 제어(IAM) 페이지를 표시합니다.":::

이러한 권한 외에도 MS에서 신뢰할 수 있는 서비스도 액세스를 허용해야 합니다. "방화벽 및 가상 네트워크"로 이동하고 **예외** 에서 "신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용" 확인란을 선택합니다.

## <a name="protect-your-virtual-machines"></a>가상 머신 보호

위의 모든 구성이 완료되면 계속해서 가상 머신에 대한 복제를 사용하도록 설정합니다. 자격 증명 모음에서 프라이빗 엔드포인트를 만드는 동안 DNS 통합을 사용하면 모든 Site Recovery 작업이 추가 단계 없이 작동합니다. 그러나 DNS 영역을 수동으로 만들고 구성하는 경우 복제를 사용하도록 설정한 후 원본 및 대상 DNS 영역에서 특정 DNS 레코드를 추가하기 위한 추가 단계가 필요합니다. 자세한 내용과 단계는 [수동으로 프라이빗 DNS 영역 만들기 및 DNS 레코드 추가](#create-private-dns-zones-and-add-dns-records-manually)를 참조하세요.

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>수동으로 프라이빗 DNS 영역 만들기 및 DNS 레코드 추가

자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 프라이빗 DNS 영역과 통합하는 옵션을 선택하지 않은 경우 이 섹션의 단계를 따릅니다.

모바일 에이전트가 프라이빗 링크 정규화된 도메인 이름을 개인 IP로 확인할 수 있도록 하는 프라이빗 DNS 영역을 하나 만듭니다.

1. 프라이빗 DNS 영역 만들기

   1. **모든 서비스** 검색 표시줄에서 "프라이빗 DNS 영역"을 검색하고 드롭다운에서 "프라이빗 DNS 영역"을 선택합니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure Portal의 새 리소스 페이지에서 '프라이빗 DNS 영역'을 검색하는 방법을 표시합니다.":::

   1. "프라이빗 DNS 영역" 페이지에서 **\+추가** 단추를 선택하여 새 영역 만들기를 시작합니다.

   1. "프라이빗 DNS 영역 만들기" 페이지에서 필요한 세부 정보를 입력합니다. 프라이빗 DNS 영역 이름을 `privatelink.siterecovery.windowsazure.com`으로 입력합니다. 영역을 만들 임의 리소스 그룹 및 구독을 선택할 수 있습니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Azure Portal에서 프라이빗 DNS 영역 만들기 페이지의 기본 사항 탭 및 관련 프로젝트 세부 정보를 표시합니다.":::

   1. **검토 \+ 만들기** 탭으로 이동하여 DNS 영역을 검토하고 만듭니다.

1. 가상 네트워크에 프라이빗 DNS 영역 연결

   위에서 만든 프라이빗 DNS 영역은 현재 서버가 있는 가상 네트워크에 연결되어야 합니다. 또한 프라이빗 DNS 영역을 대상 가상 네트워크에 미리 연결해야 합니다.

   1. 이전 단계에서 만든 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 **가상 네트워크 링크** 로 이동합니다. 그런 다음, **\+추가** 단추를 선택합니다.

   1. 필수 세부 정보를 입력합니다. **구독** 및 **가상 네트워크** 필드는 서버가 있는 가상 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 그대로 유지해야 합니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Azure Portal에서 링크 이름, 구독 및 관련 가상 네트워크를 사용하여 가상 네트워크 링크를 추가하는 페이지를 표시합니다.":::

1. DNS 레코드 추가

   필요한 프라이빗 DNS 영역 및 프라이빗 엔드포인트를 만들었으면 DNS 영역에 DNS 레코드를 추가해야 합니다.

   > [!NOTE]
   > 사용자 지정 프라이빗 DNS 영역을 사용하는 경우 아래에 설명된 대로 유사한 항목이 생성되었는지 확인합니다.

   이 단계에서는 프라이빗 DNS 영역에 프라이빗 엔드포인트의 각 정규화된 도메인 이름에 대한 항목을 만들어야 합니다.

   1. 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 **개요** 섹션으로 이동합니다. 여기에서 **\+레코드 집합** 을 선택하여 레코드 추가를 시작합니다.

   1. 열리는 "레코드 집합 추가" 페이지에서 각 정규화된 도메인 이름 및 개인 IP 항목을 _A_ 형식 레코드로 추가합니다. 정규화된 도메인 이름 및 IP 목록은 **개요** 의 "프라이빗 엔드포인트" 페이지에서 가져올 수 있습니다. 아래 예제에 표시된 것처럼 프라이빗 엔드포인트의 첫 번째 정규화된 도메인 이름이 프라이빗 DNS 영역의 레코드 집합에 추가됩니다.

      이러한 정규화된 도메인 이름은 `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com` 패턴과 일치합니다.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="정규화된 도메인 이름에 대한 DNS A 형식 레코드를 Azure Portal의 프라이빗 엔드포인트에 추가하는 페이지를 표시합니다.":::

   > [!NOTE]
   > 복제를 사용하도록 설정하면 두 지역의 프라이빗 엔드포인트에 정규화된 도메인 이름이 두 개 이상 만들어집니다. 새로 만든 정규화된 도메인 이름에 대한 DNS 레코드를 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

가상 머신 복제를 위한 프라이빗 엔드포인트를 사용하도록 설정했으므로 다음 페이지에서 추가 및 관련 정보를 참조하세요.

- [Azure VM을 다른 Azure 지역에 복제](./azure-to-azure-how-to-enable-replication.md)
- [자습서: Azure VM에 대한 재해 복구 설정](./azure-to-azure-tutorial-enable-replication.md)