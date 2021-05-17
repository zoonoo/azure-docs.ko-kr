---
title: Azure Backup 에이전트 업그레이드
description: 이 정보는 Azure Backup 에이전트를 업그레이드해야 하는 이유와 업그레이드를 다운로드할 수 있는 위치를 설명합니다.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294154"
---
## <a name="upgrade-the-mars-agent"></a>MARS 에이전트 업그레이드

2\.0.9083.0 미만의 MARS(Microsoft Azure Recovery Service) 에이전트 버전은 Azure Access Control Service에 종속됩니다. MARS 에이전트는 Azure Backup 에이전트라고도 합니다.

2018년에 Microsoft는 [Azure Access Control Service 사용을 중지했습니다](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). 2018년 3월 19일부터는 2.0.9083.0 미만의 모든 MARS 에이전트 버전에서 백업이 실패합니다. 백업 실패를 방지하거나 해결하려면 [MARS 에이전트를 최신 버전으로 업그레이드](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)하세요. MARS 에이전트를 업그레이드해야 하는 서버를 식별하려면 [MARS(Microsoft Azure Recovery Services) 에이전트 업그레이드](../articles/backup/upgrade-mars-agent.md)의 단계를 따르세요.

MARS 에이전트는 파일 및 폴더, 시스템 상태 데이터를 Azure에 백업하는 데 사용됩니다. System Center DPM 및 Azure Backup Server는 MARS 에이전트를 사용하여 Azure로 데이터를 백업합니다.
