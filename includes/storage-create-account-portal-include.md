---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bed8475e5d6c7bf26003672b6cf9ce51a82384ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376381"
---
Azure Portal에서 범용 v2 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal 메뉴에서 **모든 서비스**를 선택합니다. 리소스 목록에 **스토리지 계정**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Storage 계정**을 선택합니다.
1. 나타나는 **Storage 계정** 창에서 **추가**를 선택합니다.
1. **기본 사항** 탭에서 스토리지 계정을 만들 구독을 선택합니다.
1. **리소스 그룹** 필드 아래에서 원하는 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.  Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../articles/azure-resource-manager/resource-group-overview.md)를 참조하세요.
1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름은 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.
1. 성능 계층을 선택합니다. 기본 계층은 *표준*입니다.
1. **계정 종류** 필드를 *Storage V2(범용 v2)* 로 설정합니다.
1. 스토리지 계정이 복제되는 방법을 지정합니다. 기본 복제 옵션은 *RA-GRS(읽기 액세스 지역 중복 스토리지)* 입니다. 사용 가능한 복제 옵션에 대한 자세한 내용은 [Azure Storage 중복성](../articles/storage/common/storage-redundancy.md)을 참조하세요.
1. 스토리지 계정의 Blob에 대한 액세스 계층을 지정합니다. 기본 계층은 *핫*입니다. Blob 계층에 대한 자세한 내용은 [Blob에 대한 핫, 쿨 및 보관 액세스 계층](../articles/storage/blobs/storage-blob-storage-tiers.md)을 참조하세요.
1. Azure Data Lake Storage를 사용하려면 **고급** 탭을 선택한 다음, **계층 구조 네임스페이스**를 **사용**으로 설정합니다. 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../articles/storage/blobs/data-lake-storage-introduction.md)를 참조하세요.
1. **검토 + 만들기**를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
1. **만들기**를 선택합니다.

다음 이미지에서는 새 스토리지 계정에 대한 **기본 사항** 탭의 설정을 보여 줍니다.

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Azure Portal에서 스토리지 계정을 만드는 방법을 보여주는 스크린샷":::
