---
title: Azure HPC 캐시에서 고객 관리 키를 사용하여 데이터 암호화
description: Azure HPC 캐시를 사용하여 기본 Microsoft 관리 암호화 키를 사용하는 대신 암호화 키 액세스를 제어하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538870"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Azure HPC 캐시에 고객 관리 암호화 키 사용

Azure 키 자격 증명 모음을 사용하여 Azure HPC 캐시에서 데이터를 암호화하는 데 사용되는 키의 소유권을 제어할 수 있습니다. 이 문서에서는 캐시 데이터 암호화에 고객 관리 키를 사용하는 방법을 설명합니다.

> [!NOTE]
> 캐시 디스크를 포함하여 Azure에 저장된 모든 데이터는 기본적으로 Microsoft에서 관리하는 키를 사용하여 미사용 으로 암호화됩니다. 데이터를 암호화하는 데 사용되는 키를 관리하려면 이 문서의 단계를 따르기만 하면 됩니다.

이 기능은 다음 Azure 지역에서만 사용할 수 있습니다.

* 미국 동부
* 미국 중남부
* 미국 서부 2

Azure HPC 캐시에 대해 고객 관리 키 암호화를 활성화하는 세 가지 단계가 있습니다.

1. 키를 저장하려면 Azure 키 자격 증명 모음을 설정합니다.
1. Azure HPC 캐시를 만들 때 고객이 관리하는 키 암호화를 선택하고 사용할 키 자격 증명 모음 및 키를 지정합니다.
1. 캐시를 만든 후 키 자격 증명 모음에 액세스할 수 있는 권한을 부여합니다.

새로 만든 캐시(3단계)에서 암호화를 승인할 때까지 암호화가 완전히 설정되지 않습니다. 이는 캐시의 ID를 인증된 사용자로 만들기 위해 키 자격 증명 모음에 전달해야 하기 때문입니다. 캐시를 만들 때까지 ID가 존재하지 않으므로 캐시를 만들기 전에는 이 작업을 수행할 수 없습니다.

캐시를 만든 후에는 고객 관리 키와 Microsoft 관리 키 간에 변경할 수 없습니다. 그러나 캐시에서 고객 관리 키를 사용하는 경우 필요에 따라 암호화 키, 키 버전 및 키 자격 증명 모음을 [변경할](#update-key-settings) 수 있습니다.

## <a name="understand-key-vault-and-key-requirements"></a>키 자격 증명 모음 및 주요 요구 사항 이해

키 자격 증명 모음 및 키는 Azure HPC 캐시에서 작동하려면 이러한 요구 사항을 충족해야 합니다.

키 볼트 속성:

* **구독** - 캐시에 사용되는 것과 동일한 구독을 사용합니다.
* **지역** - 키 자격 증명 모음은 Azure HPC 캐시와 동일한 지역에 있어야 합니다.
* **가격 책정 계층** - 표준 계층은 Azure HPC 캐시에서 사용하기에 충분합니다.
* **소프트 삭제** - Azure HPC 캐시는 키 자격 증명 모음에 아직 구성되지 않은 경우 소프트 삭제를 사용하도록 설정합니다.
* **지우기 보호** - 제거 보호를 사용하도록 설정해야 합니다.
* **액세스 정책** - 기본 설정으로 충분합니다.
* **네트워크 연결** - Azure HPC 캐시는 선택한 끝점 설정에 관계없이 키 자격 증명 모음에 액세스할 수 있어야 합니다.

주요 속성:

* **키 유형** - RSA
* **RSA 키 크기** - 2048
* **사용 -** 예

키 자격 증명 모음 액세스 권한:

* Azure HPC 캐시를 만드는 [사용자에게는 키 볼트 기여자 역할과](../role-based-access-control/built-in-roles.md#key-vault-contributor)동일한 권한이 있어야 합니다. Azure 키 자격 증명 모음을 설정하고 관리하는 데 동일한 권한이 필요합니다.

  자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스를](../key-vault/key-vault-secure-your-key-vault.md) 참조하십시오.

## <a name="1-set-up-azure-key-vault"></a>1. Azure 키 볼트 설정

캐시를 만들기 전에 키 자격 증명 모음 및 키를 설정하거나 캐시 생성의 일부로 설정할 수 있습니다. 이러한 리소스가 위에 설명된 요구 사항을 충족하는지 [확인합니다.](#understand-key-vault-and-key-requirements)

캐시 생성 시 캐시 암호화에 사용할 자격 증명 모음, 키 및 키 버전을 지정해야 합니다.

자세한 내용은 [Azure 키 자격 증명 모음 설명서를](../key-vault/key-vault-overview.md) 참조하십시오.

> [!NOTE]
> Azure 키 자격 증명 모음은 동일한 구독을 사용하고 Azure HPC 캐시와 동일한 지역에 있어야 합니다. 이 문서의 시작 부분에 나열된 지원되는 지역 중 하나를 사용합니다.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 고객 관리 키가 활성화된 캐시 만들기

Azure HPC 캐시를 만들 때 암호화 키 원본을 지정해야 합니다. [Azure HPC 캐시 만들기의](hpc-cache-create.md)지침에 따라 디스크 암호화 키 페이지에서 키 자격 증명 모음 및 키를 **지정합니다.** 캐시를 만드는 동안 새 키 자격 증명 모음 및 키를 만들 수 있습니다.

> [!TIP]
> 디스크 **암호화 키** 페이지가 나타나지 않으면 캐시가 지원되는 지역 중 하나에 있는지 확인합니다.

캐시를 만드는 사용자는 [Key Vault 기여자 역할](../role-based-access-control/built-in-roles.md#key-vault-contributor) 과 같은 권한이 있어야 합니다.

1. 단추를 클릭하여 개인적으로 관리되는 키를 활성화합니다. 이 설정을 변경하면 키 자격 증명 모음 설정이 나타납니다.

1. **키 자격 증명 모음 을 클릭하여 키** 선택 페이지를 엽니다. 이 캐시의 디스크에서 데이터를 암호화하기 위한 키 자격 증명 모음 및 키를 선택하거나 만듭니다.

   Azure 키 자격 증명 모음이 목록에 나타나지 않으면 다음 요구 사항을 확인하십시오.

   * 캐시가 키 자격 증명 모음과 동일한 구독에 있습니까?
   * 캐시가 키 자격 증명 모음과 동일한 영역에 있습니까?
   * Azure 포털과 키 자격 증명 모음 사이에 네트워크 연결이 있습니까?

1. 볼트를 선택한 후 사용 가능한 옵션에서 개별 키를 선택하거나 새 키를 만듭니다. 키는 2048비트 RSA 키여야 합니다.

1. 선택한 키의 버전을 지정합니다. [Azure 키 자격 증명 모음 설명서에서](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)버전 조정에 대해 자세히 알아봅니다.

사양의 나머지 부분을 계속 하 고 Azure [HPC](hpc-cache-create.md)캐시 만들기에 설명 된 대로 캐시를 만듭니다.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 캐시에서 Azure 키 볼트 암호화 권한 부여
<!-- header is linked from create article, update if changed -->

몇 분 후 새 Azure HPC 캐시가 Azure 포털에 나타납니다. **개요** 페이지로 이동하여 Azure Key Vault에 액세스하고 고객이 관리하는 키 암호화를 사용하도록 승인합니다. 캐시는 "배포 진행 중" 메시지가 지워지기 전에 리소스 목록에 나타날 수 있습니다.

Azure HPC 캐시 인스턴스에 권한 부여를 위해 Azure 키 자격 증명 모음에 전달하려면 ID가 필요하기 때문에 이 2단계 프로세스가 필요합니다. 캐시 ID는 초기 생성 단계가 완료될 때까지 존재하지 않습니다.

> [!NOTE]
> 캐시를 만든 후 90분 이내에 암호화에 권한을 부여해야 합니다. 이 단계를 완료하지 않으면 캐시가 시간 지정되고 실패합니다. 실패한 캐시를 다시 만들어야 하며 수정할 수 없습니다.

캐시에 키 **대기**상태가 표시됩니다. 페이지 상단의 **암호화 활성화** 단추를 클릭하여 캐시가 지정된 키 자격 증명 모음에 액세스할 수 있도록 권한을 부여합니다.

![포털의 캐시 개요 페이지의 스크린샷, 암호화 사용 버튼(맨 위 행) 및 상태: 키 대기 중](media/waiting-for-key.png)

**암호화 활성화를** 클릭한 다음 **예** 버튼을 클릭하여 캐시에 암호화 키를 사용하도록 권한을 부여합니다. 또한 이 작업을 사용하면 키 자격 증명 모음에서 소프트 삭제 및 제거 보호(아직 활성화되지 않은 경우)도 사용할 수 있습니다.

![포털의 캐시 개요 페이지의 스크린샷, 상단에 'yes'를 클릭하여 암호화를 사용하도록 요청하는 배너 메시지](media/enable-keyvault.png)

캐시가 키 자격 증명 모음에 대한 액세스를 요청한 후 캐시된 데이터를 저장하는 디스크를 만들고 암호화할 수 있습니다.

암호화를 승인한 후 Azure HPC 캐시는 암호화된 디스크 및 관련 인프라를 만들기 위해 몇 분 이상의 설정을 거칩니다.

## <a name="update-key-settings"></a>키 설정 업데이트

Azure 포털에서 캐시의 키 자격 증명 모음, 키 또는 키 버전을 변경할 수 있습니다. 캐시의 **암호화** 설정 링크를 클릭하여 **고객 키 설정** 페이지를 엽니다. (고객 관리 키와 시스템 관리 키 간에는 캐시를 변경할 수 없습니다.)

![Azure 포털의 캐시 페이지에서 암호화를 > 설정을 클릭하여 도달한 "고객 키 설정" 페이지의 스크린샷](media/change-key-click.png)

키 **변경** 링크를 클릭한 다음 **키 자격 증명 모음, 키 또는 버전 변경을** 클릭하여 키 선택기를 엽니다.

![키 볼트, 키 및 버전을 선택하는 세 개의 드롭다운 선택기와 함께 "Azure Key Vault에서 키 선택" 페이지의 스크린샷](media/select-new-key.png)

이 캐시와 동일한 구독 및 동일한 리전의 키 자격 증명 모음이 목록에 표시됩니다.

새 암호화 키 값을 선택한 후 **선택**을 클릭합니다. 새 값과 함께 확인 페이지가 나타납니다. **저장을** 클릭하여 선택을 완료합니다.

![왼쪽 상단에 저장 버튼이 있는 확인 페이지의 스크린샷](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Azure에서 고객 관리 키에 대해 자세히 알아보기

이 문서에서는 Azure Key Vault 및 고객 관리 키를 사용하여 Azure의 데이터를 암호화하는 데 대해 자세히 설명합니다.

* [Azure 저장소 암호화 개요](../storage/common/storage-service-encryption.md)
* [고객 관리 키가 있는 디스크 암호화](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) - Azure 키 볼트 및 관리 디스크사용에 대한 설명서(Azure HPC 캐시와 함께 사용되는 프로세스와 유사)

## <a name="next-steps"></a>다음 단계

Azure HPC 캐시를 만들고 키 볼트 기반 암호화를 승인한 후에는 데이터 원본에 대한 액세스 권한을 부여하여 캐시를 계속 설정합니다.

* [스토리지 대상 추가](hpc-cache-add-storage.md)
