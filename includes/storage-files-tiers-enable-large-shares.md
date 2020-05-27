---
title: 파일 포함
description: 포함 파일
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536495"
---
기본적으로, 공유 제한을 100 TiB로 늘릴 수 있지만 표준 파일 공유는 최대 5 개의 TiB 확장할 수 있습니다. 이렇게 하려면 저장소 계정 수준에서 *대량 파일 공유* 기능을 사용 하도록 설정 해야 합니다. Premium storage 계정 (*FileStorage* storage 계정)에는 모든 프리미엄 파일 공유가 전체 100 TiB 용량까지 프로 비전 할 수 있도록 이미 설정 되어 있으므로 대용량 파일 공유 기능 플래그가 없습니다.

로컬 중복 또는 영역 중복 표준 저장소 계정에 대해서만 대량 파일 공유를 사용 하도록 설정할 수 있습니다. 대량 파일 공유 기능 플래그를 사용 하도록 설정 하면 중복 수준을 지역 중복 또는 지역 중복 저장소로 변경할 수 없습니다.

기존 저장소 계정에서 파일 공유를 사용 하도록 설정 하려면 저장소 계정의 목차에서 **구성** 뷰로 이동 하 고 large file share rocker 스위치를 enabled로 전환 합니다.

![Azure Portal에서 large file share 사용 rocker 스위치의 스크린샷](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 및 [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI 명령을 통해 100 TiB 파일 공유를 사용 하도록 설정할 수도 있습니다. 대량 파일 공유 사용에 대 한 자세한 지침은 [대량 파일 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md)를 참조 하세요.

새 저장소 계정에서 파일 공유를 만드는 방법에 대해 자세히 알아보려면 [Azure 파일 공유 만들기](../articles/storage/files/storage-how-to-create-file-share.md)를 참조 하세요.
