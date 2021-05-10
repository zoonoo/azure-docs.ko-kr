---
title: Azure Data Lake Storage Gen2용 스토리지 계정 만들기
description: Azure Data Lake Storage Gen2에서 사용할 스토리지 계정을 만드는 방법을 알아봅니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624340"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Data Lake Storage Gen2에서 사용할 스토리지 계정 만들기

Data Lake Storage Gen2 기능을 사용하려면 계층 구조 네임스페이스가 있는 스토리지 계정을 만듭니다.

## <a name="choose-a-storage-account-type"></a>스토리지 계정 유형 선택

Data Lake Storage 기능은 다음 유형의 스토리지 계정에서 지원됩니다.

- 범용 v2
- BlockBlobStorage

이러한 항목 중 하나를 선택하는 방법에 대한 자세한 내용은 [스토리지 계정 개요](../common/storage-account-overview.md)를 참조하세요.

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>계층 구조 네임스페이스를 사용하여 스토리지 계정 만들기

**계층 구조 네임스페이스** 설정을 사용하도록 지정하고 [범용 V2 계정](../common/storage-account-create.md) 또는 [BlockBlobStorage](storage-blob-create-account-block-blob.md) 계정을 만듭니다.

**스토리지 계정 만들기** 페이지의 **고급** 탭에서 **계층 구조 네임스페이스** 설정을 사용하도록 지정하여 계정을 만들 때 Data Lake Storage 기능을 잠금 해제합니다. 계정을 만들 때 이 설정을 사용하도록 설정해야 합니다. 나중에 사용하도록 설정할 수 없습니다.

다음 이미지는 **스토리지 계정 만들기** 페이지의 이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Data Lake Storage에서 사용하려는 기존 스토리지 계정이 있고 계층 구조 네임스페이스 설정을 사용하지 않도록 설정한 경우 해당 설정을 사용하도록 지정한 새 스토리지 계정으로 데이터를 마이그레이션해야 합니다.

> [!NOTE]
> **데이터 보호** 및 **계층 구조 네임스페이스** 는 동시에 사용하도록 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](../common/storage-account-overview.md)
- [빅 데이터 요구 사항을 위한 Azure Data Lake Storage Gen2 사용](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)
