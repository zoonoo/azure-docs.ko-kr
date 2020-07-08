---
title: 저장소 동기화 서비스 배포
description: 저장소 동기화 서비스인 Azure File Sync 클라우드 리소스 배포 마이그레이션 문서 전체에서 공유 되는 일반 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143564"
---
이 단계에서는 Azure 구독 자격 증명이 필요 합니다.

Azure File Sync에 대해 구성할 핵심 리소스를 *저장소 동기화 서비스*라고 합니다. 지금 또는 나중에 동일한 파일 집합을 동기화 하는 모든 서버에 대해 하나만 배포 하는 것이 좋습니다. 데이터를 교환 하지 않아야 하는 고유한 서버 집합 (예: 동일한 Azure 파일 공유 동기화)이 있는 경우에만 여러 저장소 동기화 서비스를 만듭니다. 그렇지 않으면 단일 저장소 동기화 서비스를 활용 하는 것이 가장 좋습니다.

사용자의 위치에 가까운 저장소 동기화 서비스에 대 한 Azure 지역을 선택 합니다. 다른 모든 클라우드 리소스는 동일한 지역에 배포 되어야 합니다.
관리를 간소화 하려면 구독에서 동기화 및 저장소 리소스를 보관 하는 새 리소스 그룹을 만듭니다.

자세한 내용은 Azure File Sync 배포에 대 한 문서에서 [저장소 동기화 서비스 배포에 대 한 섹션](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) 을 참조 하세요. 문서의이 부분만 따릅니다. 이후 단계에서 아티클의 다른 섹션에 대 한 링크가 제공 됩니다.