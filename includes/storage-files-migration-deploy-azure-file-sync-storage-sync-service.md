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
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075595"
---
이 단계에서는 Azure 구독 자격 증명이 필요합니다.

Azure 파일 동기화에 대해 구성할 핵심 리소스를 '스토리지 동기화 서비스'라고 합니다. 지금 또는 나중에 동일한 파일 집합을 동기화하는 모든 서버에 대해 하나만 배포하는 것이 좋습니다. 데이터를 교환하면 안 되는 고유한 서버 집합이 있는 경우에만 스토리지 동기화 서비스를 여러 개 만듭니다. 예를 들어 동일한 Azure 파일 공유를 동기화하면 안 되는 서버들이 있을 수 있습니다. 그렇지 않은 경우에는 단일 스토리지 동기화 서비스를 활용하는 것이 가장 좋습니다.

스토리지 동기화 서비스에는 사용자의 위치에 가까운 Azure 지역을 선택합니다. 다른 모든 클라우드 리소스는 동일한 지역에 배포해야 합니다. 관리를 간소화하려면 구독에서 동기화 및 스토리지 리소스를 보관하는 새 리소스 그룹을 만듭니다.

자세한 내용은 Azure 파일 동기화 배포에 대한 문서에서 [스토리지 동기화 서비스 배포에 관한 섹션](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)을 참조하세요. 문서의 이 부분만 따르세요. 이후 단계에서는 문서의 다른 섹션에 대한 링크가 제공됩니다.