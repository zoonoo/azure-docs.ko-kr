---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081044"
---
Azure 파일 공유는 Azure 스토리지 계정의 클라우드에 저장됩니다.
여기에는 또 다른 수준의 성능 고려 사항이 있습니다.

많은 사용자와 애플리케이션이 사용하는 공유와 같이 매우 활성화된 공유가 있는 경우 2개의 Azure 파일 공유는 스토리지 계정의 성능 한도에 도달할 수 있습니다.

가장 좋은 방법은 각각 파일 공유가 하나씩 있는 스토리지 계정을 배포하는 것입니다.
보관 공유가 있거나 일상적 활동이 적을 것으로 예상하는 경우 여러 Azure 파일 공유를 동일한 스토리지 계정에 풀링할 수 있습니다.

이러한 고려 사항은 Azure 파일 동기화보다는 Azure VM을 통한 직접 클라우드 액세스에 더 많이 적용됩니다. 이러한 공유에서만 Azure 파일 동기화를 사용하려는 경우 여러 공유를 Azure 스토리지 계정으로 그룹화해도 좋습니다.

공유 목록을 만들었으면 각 공유를 공유가 상주할 스토리지 계정에 매핑해야 합니다.

이전 단계에서는 적절한 공유 수를 결정했습니다. 이 단계에서는 파일 공유에 대한 스토리지 계정 매핑을 만들었습니다. 이제 적절한 수의 Azure 파일 공유가 포함된 적절한 수의 Azure 스토리지 계정을 배포합니다.

각 스토리지 계정의 지역이 동일하고 이미 배포한 Storage Sync Service 리소스의 지역과 일치해야 합니다.

> [!CAUTION]
> 100TiB 한도가 있는 Azure 파일 공유를 만드는 경우 해당 공유는 로컬 중복 스토리지 또는 영역 중복 스토리지 중복도 옵션만 사용할 수 있습니다. 100TiB 파일 공유를 사용하려면 먼저 스토리지 중복도 요구 사항을 고려하세요.

Azure 파일 공유는 기본적으로 여전히 5TiB 한도로 생성됩니다. 새 스토리지 계정을 만드는 중이므로 [100TiB 한도의 Azure 파일 공유를 허용하는 스토리지 계정 만들기 지침](../articles/storage/files/storage-files-how-to-create-large-file-share.md)에 따라야 합니다.

스토리지 계정을 배포할 때 또 다른 고려 사항은 Azure Storage의 중복도입니다. [Azure Storage 중복도 옵션](../articles/storage/common/storage-redundancy.md)을 참조하세요.

리소스의 이름도 중요합니다. 예를 들어 HR 부서의 여러 공유를 Azure 스토리지 계정 하나로 그룹화하는 경우 스토리지 계정의 이름을 적절하게 지정해야 합니다. 마찬가지로 Azure 파일 공유의 이름을 지정할 때 온-프레미스 파일 공유에 사용되는 이름과 비슷한 이름을 사용해야 합니다.