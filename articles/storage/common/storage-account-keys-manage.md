---
title: 계정 선택키 관리
titleSuffix: Azure Storage
description: 저장소 계정 액세스 키를 보고 관리 하 고 회전 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 13adf6de420b54299d04a226dab81e75cbb9fef2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975777"
---
# <a name="manage-storage-account-access-keys"></a>저장소 계정 액세스 키 관리

스토리지 계정을 만들 때 Azure는 두 개의 512비트 스토리지 계정 액세스 키를 생성합니다. 이러한 키를 사용 하 여 공유 키 권한 부여를 통해 저장소 계정의 데이터에 대 한 액세스 권한을 부여할 수 있습니다.

Azure Key Vault를 사용 하 여 액세스 키를 관리 하 고 키를 정기적으로 회전 하 고 다시 생성 하는 것이 좋습니다. Azure Key Vault를 사용 하면 응용 프로그램을 중단 하지 않고 쉽게 키를 회전할 수 있습니다. 키를 수동으로 회전할 수도 있습니다.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>액세스 키 및 연결 문자열 보기

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault를 사용 하 여 액세스 키 관리

Azure Key Vault 사용 하 여 액세스 키를 관리 하 고 회전 하는 것이 좋습니다. 응용 프로그램은 Key Vault의 키에 안전 하 게 액세스할 수 있으므로 응용 프로그램 코드를 사용 하 여 해당 키를 저장 하지 않을 수 있습니다. 키 관리에 Key Vault를 사용 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Key Vault 및 PowerShell을 사용 하 여 저장소 계정 키 관리](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키 관리](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>수동으로 액세스 키 회전

저장소 계정을 안전 하 게 유지 하기 위해 액세스 키를 정기적으로 회전 하는 것이 좋습니다. 가능 하면 Azure Key Vault를 사용 하 여 액세스 키를 관리 합니다. Key Vault 사용 하지 않는 경우 수동으로 키를 회전 해야 합니다.

키를 회전할 수 있도록 두 개의 액세스 키가 할당됩니다. 두 개의 키가 있으면 응용 프로그램이 프로세스 전체에서 Azure Storage에 대 한 액세스를 유지 관리할 수 있습니다.

> [!WARNING]
> 액세스 키를 다시 생성하면 스토리지 계정 키에 종속된 모든 애플리케이션과 Azure 서비스에 영향을 미칠 수 있습니다. 계정 키를 사용하여 스토리지 계정에 액세스하는 모든 클라이언트는 미디어 서비스, 클라우드, 데스크톱 및 모바일 애플리케이션, Azure Storage용 그래픽 사용자 인터페이스 애플리케이션(예: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/))을 포함하여 새로운 키를 사용하도록 업데이트되어야 합니다.

스토리지 계정 키를 회전하려면 다음 프로세스를 따릅니다.

1. 보조 키를 사용 하도록 애플리케이션 코드에서 연결 문자열을 업데이트합니다.
2. 스토리지 계정의 기본 액세스 키를 다시 생성합니다. Azure Portal의 **액세스 키** 블레이드에서 **Key1 다시 생성**을 클릭하고 **예**를 클릭하여 새 키를 생성하려는 것을 확인합니다.
3. 새 기본 액세스 키를 참조하도록 코드의 연결 문자열을 업데이트합니다.
4. 같은 방식으로 보조 액세스 키를 다시 생성합니다.

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 애플리케이션이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
