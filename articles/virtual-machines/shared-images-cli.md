---
title: Azure CLI를 사용 하 여 공유 이미지 갤러리 만들기
description: 이 문서에서는 Azure CLI를 사용하여 Azure에서 VM의 공유 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff50f61fe159c518d488da9f9911e40f1ddb8ca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501460"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Azure CLI를 사용 하 여 공유 이미지 갤러리 만들기

[공유 이미지 갤러리](./linux/shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

Shared Image Gallery를 사용하면 사용자 지정 VM 이미지를 다른 사용자와 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>다음 단계

Azure CLI를 사용 하 여 [VM](image-version-vm-cli.md)또는 [관리 되는 이미지](image-version-managed-image-cli.md) 에서 이미지 버전을 만듭니다.

공유 이미지 갤러리에 대한 자세한 내용은 [개요](./linux/shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.
