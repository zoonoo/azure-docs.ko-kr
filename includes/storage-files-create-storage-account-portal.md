---
title: storage-files-create-storage-account-portal
description: Azure Files에 대한 스토리지 계정 만들기.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717838"
---
스토리지 계정은 Azure 파일 공유 또는 Blob나 큐와 같은 다른 스토리지 리소스를 배포할 수 있는 스토리지의 공유 풀입니다. 스토리지 계정에 포함될 수 있는 공유 수에는 제한이 없습니다. 공유에 저장할 수 있는 파일 수에는 제한이 없으며, 스토리지 계정의 최대 용량까지 저장할 수 있습니다.

스토리지 계정을 만들려면

1. 왼쪽 메뉴에서 **+** 를 선택하여 리소스를 만듭니다.
1. **스토리지 계정** 을 선택하여 스토리지 계정을 만듭니다.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="리소스 만들기 블레이드의 스토리지 계정 옵션 스크린샷." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. **이름** 에 *mystorageacct* 를 입력하고 고유 이름임을 나타내는 녹색 확인 표시가 표시될 때까지 이름 뒤에 임의의 숫자를 붙입니다. 스토리지 계정 이름은 모두 소문자여야 하며 전역적으로 고유해야 합니다. 스토리지 계정 이름을 적어둡니다. 나중에 필요합니다. 
1. **성능** 에서 **표준** 기본값을 유지합니다.
1. **복제** 에서 **LRS(로컬 중복 스토리지)** 를 선택합니다.
1. **구독** 에서 스토리지 계정을 만들 때 사용된 구독을 선택합니다. 구독이 하나밖에 없는 경우 해당 구독이 기본 구독이어야 합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택합니다. 이름으로 *myResourceGroup* 을 입력합니다.
1. **위치** 에서 **미국 동부** 를 선택합니다.
1. 작업을 마쳤으면 **만들기** 를 선택하여 배포를 시작합니다.