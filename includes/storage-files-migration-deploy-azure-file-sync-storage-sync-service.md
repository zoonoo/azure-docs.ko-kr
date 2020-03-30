---
title: 스토리지 동기화 서비스 배포
description: Azure 파일 동기화 클라우드 리소스 배포. 스토리지 동기화 서비스입니다. 마이그레이션 문서 간에 공유되는 공통 텍스트 블록입니다.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124737"
---
이 단계에서는 Azure 구독 자격 증명이 필요합니다.

Azure 파일 동기화를 구성하는 핵심 리소스를 "저장소 동기화 서비스"라고 합니다.
현재 또는 미래에 동일한 파일 집합을 동기화하는 회사의 모든 서버에 대해 하나만 배포하는 것이 좋습니다. 데이터를 교환하지 않아야 하는 고유한 서버 집합이 있는 경우에만 여러 Storage Sync 서비스를 만듭니다. (예: 동일한 Azure 파일 공유를 동기화). 그렇지 않으면 단일 저장소 동기화 서비스가 가장 좋습니다.

사무실 위치와 가까운 저장소 동기화 서비스에 대한 Azure 지역을 선택합니다. 다른 모든 클라우드 리소스는 동일한 지역에 배포해야 합니다.
관리를 간소화하려면 동기화 및 저장소 리소스를 포함하는 구독에 새 리소스 그룹을 만듭니다.

다음 문서에서는 저장소 동기화 서비스를 배포 하는 방법을 설명 합니다. 문서의 이 부분만 따르십시오. 이후 단계에서이 문서의 다른 하위 섹션에 대 한 링크가 있을 것입니다.

[스토리지 동기화 서비스를 배포하는 방법을 알아봅니다.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
