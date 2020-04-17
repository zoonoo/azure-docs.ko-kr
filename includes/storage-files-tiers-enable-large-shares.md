---
title: 포함 파일
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536495"
---
기본적으로 표준 파일 공유는 최대 5TiB까지만 사용할 수 있지만 공유 한도는 100 TiB로 늘릴 수 있습니다. 이렇게 하려면 저장소 계정 수준에서 *대용량 파일 공유* 기능을 사용하도록 설정해야 합니다. 모든 프리미엄 파일 공유가 이미 전체 100 TiB 용량까지 프로비저닝할 수 있도록 설정되어 있기 때문에 프리미엄 저장소*계정(FileStorage* Storage storage 계정)에는 대용량 파일 공유 기능 플래그가 없습니다.

로컬 중복 또는 영역 중복 표준 저장소 계정에서만 대용량 파일 공유를 사용하도록 설정할 수 있습니다. 큰 파일 공유 피쳐 플래그를 사용하도록 설정한 후에는 중복 수준을 지리적 중복 또는 지역 영역 중복 저장소로 변경할 수 없습니다.

기존 저장소 계정에서 대용량 파일 공유를 사용하려면 저장소 계정의 내용 테이블의 **구성** 보기로 이동한 다음 대용량 파일 공유 로커 스위치를 사용하도록 설정합니다.

![Azure 포털에서 대용량 파일 공유 로커 스위치 사용의 스크린샷](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 및 Azure CLI 명령을 통해 100개의 TiB 파일 공유를 활성화할 [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) 수도 있습니다. 대용량 파일 공유 를 사용하도록 설정하는 방법에 대한 자세한 지침은 [큰 파일 공유 를 활성화하고 생성하십시오.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

새 저장소 계정에 파일 공유를 만드는 방법에 대해 자세히 알아보려면 [Azure 파일 공유 만들기를](../articles/storage/files/storage-how-to-create-file-share.md)참조하세요.
