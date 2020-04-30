---
title: Azure 파일 공유를 프로 비전 할 때의 고려 사항
description: Azure File Sync와 함께 사용 하기 위해 Azure 파일 공유를 프로 비전 합니다. 마이그레이션 문서 전체에서 공유 되는 일반 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143622"
---
Azure 파일 공유는 Azure storage 계정의 클라우드에 저장 됩니다.
여기에는 다른 수준의 성능 고려 사항이 있습니다.

많은 사용자 및/또는 응용 프로그램에서 사용 하는 공유를 사용 하는 경우, 두 개의 Azure 파일 공유가 저장소 계정의 성능 제한에 도달할 수 있습니다.

각각 하나의 파일 공유를 사용 하 여 저장소 계정을 배포 하는 것이 가장 좋습니다.
여러 Azure 파일 공유를 동일한 저장소 계정으로 풀링할 수 있습니다. 보관 공유가 있거나 해당 위치에서 일상적인 작업을 필요로 하는 경우입니다.

이러한 고려 사항은 Azure File Sync 하는 것 보다 Azure VM을 통해 직접 클라우드 액세스에 적용 됩니다. 이러한 공유에만 Azure File Sync를 사용 하려는 경우 여러 개의 Azure storage 계정으로 그룹화 하는 것이 좋습니다.

공유의 목록을 만든 경우 각 공유를 상주할 저장소 계정에 매핑해야 합니다.

이전 단계에서는 적절 한 수의 공유를 결정 했습니다. 이 단계에서는 파일 공유에 대 한 저장소 계정 매핑을 만들었습니다. 이제 적절 한 수의 azure 파일 공유가 포함 된 적절 한 수의 Azure storage 계정을 배포 합니다.

각 저장소 계정의 지역이 동일 하 고 이미 배포한 저장소 동기화 서비스 리소스의 지역과 일치 하는지 확인 합니다.

> [!CAUTION]
> 100-TiB 한도가 있는 Azure 파일 공유를 만드는 경우 해당 공유는 로컬 중복 저장소 또는 영역 중복 저장소 중복성 옵션만 사용할 수 있습니다. 100-TiB 파일 공유를 사용 하려면 먼저 저장소 중복성 요구 사항을 고려 하세요.

Azure 파일 공유는 기본적으로 5 TiB 한도로 생성 됩니다. 새 저장소 계정을 만드는 중 이므로 지침에 따라 [Azure 파일 공유를 100-TiB 제한으로 허용 하는 저장소 계정을 만들어야](../articles/storage/files/storage-files-how-to-create-large-file-share.md)합니다.

저장소 계정을 배포 하는 경우의 또 다른 고려 사항은 Azure Storage의 중복성입니다. [Azure Storage 중복 옵션](../articles/storage/common/storage-redundancy.md)을 참조 하세요.

또한 리소스의 이름도 중요 합니다. 예를 들어 HR 부서의 여러 공유를 Azure storage 계정으로 그룹화 하는 경우 저장소 계정의 이름을 적절 하 게 지정 해야 합니다. 마찬가지로, Azure 파일 공유의 이름을 지정 하는 경우 온-프레미스 대응에 사용 되는 이름과 유사한 이름을 사용 해야 합니다.