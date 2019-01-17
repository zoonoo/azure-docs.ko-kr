---
title: 포함 파일
description: 포함 파일
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300699"
---
Azure Backup에는 다음과 같은 제한이 적용됩니다.

| **제한** | **기본값** |
| --- | --- |
| 자격 증명 모음에 등록할 수 있는 서버/머신입니다. | Windows Server/Windows 클라이언트/System Center DPM: 50 <br/><br/> IaaS VM: 1000  |
| 자격 증명 모음 스토리지의 데이터 원본 크기 |최대 54400GB. IaaS VM 백업에는 제한이 적용되지 않습니다. |
| Azure 구독의 백업 자격 증명 모음 |Azure 지역당 500개 자격 증명 모음 |
| 매일 백업 예약 |Windows Server/클라이언트: 하루 세 번<br/> System Center DPM: 하루 두 번 <br/> IaaS VM: 하루 한 번  |
| 백업을 위해 Azure VM에 연결된 데이터 디스크 | 16 |
| 백업을 위해 Azure VM에 연결된 개별 데이터 디스크| 4,095GB|
