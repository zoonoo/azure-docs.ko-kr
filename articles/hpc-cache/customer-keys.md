---
title: 고객 관리 되 키를 사용 하 여 Azure HPC 캐시의 데이터 암호화
description: Azure HPC 캐시에서 Azure Key Vault를 사용 하 여 기본 Microsoft 관리 암호화 키를 사용 하는 대신 암호화 키 액세스를 제어 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 2cd97e205d88fe7ead02889f5ae9ad9df0985f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092527"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Azure HPC 캐시에 대해 고객이 관리 하는 암호화 키 사용

Azure Key Vault를 사용 하 여 Azure HPC 캐시에서 데이터를 암호화 하는 데 사용 되는 키의 소유권을 제어할 수 있습니다. 이 문서에서는 캐시 데이터 암호화에 고객이 관리 하는 키를 사용 하는 방법을 설명 합니다.

> [!NOTE]
> 캐시 디스크를 포함 하 여 Azure에 저장 된 모든 데이터는 기본적으로 Microsoft 관리 키를 사용 하 여 미사용에 암호화 됩니다. 데이터를 암호화 하는 데 사용 되는 키를 관리 하려는 경우에만이 문서의 단계를 수행 해야 합니다.

또한 Azure HPC 캐시는 캐시 디스크에 대 한 고객 키를 추가 하는 경우에도 캐시 된 데이터를 보관 하는 관리 디스크에서 [VM 호스트 암호화](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) 를 통해 보호 됩니다. 이중 암호화를 위해 고객이 관리 하는 키를 추가 하면 보안 요구 사항이 높은 고객에 게 추가 보안 수준이 제공 됩니다. 자세한 내용은 [Azure disk storage의 서버 쪽 암호화](../virtual-machines/linux/disk-encryption.md) 를 참조 하세요.

이 기능은 Azure HPC 캐시를 사용할 수 있는 일부 Azure 지역 에서만 사용할 수 있습니다. 자세한 내용은 [지역 가용성](hpc-cache-overview.md#region-availability) 목록을 참조 하세요.

Azure HPC 캐시에 대해 고객이 관리 하는 키 암호화를 사용 하도록 설정 하는 세 가지 단계가 있습니다.

1. 키를 저장할 Azure Key Vault 설정 합니다.
1. Azure HPC 캐시를 만들 때 고객 관리 키 암호화를 선택 하 고 사용할 키 자격 증명 모음 및 키를 지정 합니다.
1. 캐시를 만든 후 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다.

암호화는 새로 만든 캐시에서 권한을 부여할 때까지 완전 하 게 설정 되지 않습니다 (3 단계). 이는 캐시 id를 키 자격 증명 모음에 전달 하 여 권한이 있는 사용자로 만들어야 하기 때문입니다. 캐시를 만들 때까지 id가 존재 하지 않기 때문에 캐시를 만들기 전에이 작업을 수행할 수 없습니다.

캐시를 만든 후에는 고객 관리 키와 Microsoft 관리 키 사이에서 변경할 수 없습니다. 그러나 캐시에서 고객이 관리 하는 키를 사용 하는 경우 필요에 따라 암호화 키, 키 버전 및 키 자격 증명 모음을 [변경할](#update-key-settings) 수 있습니다.

## <a name="understand-key-vault-and-key-requirements"></a>주요 자격 증명 모음 및 주요 요구 사항 이해

키 자격 증명 모음 및 키는 Azure HPC 캐시로 작업 하기 위해 이러한 요구 사항을 충족 해야 합니다.

주요 자격 증명 모음 속성:

* **구독** -캐시에 사용 되는 것과 동일한 구독을 사용 합니다.
* **지역** -key Vault는 Azure HPC 캐시와 동일한 지역에 있어야 합니다.
* **가격 책정 계층** -표준 계층은 Azure HPC 캐시에 사용 하기에 충분 합니다.
* **일시 삭제** -Azure HPC 캐시는 key vault에 아직 구성 되지 않은 경우 일시 삭제를 사용 하도록 설정 합니다.
* **보호 제거** -보호 제거를 사용 하도록 설정 해야 합니다.
* **액세스 정책** -기본 설정으로 충분 합니다.
* **네트워크 연결** -선택한 끝점 설정에 관계 없이 Azure HPC 캐시가 키 자격 증명 모음에 액세스할 수 있어야 합니다.

키 속성:

* **키 유형** -RSA
* **RSA 키 크기** -2048
* **사용** -예

키 자격 증명 모음 액세스 권한:

* Azure HPC 캐시를 만드는 사용자에 게 [Key Vault 참여자 역할](../role-based-access-control/built-in-roles.md#key-vault-contributor)에 해당 하는 권한이 있어야 합니다. Azure Key Vault를 설정 하 고 관리 하는 데에도 동일한 사용 권한이 필요 합니다.

  자세한 내용은 [주요 자격 증명 모음에 대 한 보안 액세스를](../key-vault/key-vault-secure-your-key-vault.md) 참조 하세요.

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault 설정

캐시를 만들기 전에 키 자격 증명 모음 및 키를 설정 하거나 캐시를 만드는 과정에서 키 자격 증명 모음을 사용할 수 있습니다. 이러한 리소스가 [위에](#understand-key-vault-and-key-requirements)설명 된 요구 사항을 충족 하는지 확인 합니다.

캐시를 만들 때 캐시의 암호화에 사용할 자격 증명 모음, 키 및 키 버전을 지정 해야 합니다.

자세한 내용은 [Azure Key Vault 설명서](../key-vault/key-vault-overview.md) 를 참조 하세요.

> [!NOTE]
> Azure Key Vault는 동일한 구독을 사용 해야 하며, Azure HPC 캐시와 동일한 지역에 있어야 합니다. 선택한 지역이 [고객이 관리 하는 키 기능을 지원](hpc-cache-overview.md#region-availability)하는지 확인 합니다.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 고객 관리 키를 사용 하는 캐시 만들기

Azure HPC 캐시를 만들 때 암호화 키 원본을 지정 해야 합니다. [AZURE HPC 캐시 만들기](hpc-cache-create.md)의 지침에 따라 **디스크 암호화 키** 페이지에서 키 자격 증명 모음 및 키를 지정 합니다. 캐시를 만드는 동안 새 key vault 및 키를 만들 수 있습니다.

> [!TIP]
> **디스크 암호화 키** 페이지가 표시 되지 않는 경우 캐시가 지원 되는 지역 중 하나에 있는지 확인 합니다.

캐시를 만드는 사용자에 게는 [Key Vault 참여자 역할과](../role-based-access-control/built-in-roles.md#key-vault-contributor) 같은 권한이 있어야 합니다.

1. 단추를 클릭 하 여 개인적으로 관리 되는 키를 사용 하도록 설정 합니다. 이 설정을 변경한 후에는 key vault 설정이 표시 됩니다.

1. 키 **자격 증명 모음 선택** 을 클릭 하 여 키 선택 페이지를 엽니다. 이 캐시의 디스크에서 데이터를 암호화 하기 위한 주요 자격 증명 모음 및 키를 선택 하거나 만듭니다.

   Azure Key Vault 목록에 표시 되지 않으면 다음 요구 사항을 확인 하세요.

   * 키 자격 증명 모음과 동일한 구독의 캐시 입니까?
   * 키 자격 증명 모음과 동일한 지역에 있는 캐시 입니까?
   * Azure Portal와 key vault 사이에 네트워크 연결이 있나요?

1. 자격 증명 모음을 선택한 후 사용 가능한 옵션에서 개별 키를 선택 하거나 새 키를 만듭니다. 키는 2048 비트 RSA 키 여야 합니다.

1. 선택한 키의 버전을 지정 합니다. [Azure Key Vault 설명서](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)의 버전 관리에 대해 자세히 알아보세요.

나머지 사양을 계속 진행 하 고 [AZURE HPC 캐시 만들기](hpc-cache-create.md)에 설명 된 대로 캐시를 만듭니다.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 캐시에서 Azure Key Vault 암호화에 권한을 부여 합니다.
<!-- header is linked from create article, update if changed -->

몇 분 후에 새 Azure HPC 캐시가 Azure Portal에 표시 됩니다. **개요** 페이지로 이동 하 여 Azure Key Vault에 대 한 액세스 권한을 부여 하 고 고객 관리 키 암호화를 사용 하도록 설정 합니다.

> [!TIP]
> "배포가 진행 중입니다." 라는 메시지가 표시 되기 전에 캐시는 리소스 목록에 표시 될 수 있습니다. 성공 알림이 표시 되는 것이 아니라 1 ~ 2 일 후에 리소스 목록을 확인 합니다.

이 2 단계 프로세스는 Azure HPC 캐시 인스턴스에 권한 부여를 위해 Azure Key Vault에 전달할 id가 필요 하기 때문에 필요 합니다. 캐시 id는 초기 생성 단계가 완료 될 때까지 존재 하지 않습니다.

> [!NOTE]
> 캐시를 만든 후 90 분 이내에 암호화에 권한을 부여 해야 합니다. 이 단계를 완료 하지 않으면 캐시 시간이 초과 되 고 실패 합니다. 실패 한 캐시는 다시 만들어야 하므로 수정할 수 없습니다.

캐시는 **키 대기**상태를 표시 합니다. 페이지 맨 위에 있는 **암호화 사용** 단추를 클릭 하 여 캐시에 지정 된 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다.

![암호화 사용 단추 (위쪽 행) 및 상태: 키 대기 중에 강조 표시 된 포털의 캐시 개요 페이지 스크린샷](media/waiting-for-key.png)

**암호화 사용** 을 클릭 한 다음 **예** 단추를 클릭 하 여 캐시에 암호화 키를 사용할 수 있는 권한을 부여 합니다. 이 작업을 수행 하면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호 (아직 사용 하지 않는 경우)도 사용할 수 있습니다.

![예를 클릭 하 여 암호화를 사용 하도록 사용자에 게 요청 하는 배너 메시지를 포함 하는 포털의 캐시 개요 페이지 스크린샷](media/enable-keyvault.png)

캐시는 키 자격 증명 모음에 대 한 액세스를 요청한 후 캐시 된 데이터를 저장 하는 디스크를 만들고 암호화할 수 있습니다.

암호화 권한을 부여 하 고 나면 Azure HPC 캐시가 암호화 된 디스크 및 관련 된 인프라를 만들기 위해 몇 분의 설치를 진행 합니다.

## <a name="update-key-settings"></a>키 설정 업데이트

Azure Portal에서 캐시에 대 한 주요 자격 증명 모음, 키 또는 키 버전을 변경할 수 있습니다. 캐시의 **암호화** 설정 링크를 클릭 하 여 **고객 키 설정** 페이지를 엽니다.

고객 관리 키와 시스템 관리 키 사이에서 캐시를 변경할 수 없습니다.

!["고객 키 설정" 페이지의 스크린샷에는 설정 > 클릭 하 여에 도달 하 고 캐시 페이지의 캐시 페이지에서 암호화를 Azure Portal](media/change-key-click.png)

키 **변경** 링크를 클릭 한 다음 키 **자격 증명 모음, 키 또는 버전 변경** 을 클릭 하 여 키 선택기를 엽니다.

![세 개의 드롭다운 선택기를 사용 하 여 주요 자격 증명 모음, 키 및 버전을 선택 하는 "Azure Key Vault에서 키 선택" 페이지의 스크린샷](media/select-new-key.png)

이 캐시와 동일한 구독 및 동일한 지역에 있는 주요 자격 증명 모음은 목록에 표시 됩니다.

새 암호화 키 값을 선택한 후 **선택**을 클릭 합니다. 새 값이 포함 된 확인 페이지가 나타납니다. **저장** 을 클릭 하 여 선택을 마무리 합니다.

![왼쪽 위에 저장 단추가 있는 확인 페이지의 스크린샷](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Azure에서 고객이 관리 하는 키에 대해 자세히 알아보세요.

이러한 문서에서는 Azure Key Vault 및 고객 관리 키를 사용 하 여 Azure에서 데이터를 암호화 하는 방법에 대해 자세히 설명 합니다.

* [Azure storage 암호화 개요](../storage/common/storage-service-encryption.md)
* [고객 관리 키를 사용 하는 디스크 암호화](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) -Azure HPC 캐시와 유사한 시나리오인 관리 디스크와 Azure Key Vault 사용에 대 한 설명서입니다.

## <a name="next-steps"></a>다음 단계

Azure HPC 캐시와 권한 있는 Key Vault 기반 암호화를 만든 후 데이터 원본에 대 한 액세스 권한을 부여 하 여 캐시를 계속 설정 합니다.

* [스토리지 대상 추가](hpc-cache-add-storage.md)
