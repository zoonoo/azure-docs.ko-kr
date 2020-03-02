---
title: 저장소 동기화 서비스 배포
description: Azure File Sync 클라우드 리소스 배포 저장소 동기화 서비스입니다. 마이그레이션 문서 사이에 공유 되는 공통 텍스트 블록
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209455"
---
이 단계에서는 Azure 구독 자격 증명이 필요 합니다. 사용 하는 Azure 구독은 StorSimple에 사용 하는 것과 다를 수 있습니다.

Azure File Sync 구성할 핵심 리소스를 "저장소 동기화 서비스" 라고 합니다.
지금 또는 나중에 동일한 파일 집합을 동기화 하는 회사의 모든 서버에 대해 하나만 배포 하는 것이 좋습니다. StorSimple 어플라이언스가 둘 이상 있는 경우 각 장치에 대해 저장소 동기화 서비스 리소스를 만드는 것을 고려할 수 있습니다. 그러나 데이터를 교환 하지 않아야 하는 고유한 서버 집합이 있는 경우에는 저장소 동기화 서비스를 여러 개 만들어야 합니다. 그렇지 않으면 단일 저장소 동기화 서비스를 활용 하는 것이 가장 좋습니다.

사무실 위치에 가까운 저장소 동기화 서비스에 대 한 Azure 지역을 선택 합니다. 다른 모든 클라우드 리소스는 동일한 지역에 배포 되어야 합니다.
보다 쉽게 관리할 수 있도록 동기화 및 저장소 리소스를 저장 하기 위해 구독에 새 리소스 그룹을 만드는 것이 가장 좋습니다.

다음 문서에서는 저장소 동기화 서비스를 배포 하는 방법을 설명 합니다. 문서의이 부분을 따릅니다. 이후 단계에서이 문서의 다른 하위 섹션에 대 한 링크가 제공 됩니다.

[저장소 동기화 서비스를 배포 하는 방법을 알아봅니다.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
