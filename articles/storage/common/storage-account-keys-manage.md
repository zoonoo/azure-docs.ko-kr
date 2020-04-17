---
title: 계정 선택키 관리
titleSuffix: Azure Storage
description: 저장소 계정 액세스 키를 보고 관리 및 회전하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 50c0980800bbc9b2951bf9107114c1a4d9265558
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454665"
---
# <a name="manage-storage-account-access-keys"></a>저장소 계정 액세스 키 관리

스토리지 계정을 만들 때 Azure는 두 개의 512비트 스토리지 계정 액세스 키를 생성합니다. 이러한 키는 공유 키 인증을 통해 저장소 계정의 데이터에 대한 액세스 권한을 부여하는 데 사용할 수 있습니다.

Azure Key Vault를 사용하여 액세스 키를 관리하고 키를 정기적으로 회전하고 다시 생성하는 것이 좋습니다. Azure 키 볼트를 사용하면 응용 프로그램에 중단 없이 키를 쉽게 회전할 수 있습니다. 키를 수동으로 회전할 수도 있습니다.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>액세스 키 및 연결 문자열 보기

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure 키 볼트를 사용하여 액세스 키 관리

Azure 키 볼트를 사용하여 액세스 키를 관리하고 회전하는 것이 좋습니다. 응용 프로그램은 키 볼트에서 키에 안전하게 액세스할 수 있으므로 응용 프로그램 코드와 함께 키를 저장하지 않도록 할 수 있습니다. 키 관리에 키 볼트를 사용하는 자세한 내용은 다음 문서를 참조하십시오.

- [Azure 키 자격 증명 모음 및 PowerShell을 통해 저장소 계정 키 관리](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Azure 키 자격 증명 모음 및 Azure CLI를 통해 저장소 계정 키 관리](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>액세스 키를 수동으로 회전

저장소 계정을 안전하게 유지하기 위해 액세스 키를 주기적으로 회전하는 것이 좋습니다. 가능하면 Azure 키 자격 증명 모음을 사용하여 액세스 키를 관리합니다. 키 볼트를 사용하지 않는 경우 키를 수동으로 회전해야 합니다.

키를 회전할 수 있도록 두 개의 액세스 키가 할당됩니다. 두 개의 키가 있으면 응용 프로그램이 프로세스 전체에서 Azure Storage에 대한 액세스를 유지 관리할 수 있습니다.

> [!WARNING]
> 액세스 키를 다시 생성하면 스토리지 계정 키에 종속된 모든 애플리케이션과 Azure 서비스에 영향을 미칠 수 있습니다. 계정 키를 사용하여 스토리지 계정에 액세스하는 모든 클라이언트는 미디어 서비스, 클라우드, 데스크톱 및 모바일 애플리케이션, Azure Storage용 그래픽 사용자 인터페이스 애플리케이션(예: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/))을 포함하여 새로운 키를 사용하도록 업데이트되어야 합니다.

스토리지 계정 키를 회전하려면 다음 프로세스를 따릅니다.

1. 보조 키를 사용 하도록 애플리케이션 코드에서 연결 문자열을 업데이트합니다.
2. 스토리지 계정의 기본 액세스 키를 다시 생성합니다. Azure Portal의 **액세스 키** 블레이드에서 **Key1 다시 생성**을 클릭하고 **예**를 클릭하여 새 키를 생성하려는 것을 확인합니다.
3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
4. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

계정의 액세스 키를 회전하려면 사용자가 서비스 관리자이거나 **Microsoft.Storage/StorageAccounts/다시 생성 키/작업을**포함하는 RBAC 역할을 할당받아야 합니다. 이 작업을 포함하는 일부 기본 제공 RBAC 역할은 **소유자,** **기여자**및 **저장소 계정 키 운영자 서비스 역할** 역할입니다. 서비스 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 역할을](../../role-based-access-control/rbac-and-directory-admin-roles.md)참조하십시오. Azure 저장소에 대 한 기본 제공 RBAC 역할에 대 한 자세한 내용은 [Azure RBAC에 대 한 Azure 기본 제공 역할의](../../role-based-access-control/built-in-roles.md#storage) **저장소** 섹션을 참조 하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 개요](storage-account-overview.md)
- [저장소 계정 만들기](storage-account-create.md)
