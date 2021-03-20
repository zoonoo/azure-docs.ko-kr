---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99569560"
---
기본적으로, 표준 파일 공유는 최대 5TiB까지만 확장할 수 있지만 공유 한도는 100TiB까지 늘릴 수 있습니다. 공유 한도를 늘리려면 스토리지 계정에서 **대용량 파일 공유** 를 사용합니다. 프리미엄 스토리지 계정(*FileStorage* 스토리지 계정)에는 최대 100TiB 용량을 프로비저닝하기 위해 모든 프리미엄 파일 공유를 이미 사용하도록 설정했기 때문에 대용량 파일 공유 기능 플래그가 없습니다.

로컬 중복 또는 영역 중복 표준 스토리지 계정에서만 대용량 파일 공유를 사용할 수 있습니다. 대용량 파일 공유 기능 플래그를 사용하면 중복 수준을 지역 중복 또는 지역 영역 중복 스토리지로 변경할 수 없습니다.

기존 스토리지 계정에 대한 대용량 파일 공유를 사용하려면 스토리지 계정의 목차에서 **파일 공유** 로 이동합니다.
이 블레이드에서 **용량 공유** 를 선택하고, 공유 용량을 **100TiB** 로 변경하고 **저장** 을 선택합니다.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Azure Portal에서 대용량 파일 공유 옵트인 설정의 스크린샷" lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

[`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 및 [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI 명령을 통해 100TiB 파일 공유를 사용할 수도 있습니다. 대용량 파일 공유를 사용하는 방법에 대한 자세한 지침은 [대용량 파일 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md)를 참조하세요.

새로운 스토리지 계정에서 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure 파일 공유 만들기](../articles/storage/files/storage-how-to-create-file-share.md)를 참조하세요.