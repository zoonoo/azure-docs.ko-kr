---
title: Azure 파일 공유 프로비저닝에 대한 고려 사항입니다.
description: Azure 파일 동기화와 함께 사용할 Azure 파일 공유프로비전합니다. 마이그레이션 문서 간에 공유되는 공통 텍스트 블록입니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209429"
---
Azure 파일 공유는 Azure 저장소 계정의 클라우드에 저장됩니다.
여기에는 또 다른 수준의 성능 고려 사항이 있습니다.

활성 이력이 높은 공유(많은 사용자 및/또는 응용 프로그램에서 사용하는 공유)가 있는 경우 두 개의 Azure 파일 공유가 저장소 계정의 성능 제한에 도달할 수 있습니다.

가장 좋은 방법은 각각 하나의 파일 공유가 있는 저장소 계정을 배포하는 것입니다.
보관 공유가 있거나 일상적인 활동이 낮을 것으로 예상되는 경우 여러 Azure 파일 공유를 동일한 저장소 계정으로 풀을 수 있습니다.

이러한 고려 사항은 Azure 파일 동기화에 적용되는 것보다 Azure VM을 통해 직접 클라우드 액세스에 더 많이 적용됩니다. 이러한 공유에서 Azure File Sync만 사용하려는 경우 여러 개를 단일 Azure 저장소 계정으로 그룹화하는 것은 좋습니다.

공유 목록을 만든 경우 각 공유를 보유한 저장소 계정에 매핑해야 합니다.

이전 단계에서는 적절한 수의 주식을 결정했습니다. 이 단계에서는 파일 공유에 대한 저장소 계정 의 매핑을 만들었습니다. 적절한 수의 Azure 파일 공유를 통해 적절한 수의 Azure 저장소 계정을 배포합니다.

각 저장소 계정의 지역이 동일하고 이미 배포한 저장소 동기화 서비스 리소스의 리전과 일치하는지 확인합니다.

> [!CAUTION]
> 100TiB 제한 Azure 파일 공유를 만드는 경우 해당 공유는 로컬 중복 저장소 또는 영역 중복 저장소 중복 옵션만 사용할 수 있습니다. 100개의 TiB 파일 공유를 사용하기 전에 스토리지 중복 요구 사항을 고려하십시오.

Azure 파일 공유는 기본적으로 5TiB 제한으로 여전히 만들어집니다. 새 저장소 계정을 만드는 않으므로 지침을 따라 [Azure 파일 이 100TiB 제한을 사용하여 Azure 파일 공유를 허용하는 저장소 계정을 만들어야](../articles/storage/files/storage-files-how-to-create-large-file-share.md)합니다.

저장소 계정을 배포할 때 고려해야 할 또 다른 사항은 Azure 저장소의 중복성입니다. 참조: [Azure 저장소 중복 옵션](../articles/storage/common/storage-redundancy.md)입니다.

리소스 이름도 중요합니다. 예를 들어 HR 부서의 여러 공유를 Azure 저장소 계정으로 그룹화하는 경우 저장소 계정의 이름을 적절하게 지정해야 합니다. 마찬가지로 Azure 파일 공유의 이름을 지정할 때 온-프레미스 에 사용되는 이름과 유사한 이름을 사용해야 합니다.