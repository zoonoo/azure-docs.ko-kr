---
title: Azure Backup 에이전트 업그레이드
description: 이 정보는 Azure 백업 에이전트를 업그레이드해야 하는 이유와 업그레이드를 다운로드할 위치를 설명합니다.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673209"
---
## <a name="upgrade-the-mars-agent"></a>화성 에이전트 업그레이드

2.0.9083.0 미만의 Microsoft Azure 복구 서비스(MARS) 에이전트 버전은 Azure 액세스 제어 서비스에 종속됩니다. MARS 에이전트를 Azure 백업 에이전트라고도 합니다.

2018년에 [Microsoft는 Azure 액세스 제어 서비스를 더 이상 사용되지](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)않습니다. 2018년 3월 19일부터 2.0.9083.0 미만의 모든 MARS 에이전트 버전에서는 백업 오류가 발생합니다. 백업 오류를 방지하거나 해결하려면 [MARS 에이전트를 최신 버전으로 업그레이드합니다.](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent) MARS 에이전트 업그레이드가 필요한 서버를 식별하려면 [MICROSOFT Azure 복구 서비스(MARS) 에이전트 업그레이드](../articles/backup/upgrade-mars-agent.md)의 단계를 따릅니다.

MARS 에이전트는 파일 및 폴더 및 시스템 상태 데이터를 Azure에 백업하는 데 사용됩니다. 시스템 센터 DPM 및 Azure 백업 서버는 MARS 에이전트를 사용하여 데이터를 Azure에 백업합니다.
