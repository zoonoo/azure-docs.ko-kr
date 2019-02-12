---
title: Microsoft Azure Stack 개발 키트 릴리스 정보 | Microsoft Docs
description: 향상 된 기능, 수정 및 Azure Stack 개발 키트에 대 한 알려진된 문제입니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 485d88a4765d7cedcb171a5b325fe5f366fff1f9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004775"
---
# <a name="asdk-release-notes"></a>ASDK 릴리스 정보

이 문서는 변경 내용, 수정 및 알려진된 문제에는 Azure Stack 개발 키트 ASDK ()에 대 한 정보를 제공합니다. 실행 중인 버전을 잘 모를 경우 [포털을 사용 하 여 확인](../azure-stack-updates.md#determine-the-current-version)합니다.

에서 새로운 기능을 ASDK 구독 하 여 최신 합니다 [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [피드](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)합니다.

## <a name="build-11901095"></a>1.1901.0.95 빌드

### <a name="changes"></a>변경 내용

이 빌드에는 Azure Stack에 대 한 다음과 같은 개선 사항이 포함 됩니다.

- BGP 및 NAT 구성 요소가 이제는 실제 호스트에 배포 됩니다. 이 ASDK를 배포 하기 위한 두 개의 공용 또는 회사 IP 주소를 가질 필요가 없습니다 하며 또한 배포를 간소화 합니다.
- Azure Stack 통합 시스템 백업을 수행할 수 [검사할](asdk-validate-backup.md) 사용 하 여 합니다 **asdk installer.ps1** PowerShell 스크립트입니다.

### <a name="new-features"></a>새로운 기능

- 이 릴리스의 새로운 기능 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1901.md#new-features) Azure Stack의 릴리스 정보입니다.

### <a name="fixed-and-known-issues"></a>고정 및 알려진 문제

- 이 릴리스에서 해결 된 문제 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1901.md#fixed-issues) Azure Stack의 릴리스 정보입니다. 알려진된 문제 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1901.md#known-issues-post-installation)합니다.
- 사실은 [사용할 수 있는 Azure Stack 핫픽스](../azure-stack-update-1901.md#azure-stack-hotfixes) Azure Stack ASDK에 적용 되지 않습니다.

## <a name="build-118110101"></a>1.1811.0.101 빌드

### <a name="changes"></a>변경 내용

이 빌드에는 Azure Stack에 대 한 다음과 같은 개선 사항이 포함 됩니다.  

- 새 최소 및 권장 하드웨어 및 소프트웨어 요구 사항은 ASDK 집합이 있습니다. 에 설명 된 새 이러한 권장 사양을 [Azure Stack 배포의 계획 고려 사항](asdk-deploy-considerations.md)합니다. Azure Stack 플랫폼에 발전 함에 따라 더 많은 서비스 제공 됩니다.으로 더 많은 리소스가 필요할 수 있습니다. 향상 된 사양은 이러한 수정 된 권장 사항을 반영합니다.

### <a name="new-features"></a>새로운 기능

이 릴리스의 새로운 기능 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1811.md#new-features) Azure Stack의 릴리스 정보입니다.

### <a name="fixed-and-known-issues"></a>고정 및 알려진 문제

이 릴리스에서 해결 된 문제 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1811.md#fixed-issues) Azure Stack의 릴리스 정보입니다. 알려진된 문제 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1811.md#known-issues-post-installation)합니다.

## <a name="build-11809090"></a>1.1809.0.90 빌드

### <a name="new-features"></a>새로운 기능

이 릴리스의 새로운 기능 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1809.md#new-features) Azure Stack의 릴리스 정보입니다.

### <a name="fixed-issues"></a>해결된 문제

이 릴리스에서 해결 된 문제 목록을 참조 하세요 [이 섹션에서는](../azure-stack-update-1809.md#fixed-issues)합니다.

### <a name="known-issues"></a>알려진 문제

이 릴리스의 알려진된 문제 목록은 참조 하세요 [이 섹션에서는](../azure-stack-update-1809.md#known-issues-post-installation)합니다.