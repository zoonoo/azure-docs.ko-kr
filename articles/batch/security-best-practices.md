---
title: Batch 보안 및 규정 준수 모범 사례
description: Azure Batch 솔루션을 사용 하 여 보안을 강화 하기 위한 모범 사례와 유용한 팁을 알아보세요.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: b9732ffb810a1038a6f402a46fa8b809f180f0a8
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802289"
---
# <a name="batch-security-and-compliance-best-practices"></a>Batch 보안 및 규정 준수 모범 사례

이 문서에서는 Azure Batch을 사용할 때 보안을 강화 하기 위한 지침과 모범 사례를 제공 합니다.

기본적으로 Azure Batch 계정은 공용 끝점을 포함 하며 공개적으로 액세스할 수 있습니다. Azure Batch 풀이 만들어지면 풀은 Azure 가상 네트워크의 지정 된 서브넷에 프로 비전 됩니다. Batch 풀의 가상 머신은 Batch로 생성 된 공용 IP 주소를 통해 액세스 됩니다. 풀의 계산 노드는 다중 인스턴스 작업을 실행 하는 등 필요한 경우 서로 통신할 수 있지만 풀의 노드는 풀 외부의 가상 컴퓨터와 통신할 수 없습니다.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="일반적인 Batch 환경을 보여 주는 다이어그램":::

다양 한 기능을 사용 하 여 보다 안전한 Azure Batch 배포를 만들 수 있습니다. [공용 IP 주소 없이 풀을 프로 비전](batch-pool-no-public-ip-address.md)하 여 노드에 대 한 액세스를 제한 하 고 인터넷에서 노드의 검색 가능성을 줄일 수 있습니다. 계산 노드는 [Azure virtual network의 서브넷에 풀을 프로 비전](batch-virtual-network.md)하 여 다른 가상 컴퓨터 또는 온-프레미스 네트워크와 안전 하 게 통신할 수 있습니다. 그리고 Azure 개인 링크를 통해 제공 되는 서비스의 [가상 네트워크에서 개인 액세스](private-connectivity.md) 를 사용 하도록 설정할 수 있습니다.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="보다 안전한 배치 환경을 보여 주는 다이어그램":::

## <a name="general-security-related-best-practices"></a>일반적인 보안 관련 모범 사례

### <a name="pool-configuration"></a>풀 구성

많은 보안 기능은 Cloud Services 구성이 포함 된 풀이 아니라 [가상 컴퓨터 구성을](nodes-and-pools.md#configurations)사용 하 여 구성 된 풀 에서만 사용할 수 있습니다. 가능 하면 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)을 활용 하는 가상 머신 구성 풀을 사용 하는 것이 좋습니다.

### <a name="batch-account-authentication"></a>Batch 계정 인증

Batch 계정 액세스는 공유 키와 [Azure Active Directory (AZURE AD)](batch-aad-auth.md)의 두 가지 인증 방법을 지원 합니다.

Batch 계정 인증에 Azure AD를 사용 하는 것이 좋습니다. 일부 일괄 처리 기능에는 여기에서 설명 하는 많은 보안 관련 기능을 비롯 한이 인증 방법이 필요 합니다.

### <a name="batch-account-pool-allocation-mode"></a>Batch 계정 풀 할당 모드

Batch 계정을 만들 때 두 가지 [풀 할당 모드](accounts.md#batch-accounts)중에서 선택할 수 있습니다.

- **Batch 서비스**: 기본 클라우드 서비스 또는 풀 노드를 할당 하 고 관리 하는 데 사용 되는 가상 머신 확장 집합 리소스가 내부 구독에서 생성 되 고 Azure Portal에 직접 표시 되지 않는 기본 옵션입니다. Batch 풀 및 노드만 표시 됩니다. 
- **사용자 구독**: 기본 클라우드 서비스 또는 가상 머신 확장 집합 리소스는 Batch 계정과 동일한 구독에 생성 됩니다. 따라서 이러한 리소스는 해당 일괄 처리 리소스 외에도 구독에서 볼 수 있습니다.

사용자 구독 모드에서 Batch Vm 및 기타 리소스는 풀을 만들 때 구독에서 직접 만들어집니다. Azure Reserved VM Instances를 사용 하 여 Batch 풀을 만들고, 가상 머신 확장 집합 리소스에 대 한 Azure Policy를 사용 하 고, 구독의 코어 할당량을 관리 하려면 (구독의 모든 Batch 계정에서 공유 됨) 사용자 구독 모드가 필요 합니다. 사용자 구독 모드에서 Batch 계정을 만들려면 또한 Azure Batch를 사용하여 구독을 등록하고 Azure Key Vault와 계정을 연결해야 합니다.

## <a name="restrict-network-endpoint-access"></a>네트워크 끝점 액세스 제한

### <a name="batch-network-endpoints"></a>Batch 네트워크 끝점

기본적으로 공용 IP 주소를 사용 하는 끝점은 Batch 계정, Batch 풀 및 풀 노드와 통신 하는 데 사용 됩니다.

### <a name="batch-account-api"></a>Batch 계정 API

 Batch 계정이 만들어지면 [REST API](/rest/api/batchservice/)를 사용 하 여 계정에 대 한 대부분의 작업을 호출 하는 데 사용 되는 공용 끝점이 만들어집니다. 계정 끝점에는 형식을 사용 하는 기본 URL이 `https://{account-name}.{region-id}.batch.azure.com` 있습니다. 일괄 처리 계정에 대 한 액세스는 HTTPS를 사용 하 여 암호화 되는 계정 끝점과의 통신 및 공유 키 또는 Azure Active Directory (Azure AD) 인증을 사용 하 여 인증 된 각 요청에 대 한 통신을 통해 보안이 유지 됩니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자

일괄 처리 계정과 관련 된 작업 외에도 [관리 작업](/rest/api/batchmanagement/) 은 단일 및 다중 배치 계정에 적용 됩니다. 이러한 관리 작업은 Azure Resource Manager를 통해 액세스 됩니다.

Azure Resource Manager를 통한 일괄 처리 관리 작업은 HTTPS를 사용 하 여 암호화 되 고 각 요청은 Azure AD 인증을 사용 하 여 인증 됩니다.

### <a name="batch-pool-nodes"></a>Batch 풀 노드

Batch 서비스는 풀의 각 노드에서 실행 되는 Batch 노드 에이전트와 통신 합니다. 예를 들어 서비스는 태스크를 실행 하거나 작업을 중지 하거나 태스크에 대 한 파일을 가져오기 위해 노드 에이전트에 지시 합니다. 노드 에이전트와의 통신은 하나 이상의 부하 분산 장치에서 사용 하도록 설정 되며,이 수는 풀의 노드 수에 따라 달라 집니다. 부하 분산 장치는 원하는 노드에 통신을 전달 하 고 각 노드는 고유한 포트 번호로 주소가 지정 됩니다. 기본적으로 부하 분산 장치에는 연결 된 공용 IP 주소가 있습니다. RDP 또는 SSH를 통해 풀 노드에 원격으로 액세스할 수도 있습니다 .이 액세스는 기본적으로 사용 하도록 설정 되 고 부하 분산 장치를 통한 통신에 사용 됩니다.

### <a name="restricting-access-to-batch-endpoints"></a>일괄 처리 끝점에 대 한 액세스 제한 

특히 솔루션에서 가상 네트워크를 사용 하는 경우 다양 한 일괄 처리 끝점에 대 한 액세스를 제한 하는 데 몇 가지 기능을 사용할 수 있습니다. 

#### <a name="use-private-endpoints"></a>프라이빗 엔드포인트 사용

[Azure 개인 링크](../private-link/private-link-overview.md) 를 사용 하면 가상 네트워크의 개인 끝점을 통해 Azure PaaS 서비스 및 azure에서 호스트 하는 고객 소유/파트너 서비스에 액세스할 수 있습니다. 개인 링크를 사용 하 여 가상 네트워크 또는 피어 링 가상 네트워크 내에서 Batch 계정에 대 한 액세스를 제한할 수 있습니다. Private Link에 매핑된 리소스 역시 프라이빗 피어링을 사용하여 VPN 또는 Azure ExpressRoute를 통해 온-프레미스에서 액세스할 수 있습니다.

개인 끝점을 사용 하려면 일괄 처리 계정을 만들 때 적절 하 게 구성 해야 합니다. 공용 네트워크 액세스 구성을 사용 하지 않도록 설정 해야 합니다. 만든 후에는 개인 끝점을 만들고 Batch 계정에 연결할 수 있습니다. 자세한 내용은 [Azure Batch 계정으로 전용 끝점 사용](private-connectivity.md)을 참조 하세요.

#### <a name="create-pools-in-virtual-networks"></a>가상 네트워크에서 풀 만들기

Batch 풀의 계산 노드는 VNET (가상 네트워크)을 요구 하지 않고 여러 인스턴스 작업을 실행 하기 위해와 같이 서로 통신할 수 있습니다. 그러나 기본적으로 풀의 노드는 가상 네트워크의 풀 외부에 있고 라이선스 서버 또는 파일 서버와 같은 개인 IP 주소를 가진 가상 컴퓨터와 통신할 수 없습니다.

계산 노드가 다른 가상 컴퓨터 또는 온-프레미스 네트워크와 안전 하 게 통신 하도록 허용 하려면 Azure VNET의 서브넷에 있도록 풀을 구성할 수 있습니다.

풀에 공용 IP 끝점이 있는 경우 서브넷은 Batch 서비스에서 인바운드 통신을 허용 하 여 작업을 예약 하 고 계산 노드에서 다른 작업을 수행 하 고, 아웃 바운드 통신을 수행 하 여 작업에 필요한 대로 Azure Storage 또는 다른 리소스와 통신할 수 있도록 해야 합니다. 가상 컴퓨터 구성에서 풀의 경우 Batch는 계산 노드에 연결 된 네트워크 인터페이스 수준에서 NSGs (네트워크 보안 그룹)를 추가 합니다. 이러한 NSGs에는 다음을 사용 하도록 설정 하는 규칙이 있습니다.

- Batch 서비스 IP 주소의 인바운드 TCP 트래픽
- 원격 액세스에 대 한 인바운드 TCP 트래픽
- 가상 네트워크에 대 한 모든 포트의 아웃 바운드 트래픽 (서브넷 수준 NSG 규칙에 따라 수정 될 수 있음)
- 인터넷에 대 한 모든 포트의 아웃 바운드 트래픽 (서브넷 수준 NSG 규칙에 따라 수정 될 수 있음)

Batch에서 자체 NSGs를 구성 하므로 가상 네트워크 서브넷 수준에서 NSGs를 지정할 필요가 없습니다. Batch 계산 노드가 배포 되는 서브넷과 연결 된 NSG가 있거나 적용 된 기본값을 재정의 하는 사용자 지정 NSG 규칙을 적용 하려는 경우, Azure Storage에 대 한 일괄 처리 서비스 통신을 허용 하려면 적어도 인바운드 및 아웃 바운드 보안 규칙을 사용 하 여이 NSG를 구성 해야 합니다.

자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](batch-virtual-network.md)를 참조 하세요.

#### <a name="create-pools-with-static-public-ip-addresses"></a>고정 공용 IP 주소를 사용 하 여 풀 만들기

기본적으로 풀과 연결 된 공용 IP 주소는 동적입니다. 풀을 만들 때 생성 되며 풀의 크기를 조정할 때 IP 주소를 추가 하거나 제거할 수 있습니다. 풀 노드에서 실행 중인 태스크 응용 프로그램에서 외부 서비스에 액세스 해야 하는 경우 해당 서비스에 대 한 액세스를 특정 Ip로 제한 해야 할 수 있습니다.  이 경우 동적 IP 주소를 관리 하는 것은 관리할 수 없습니다.

풀을 만들기 전에 Batch 계정과 동일한 구독에 고정 공용 IP 주소 리소스를 만들 수 있습니다. 그런 다음 풀을 만들 때 이러한 주소를 지정할 수 있습니다.

자세한 내용은 [지정 된 공용 IP 주소를 사용 하 여 Azure Batch 풀 만들기](create-pool-public-ip.md)를 참조 하세요.

#### <a name="create-pools-without-public-ip-addresses"></a>공용 IP 주소가 없는 풀 만들기

기본적으로 Azure Batch 가상 컴퓨터 구성 풀의 모든 계산 노드에는 하나 이상의 공용 IP 주소가 할당 됩니다. 이러한 끝점은 Batch 서비스에서 작업을 예약 하 고, 인터넷에 대 한 아웃 바운드 액세스를 비롯 하 여 계산 노드와 통신 하는 데 사용 됩니다.

이러한 노드에 대 한 액세스를 제한 하 고 인터넷에서 이러한 노드의 검색 가능성을 줄이려면 공용 IP 주소 없이 풀을 프로 비전 할 수 있습니다.

자세한 내용은 [공용 IP 주소를 사용 하지 않고 풀 만들기](batch-pool-no-public-ip-address.md)를 참조 하세요.

#### <a name="limit-remote-access-to-pool-nodes"></a>풀 노드에 대 한 원격 액세스 제한

기본적으로 Batch를 사용 하면 네트워크에 연결 된 노드 사용자가 RDP 또는 SSH를 사용 하 여 Batch 풀의 계산 노드에 외부적으로 연결할 수 있습니다.

노드에 대 한 원격 액세스를 제한 하려면 다음 방법 중 하나를 사용 합니다.

- 액세스를 거부 하도록 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 을 구성 합니다. 적절 한 NSG (네트워크 보안 그룹)가 풀과 연결 됩니다.
- [공용 IP 주소 없이](batch-pool-no-public-ip-address.md)풀을 만듭니다. 기본적으로 이러한 풀은 VNet 외부에서 액세스할 수 없습니다.
- VNet과 NSG를 연결 하 여 RDP 또는 SSH 포트에 대 한 액세스를 거부 합니다.
- 노드에 사용자를 만들지 마세요. 노드 사용자가 없는 경우 원격 액세스를 수행할 수 없습니다.

## <a name="encrypt-data"></a>데이터 암호화

### <a name="encrypt-data-in-transit"></a>전송 중인 데이터 암호화

Batch 계정 끝점 (또는 Azure Resource Manager를 통해)에 대 한 모든 통신은 HTTPS를 사용 해야 합니다. `https://`Batch 서비스에 연결할 때 api에 지정 된 배치 계정 url에서를 사용 해야 합니다.

Batch 서비스와 통신 하는 클라이언트는 TLS (전송 계층 보안) 1.2를 사용 하도록 구성 해야 합니다.

### <a name="encrypt-batch-data-at-rest"></a>미사용 일괄 처리 데이터 암호화

Batch 서비스에 의해 저장 되는 경우 Batch Api에 지정 된 일부 정보 (예: 계정 인증서, 작업 및 태스크 메타 데이터 및 작업 명령줄)가 자동으로 암호화 됩니다. 기본적으로이 데이터는 각 Batch 계정에 고유한 Azure Batch 플랫폼 관리 키를 사용 하 여 암호화 됩니다.

[고객 관리 키](batch-customer-managed-key.md)를 사용 하 여이 데이터를 암호화할 수도 있습니다. [Azure Key Vault](../key-vault/general/overview.md) 는 키 식별자를 사용 하 여 키를 생성 하 고 저장 하는 데 사용 됩니다 (배치 계정에 등록 됨).

### <a name="encrypt-compute-node-disks"></a>계산 노드 디스크 암호화

일괄 처리 계산 노드에는 기본적으로 OS 디스크와 로컬 임시 SSD 라는 두 개의 디스크가 있습니다. Batch에서 관리 하는 [파일 및 디렉터리](files-and-directories.md) 는 임시 SSD (작업 출력 파일 등의 파일에 대 한 기본 위치)에 있습니다. Batch 작업 응용 프로그램은 SSD 또는 OS 디스크의 기본 위치를 사용할 수 있습니다.

추가 보안을 위해 다음 Azure disk encryption 기능 중 하나를 사용 하 여 이러한 디스크를 암호화 합니다.

- [플랫폼 관리 키를 사용 하 여 미사용 관리 디스크 암호화](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [플랫폼 관리 키를 사용 하 여 호스트에서 암호화](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 디스크 암호화](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>계산 노드에서 안전 하 게 서비스에 액세스

일괄 처리 노드는 [Azure Key Vault](../key-vault/general/overview.md)에 저장 된 [자격 증명과 암호에 안전 하 게 액세스할](credential-access-key-vault.md) 수 있으며, 작업 응용 프로그램에서 다른 서비스에 액세스 하는 데 사용할 수 있습니다. 인증서는 Key Vault에 대 한 액세스를 풀 노드에 부여 하는 데 사용 됩니다.

## <a name="governance-and-compliance"></a>거버넌스 및 규정 준수

### <a name="compliance"></a>규정 준수

고객이 전 세계 규정 된 산업 및 시장에서 자신의 규정 준수 의무를 충족 하는 데 도움이 되도록 Azure는 [광범위 한 규정 준수 제품](https://azure.microsoft.com/overview/trusted-cloud/compliance)을 유지 관리 합니다. 

이러한 제품은 Microsoft에서 생성 한 계약 개정, 자체 평가 및 고객 지침 문서 뿐만 아니라 공식 인증, 증명, 유효성 검사, 권한 부여 및 평가를 비롯 한 다양 한 유형의 보증을 기반으로 합니다. [준수 제품의 포괄적인 개요](https://aka.ms/AzureCompliance) 를 검토 하 여 Batch 솔루션과 관련이 있을 수 있는 기능을 확인 합니다.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) 는 조직의 표준을 적용 하 고 대규모로 규정 준수를 평가 하는 데 도움이 됩니다. Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다.

풀 할당 모드 및 정책이 적용 해야 하는 리소스에 따라 다음 방법 중 하나로 일괄 처리에 Azure Policy를 사용 합니다.

- Microsoft.Batch/batchAccounts 리소스를 사용 하 여 직접. Batch 계정에 대 한 속성의 하위 집합을 사용할 수 있습니다. 예를 들어 정책에는 유효한 배치 계정 영역, 허용 된 풀 할당 모드 및 계정에 대해 공용 네트워크를 사용할 수 있는지 여부가 포함 될 수 있습니다.
- Microsoft. Compute/virtualMachineScaleSets 리소스를 사용 하 여 간접적으로 사용자 구독 풀 할당 모드를 사용 하는 batch 계정에는 Batch 계정 구독에서 생성 된 가상 머신 확장 집합 리소스에 대해 설정 된 정책이 있을 수 있습니다. 예를 들어 허용 되는 VM 크기와 각 풀 노드에서 특정 확장이 실행 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Batch에 대 한 Azure 보안 기준](security-baseline.md)을 검토 합니다.
- [Azure Batch에 대 한 모범 사례를](best-practices.md)참조 하세요.
