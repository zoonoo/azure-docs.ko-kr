---
title: Azure Media Services 계정 만들기
description: 이 자습서에서는 Azure Media Services 계정을 만드는 단계를 안내합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9234b27e2f08e65f569393bde342cba3f37adee
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963821"
---
# <a name="create-a-media-services-account"></a>Media Services 계정 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure에서 암호화, 인코딩, 분석, 관리 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정은 하나 이상의 스토리지 계정과 연결되어야 합니다. 이 문서에서는 새로운 Azure Media Services 계정을 만드는 단계를 설명합니다.

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 Azure Portal 또는 CLI를 사용하여 Media Services 계정을 만들 수 있습니다. 사용하려는 방법에 대한 탭을 선택합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[포털](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Azure 구독 설정

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)