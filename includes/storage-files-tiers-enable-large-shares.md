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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81536495"
---
기본적으로, 공유 한도를 100TiB로 늘릴 수 있지만 표준 파일 공유는 최대 5TiB까지만 확장할 수 있습니다. 이렇게 하려면 스토리지 계정 수준에서 *대용량 파일 공유* 기능을 사용하도록 설정해야 합니다. 프리미엄 스토리지 계정(*FileStorage* 스토리지 계정)에는 최대 100TiB 용량을 프로비저닝하기 위해 모든 프리미엄 파일 공유를 이미 사용하도록 설정했기 때문에 대용량 파일 공유 기능 플래그가 없습니다.

로컬 중복 또는 영역 중복 표준 스토리지 계정에서만 대용량 파일 공유를 사용할 수 있습니다. 대용량 파일 공유 기능 플래그를 사용하면 중복 수준을 지역 중복 또는 지역 영역 중복 스토리지로 변경할 수 없습니다.

기존 스토리지 계정에서 대용량 파일 공유를 사용하려면 스토리지 계정의 목차에 있는 **구성** 보기로 이동한 다음, 대용량 파일 공유 로커 스위치를 사용하도록 전환합니다.

![Azure Portal에서 대용량 파일 공유 로커 스위치 사용의 스크린샷](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

[`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet 및 [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure CLI 명령을 통해 100TiB 파일 공유를 사용할 수도 있습니다. 대용량 파일 공유를 사용하는 방법에 대한 자세한 지침은 [대용량 파일 공유 사용 및 만들기](../articles/storage/files/storage-files-how-to-create-large-file-share.md)를 참조하세요.

새로운 스토리지 계정에서 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure 파일 공유 만들기](../articles/storage/files/storage-how-to-create-file-share.md)를 참조하세요.
