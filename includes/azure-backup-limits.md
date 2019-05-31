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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238305"
---
Azure Backup에는 다음과 같은 제한이 적용됩니다.

| **제한** | **기본값** |
| --- | --- |
| 서버 또는 컴퓨터 자격 증명 모음에 등록할 수 있습니다. | Windows Server/Windows 클라이언트/System Center Data Protection Manager: 50. <br/><br/> IaaS VM: 1,000.  |
| 자격 증명 모음 저장소에 데이터 원본의 크기입니다. |최대 54,400 GB입니다. IaaS VM 백업 제한 적용 되지 않습니다. |
| Azure 구독에서 백업 자격 증명 모음입니다. |지역당 자격 증명 모음 500입니다. |
| 매일 백업을 예약 합니다. |Windows Server/클라이언트: 하루 3입니다.<br/> System Center DPM: 하루 두입니다. <br/> IaaS VM: 하루에 한 번입니다.  |
| 데이터 디스크 백업에 대 한 Azure VM에 연결 합니다. | 16 |
| 개별 데이터 디스크 백업에 대 한 Azure VM에 연결 합니다.| 4,095GB|
