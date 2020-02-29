---
title: Azure Backup 에이전트 업그레이드
description: 이 정보는 Azure Backup 에이전트를 업그레이드 해야 하는 이유와 업그레이드를 다운로드 하는 위치에 대해 설명 합니다.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197126"
---
## <a name="upgrade-the-mars-agent"></a>MARS 에이전트 업그레이드

2\.0.9083.0 아래의 MARS (MARS) 에이전트 Microsoft Azure Recovery Services 버전은 Azure Access Control 서비스에 종속 되어 있습니다. MARS 에이전트를 Azure Backup 에이전트 라고도 합니다.

2018에서는 Microsoft가 [Azure Access Control 서비스를 사용 하지 않습니다](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018 년 3 월 19 일부 터 2.0.9083.0 아래의 모든 MARS 에이전트 버전은 백업 오류가 발생 합니다. 백업 실패를 방지하거나 해결하려면 [MARS 에이전트를 최신 버전으로 업그레이드](https://go.microsoft.com/fwlink/?linkid=229525)하세요. MARS 에이전트를 업그레이드 해야 하는 서버를 식별 하려면 [Mars 에이전트 업그레이드를 위한 백업 블로그의](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)단계를 따르세요.

MARS 에이전트는 파일 및 폴더와 시스템 상태 데이터를 Azure에 백업 하는 데 사용 됩니다. System Center DPM 및 Azure Backup Server MARS 에이전트를 사용 하 여 Azure에 데이터를 백업 합니다.
