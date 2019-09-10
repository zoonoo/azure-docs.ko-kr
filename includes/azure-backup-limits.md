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
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "67182603"
---
Azure Backup에는 다음과 같은 제한이 적용됩니다.

| **제한** | **Default** |
| --- | --- |
| 자격 증명 모음에 등록할 수 있는 서버 또는 컴퓨터 | Windows Server/Windows 클라이언트/시스템 센터 Data Protection Manager: 50. <br/><br/> IaaS VM: 1000.  |
| 자격 증명 모음 저장소에 있는 데이터 원본의 크기입니다. |54400-GB의 최대값 이 제한은 IaaS VM 백업에 적용 되지 않습니다. |
| Azure 구독의 백업 자격 증명 모음. |500 지역 당 자격 증명 모음 |
| 매일 백업을 예약 합니다. |Windows Server/클라이언트: 3 일<br/> System Center DPM: 하루에 두 번 <br/> IaaS VM: 하루에 한 번  |
| 백업을 위해 Azure VM에 연결 된 데이터 디스크입니다. | 16 |
| 백업을 위해 Azure VM에 연결 된 개별 데이터 디스크입니다.| 4095 GB|
