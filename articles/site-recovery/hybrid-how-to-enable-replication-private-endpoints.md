---
title: Azure Site Recovery에서 전용 끝점을 사용 하 여 온-프레미스 컴퓨터에 대 한 복제를 사용 하도록 설정
description: 이 문서에서는 Site Recovery를 사용 하 여 개인 끝점이 있는 온-프레미스 컴퓨터에 대 한 복제를 구성 하는 방법을 설명 합니다.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: c91c92a18570f569b6364b646e6fdf7bf534802f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098828"
---
# <a name="replicate-on-premises-machines-with-private-endpoints"></a>개인 끝점을 사용 하 여 온-프레미스 컴퓨터 복제

Azure Site Recovery를 사용 하면 azure [개인 링크](../private-link/private-endpoint-overview.md) 개인 끝점을 사용 하 여 온-프레미스 컴퓨터를 azure의 가상 네트워크에 복제할 수 있습니다. 복구 자격 증명 모음에 대 한 개인 끝점 액세스 지원은 다음 지역에서 지원 됩니다.

- Azure 상용: 미국 동부, 미국 서 부 2, 미국 동부
- Azure Government: US Gov 버지니아, US Gov 애리조나, US Gov 텍사스, US DoD 동부, US DoD 중부

이 문서에서는 다음 단계를 수행 하는 데 필요한 지침을 제공 합니다.

- 컴퓨터를 보호 하기 위해 Azure Backup Recovery Services 자격 증명 모음을 만듭니다.
- 자격 증명 모음에 관리 되는 id를 사용 하도록 설정 하 고 고객 저장소 계정에 액세스 하는 데 필요한 권한을 부여 하 여 온-프레미스에서 Azure 대상 위치로 트래픽을 복제 합니다. 저장소에 대 한 관리 id 액세스는 자격 증명 모음에 대 한 개인 링크 액세스를 설정 하는 경우에 필요 합니다.
- 개인 끝점에 대 한 DNS 변경 필요
- 가상 네트워크 내에서 자격 증명 모음에 대 한 개인 끝점 만들기 및 승인
- 저장소 계정에 대 한 개인 끝점을 만듭니다. 필요에 따라 저장소에 대 한 공용 또는 방화벽이 사용 액세스를 계속 허용할 수 있습니다. 저장소에 액세스 하기 위한 개인 끝점 생성은 Azure Site Recovery에 반드시 필요한 것은 아닙니다.
  
다음은 사설 끝점을 사용 하 여 하이브리드 재해 복구를 위해 복제 워크플로를 변경 하는 방법에 대 한 참조 아키텍처입니다. 온-프레미스 네트워크에서 개인 끝점을 만들 수 없습니다. 개인 링크를 사용 하려면 Azure 가상 네트워크 (이 문서에서는 "바이패스 네트워크")를 만들고, 온-프레미스와 바이패스 네트워크 간에 개인 연결을 설정한 다음, 바이패스 네트워크에서 개인 끝점을 만듭니다. 개인 연결을 선택 하는 것은 임의로 선택 합니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="전용 끝점을 사용 하는 Site Recovery에 대 한 참조 아키텍처입니다.":::

## <a name="prerequisites-and-caveats"></a>필수 조건 및 주의 사항

- **9.35** 버전 이상의 Site Recovery 인프라에서는 개인 링크에 대 한 지원이 사용 하도록 설정 되어 있습니다.
- 개인 끝점은 자격 증명 모음에 등록 된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 **자격 증명 모음에 항목을 추가 하기 전에**개인 끝점을 만들어야 합니다. [전용 끝점](https://azure.microsoft.com/pricing/details/private-link/)에 대 한 가격 책정 구조를 검토 합니다.
- 자격 증명 모음에 대 한 개인 끝점을 만들 때 자격 증명 모음은 잠기므로 **개인 끝점이 있는 네트워크 이외의 네트워크에서 액세스할 수 없습니다**.
- 현재 Azure Active Directory 개인 끝점을 지원 하지 않습니다. 따라서 지역에서 작업 하 Azure Active Directory 하는 데 필요한 Ip와 정규화 된 도메인 이름은 보안 된 Azure 가상 네트워크에서 아웃 바운드 액세스를 허용 해야 합니다. 네트워크 보안 그룹 태그 "Azure Active Directory" 및 Azure 방화벽 태그를 사용 하 여 해당 하는 경우 Azure Active Directory에 대 한 액세스를 허용할 수도 있습니다.
- 개인 끝점을 만드는 바이패스 네트워크에 **5 개의 IP 주소가 필요** 합니다. 자격 증명 모음에 대 한 개인 끝점을 만들 때 Site Recovery는 마이크로 서비스에 액세스 하기 위해 5 개의 개인 링크를 만듭니다.
- 캐시 저장소 계정에 대 한 개인 끝점 연결에 대 한 네트워크 바이패스 네트워크에서 **하나의 추가 IP 주소가 필요** 합니다. 온-프레미스와 저장소 계정 끝점 간의 연결 방법 (예: 인터넷 또는 [express](../expressroute/index.yml)경로)은 사용자의 설명에 있습니다. 개인 링크 설정은 선택 사항입니다. 저장소에 대 한 개인 끝점은 범용 v2 형식 에서만 만들 수 있습니다. [GPv2에서 데이터 전송](https://azure.microsoft.com/pricing/details/storage/page-blobs/)에 대 한 가격 책정 구조를 검토 합니다.

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Site Recovery에 대 한 전용 끝점 만들기 및 사용

 이 섹션에서는 가상 네트워크 내에서 Azure Site Recovery에 대 한 개인 끝점을 만들고 사용 하는 단계에 대해 설명 합니다.

> [!NOTE]
> 제공 된 것과 동일한 순서로 이러한 단계를 수행 하는 것이 좋습니다. 그렇게 하지 않으면 개인 끝점을 사용할 수 없으며 새 자격 증명 모음을 사용 하 여 프로세스를 다시 시작 해야 하는 자격 증명 모음이 렌더링 될 수 있습니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음을 만듭니다.

Recovery services 자격 증명 모음은 컴퓨터의 복제 정보를 포함 하는 엔터티가 며 Site Recovery 작업을 트리거하는 데 사용 됩니다. 재해 발생 시 장애 조치 (failover) 하려는 Azure 지역에 recovery services 자격 증명 모음을 만드는 단계는 [Recovery Services 자격 증명 모음 만들기](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)를 참조 하세요.

## <a name="enable-the-managed-identity-for-the-vault"></a>자격 증명 모음에 관리 되는 id를 사용 하도록 설정 합니다.

[관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하면 자격 증명 모음에서 고객의 저장소 계정에 액세스할 수 있습니다. Site Recovery는 시나리오 요구 사항에 따라 대상 저장소 및 캐시/로그 저장소 계정에 액세스 해야 합니다. 관리 id 액세스는 자격 증명 모음에 개인 링크 서비스를 사용 하는 경우에 필요 합니다.

1. Recovery Services 자격 증명 모음으로 이동 합니다. _설정_에서 **id** 를 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Azure Portal 및 Recovery Services 페이지를 표시 합니다.":::

1. **상태** 를 _켜기_ 로 변경 하 고 **저장**을 선택 합니다.

1. 자격 증명 모음이 이제 Azure Active Directory에 등록 되었음을 나타내는 **개체 ID** 가 생성 됩니다.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 대 한 개인 끝점 만들기

온-프레미스 원본 네트워크의 컴퓨터를 보호 하기 위해 바이패스 네트워크에 자격 증명 모음에 대 한 개인 끝점이 하나 필요 합니다. 포털에서 또는 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)를 통해 개인 링크 센터를 사용 하 여 개인 끝점을 만듭니다.

1. Azure Portal 검색 창에서 "개인 링크"를 검색 하 고 선택 합니다. 이 작업을 수행 하면 개인 링크 센터로 이동 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="개인 링크 센터에 대 한 Azure Portal 검색을 표시 합니다.":::

1. 왼쪽 탐색 모음에서 **전용 끝점**을 선택 합니다. 개인 끝점 페이지에서 ** \+ 추가** 를 선택 하 여 자격 증명 모음에 대 한 개인 끝점 만들기를 시작 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="개인 링크 센터에서 개인 끝점을 만드는 방법을 보여 줍니다.":::

1. "개인 끝점 만들기" 환경에서 개인 끝점 연결을 만드는 방법에 대 한 세부 정보를 지정 해야 합니다.

   1. **기본 사항**: 개인 끝점에 대 한 기본 세부 정보를 입력 합니다. 지역은 바이패스 네트워크와 동일 해야 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Azure Portal에서 개인 끝점을 만들기 위한 기본 탭, 프로젝트 세부 정보, 구독 및 기타 관련 필드를 표시 합니다.":::

   1. **리소스**:이 탭을 사용 하려면 연결을 만들 platform as a service 리소스를 언급 해야 합니다. 선택한 구독의 **리소스 유형에** 서 _Microsoft recoveryservices/자격 증명 모음_ 을 선택 합니다. 그런 다음 **리소스** 에 대 한 Recovery Services 자격 증명 모음의 이름을 선택 하 고 _Azure Site Recovery_ 를 **대상 하위 리소스로**설정 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Azure Portal의 전용 끝점에 연결 하기 위한 리소스 탭, 리소스 유형, 리소스 및 대상 하위 리소스 필드를 표시 합니다.":::

   1. **구성**: 구성에서 개인 끝점을 만들려는 바이패스 네트워크 및 서브넷을 지정 합니다. **예**를 선택 하 여 사설 DNS 영역과의 통합을 사용 하도록 설정 합니다.
      이미 생성 된 DNS 영역을 선택 하거나 새로 만듭니다. **예** 를 선택 하면 자동으로 해당 영역이 바이패스 네트워크에 연결 되 고 새 IP의 dns 확인에 필요한 dns 레코드와 개인 끝점에 대해 만든 정규화 된 도메인 이름이 추가 됩니다.

      동일한 자격 증명 모음에 연결 하는 모든 새 개인 끝점에 대 한 새 DNS 영역을 만들도록 선택 합니다. 기존 개인 DNS 영역을 선택 하는 경우 이전 CNAME 레코드를 덮어씁니다. 계속 하기 전에 [개인 끝점 지침](../private-link/private-endpoint-overview.md#private-endpoint-properties) 을 참조 하십시오.

      환경에서 허브 및 스포크 모델을 사용 하는 경우 모든 가상 네트워크 간에 피어 링이 사용 하도록 설정 되어 있기 때문에 전체 설치에 대해 하나의 개인 끝점과 하나의 개인 DNS 영역만 필요 합니다. 자세한 내용은 [개인 끝점 DNS 통합](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)을 참조 하세요.

      개인 DNS 영역을 수동으로 만들려면 [개인 dns 영역 만들기 및 수동으로 DNS 레코드 추가](#create-private-dns-zones-and-add-dns-records-manually)의 단계를 따르세요.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Azure Portal에서 개인 끝점을 구성 하는 데 사용할 네트워킹 및 DNS 통합 필드가 있는 구성 탭을 표시 합니다.":::

   1. **태그**: 선택적으로 개인 끝점에 대 한 태그를 추가할 수 있습니다.

   1. **검토 \+ 만들기**: 유효성 검사가 완료 되 면 **만들기** 를 선택 하 여 개인 끝점을 만듭니다.

개인 끝점이 만들어지면 정규화 된 5 개의 도메인 이름이 개인 끝점에 추가 됩니다. 이러한 링크를 통해 온-프레미스 네트워크의 컴퓨터는 자격 증명 모음 컨텍스트에서 필요한 모든 Site Recovery 마이크로 서비스에 대 한 바이패스 네트워크를 통해 액세스할 수 있습니다. 우회 네트워크 및 모든 피어 링 네트워크에서 모든 Azure 컴퓨터를 보호 하기 위해 동일한 개인 끝점을 사용할 수 있습니다.

5 개의 도메인 이름은 다음과 같은 패턴으로 포맷 됩니다.

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Site Recovery에 대 한 개인 끝점 승인

개인 끝점을 만드는 사용자가 Recovery Services 자격 증명 모음의 소유자 이기도 한 경우 위에서 만든 개인 끝점은 몇 분 내에 자동으로 승인 됩니다. 그렇지 않으면 자격 증명 모음 소유자는 사용 하기 전에 개인 끝점을 승인 해야 합니다. 요청 된 개인 끝점 연결을 승인 하거나 거부 하려면 복구 자격 증명 모음 페이지의 "설정"에서 **개인 끝점 연결** 로 이동 합니다.

계속 하기 전에 개인 끝점 리소스로 이동 하 여 연결 상태를 검토할 수 있습니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="자격 증명 모음의 개인 끝점 연결 페이지와 Azure Portal 연결 목록을 표시 합니다.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>필드 캐시 저장소 계정에 대 한 개인 끝점 만들기

Azure Storage에 대 한 개인 끝점을 사용할 수 있습니다. Azure Site Recovery 복제에 대 한 저장소 액세스를 위한 개인 끝점 만들기는 _선택 사항_ 입니다. 저장소에 대 한 개인 끝점을 만들 때 바이패스 가상 네트워크의 캐시/로그 저장소 계정에 대 한 개인 끝점이 필요 합니다.

> [!NOTE]
> 저장소에 대 한 개인 끝점은 범용 **v2** 저장소 계정에만 만들 수 있습니다. 가격 책정에 대 한 자세한 내용은 [표준 페이지 blob 가격](https://azure.microsoft.com/pricing/details/storage/page-blobs/)을 참조 하세요.

개인 [저장소 만들기에 대 한 지침](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) 에 따라 개인 끝점을 사용 하 여 저장소 계정을 만듭니다. 사설 DNS 영역과 통합 하려면 **예** 를 선택 합니다. 이미 생성 된 DNS 영역을 선택 하거나 새로 만듭니다.

## <a name="grant-required-permissions-to-the-vault"></a>자격 증명 모음에 필요한 권한 부여

설정에 따라 대상 Azure 지역에 하나 이상의 저장소 계정이 필요할 수 있습니다. 그런 다음 Site Recovery에 필요한 모든 캐시/로그 저장소 계정에 대 한 관리 id 권한을 부여 합니다. 이 경우 필요한 저장소 계정을 미리 만들어야 합니다.

가상 컴퓨터의 복제를 사용 하도록 설정 하기 전에 저장소 계정 유형에 따라 자격 증명 모음의 관리 id에 다음 역할 권한이 있어야 합니다.

- 리소스 관리자 기반 저장소 계정 (표준 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 리소스 관리자 기반 저장소 계정 (프리미엄 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 소유자](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 클래식 저장소 계정:
  - [클래식 Storage 계정 기여자](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [클래식 스토리지 계정 키 운영자 서비스 역할](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

다음 단계에서는 저장소 계정에 역할 할당을 한 번에 하나씩 추가 하는 방법에 대해 설명 합니다.

1. 저장소 계정으로 이동 하 여 페이지의 왼쪽에 있는 **액세스 제어 (IAM)** 로 이동 합니다.

1. **액세스 제어 (IAM)** 에서 "역할 할당 추가" 상자에 **추가**를 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="저장소 계정에 대 한 액세스 제어 (IAM) 페이지와 Azure Portal의 ' 역할 할당 추가 ' 단추를 표시 합니다.":::

1. "역할 할당 추가" 쪽 페이지의 **역할** 드롭다운에서 위의 목록에서 역할을 선택 합니다. 자격 증명 모음의 **이름을** 입력 하 고 **저장**을 선택 합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="저장소 계정에 대 한 액세스 제어 (IAM) 페이지 및 역할을 선택 하는 옵션과 Azure Portal에 해당 역할을 부여할 보안 주체를 표시 합니다.":::

이러한 권한 외에도 MS 신뢰할 수 있는 서비스도 액세스를 허용 해야 합니다. **예외**에서 "방화벽 및 가상 네트워크"로 이동 하 고 "신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용" 확인란을 선택 합니다.

## <a name="protect-your-virtual-machines"></a>가상 컴퓨터 보호

위의 모든 구성이 완료 되 면 온-프레미스 인프라의 설정을 계속 진행 합니다.

- [VMware 및 물리적 컴퓨터에 대 한 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- 또는 [복제를 위한 hyper-v 환경 설정](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

설치가 완료 되 면 원본 컴퓨터에 대 한 복제를 사용 하도록 설정 합니다. 자격 증명 모음에 대 한 개인 끝점을 바이패스 네트워크에서 이미 만든 후에만 인프라 설정이 수행 되도록 합니다.

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>개인 DNS 영역 만들기 및 수동으로 DNS 레코드 추가

자격 증명 모음에 대 한 개인 끝점을 만들 때 개인 DNS 영역과 통합 하는 옵션을 선택 하지 않은 경우이 섹션의 단계를 따릅니다.

Site Recovery 공급자 (Hyper-v 컴퓨터의 경우) 또는 프로세스 서버 (VMware/물리적 컴퓨터용)를 사용 하 여 개인 Ip에 대 한 개인 링크 정규화 된 도메인 이름을 확인할 수 있는 개인 DNS 영역을 하나 만듭니다.

1. 프라이빗 DNS 영역 만들기

   1. **모든 서비스** 검색 표시줄에서 "사설 DNS 영역"을 검색 하 고 드롭다운에서 "사설 DNS 영역"을 선택 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure Portal의 새 리소스 페이지에서 ' 개인 dns 영역 ' 검색을 표시 합니다.":::

   1. "사설 DNS 영역" 페이지에서 ** \+ 추가** 단추를 선택 하 여 새 영역 만들기를 시작 합니다.

   1. "개인 DNS 영역 만들기" 페이지에서 필요한 세부 정보를 입력 합니다. 개인 DNS 영역 이름을으로 입력 합니다 `privatelink.siterecovery.windowsazure.com` . 모든 리소스 그룹 및 구독을 선택 하 여 만들 수 있습니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Azure Portal에서 사설 DNS 영역 만들기 페이지의 기본 탭 및 관련 프로젝트 세부 정보를 표시 합니다.":::

   1. **검토 \+ 만들기** 탭으로 이동 하 여 DNS 영역을 검토 하 고 만듭니다.

1. 가상 네트워크에 개인 DNS 영역 연결

   위에서 만든 개인 DNS 영역을 무시에 연결 해야 합니다.

   1. 이전 단계에서 만든 개인 DNS 영역으로 이동 하 여 페이지의 왼쪽에 있는 **가상 네트워크 링크** 로 이동 합니다. 그런 다음 ** \+ 추가** 단추를 선택 합니다.

   1. 필요한 세부 정보를 입력 합니다. **구독** 및 **가상 네트워크** 필드는 바이패스 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 그대로 유지 해야 합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Azure Portal에서 링크 이름, 구독 및 관련 가상 네트워크를 사용 하 여 가상 네트워크 링크를 추가 하는 페이지를 표시 합니다.":::

1. DNS 레코드 추가

   필요한 개인 DNS 영역 및 개인 끝점을 만들었으면 dns 레코드를 DNS 영역에 추가 해야 합니다.

   > [!NOTE]
   > 사용자 지정 개인 DNS 영역을 사용 하는 경우 아래에 설명 된 대로 유사한 항목이 생성 되었는지 확인 합니다.

   이 단계에서는 개인 끝점의 각 정규화 된 도메인 이름에 대 한 항목을 개인 DNS 영역으로 설정 해야 합니다.

   1. 개인 DNS 영역으로 이동 하 여 페이지의 왼쪽에 있는 **개요** 섹션으로 이동 합니다. 일단 레코드 ** \+ 집합** 을 선택 하 여 레코드 추가를 시작 합니다.

   1. 열리는 "레코드 집합 추가" 페이지에서 각 정규화 _된_ 도메인 이름 및 개인 IP에 대 한 항목을 유형 레코드로 추가 합니다. 정규화 된 도메인 이름 및 Ip 목록은 **개요**의 "개인 끝점" 페이지에서 가져올 수 있습니다. 아래 예제에 표시 된 것 처럼 개인 끝점의 정규화 된 첫 번째 도메인 이름이 개인 DNS 영역의 레코드 집합에 추가 됩니다.

      이러한 정규화 된 도메인 이름은 패턴과 일치 합니다.`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="정규화 된 도메인 이름에 대 한 DNS A 형식 레코드를 Azure Portal의 개인 끝점에 추가 하는 페이지를 표시 합니다.":::

## <a name="next-steps"></a>다음 단계

가상 컴퓨터 복제를 위한 개인 끝점을 사용 하도록 설정 했으므로 다음과 같은 다른 페이지에서 추가 및 관련 정보를 참조 하세요.

- [온-프레미스 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- [온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정](./hyper-v-azure-tutorial.md)