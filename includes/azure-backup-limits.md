---
title: 포함 파일
description: 포함 파일
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829110"
---
Azure Backup에는 다음과 같은 제한이 적용됩니다.

| **제한** | **기본값** |
| --- | --- |
| 자격 증명 모음에 등록할 수 있는 서버 또는 컴퓨터 | Windows Server/Windows 클라이언트/System Center Data Protection Manager: 50. <br/><br/> IaaS Vm: 1000.  |
| 자격 증명 모음 저장소에 있는 데이터 원본의 크기입니다. |54400-GB의 최대값 이 제한은 IaaS VM 백업에 적용 되지 않습니다. |
| Azure 구독의 백업 자격 증명 모음. |500 지역 당 자격 증명 모음 |
| 매일 백업을 예약 합니다. |Windows Server/Client: 3 일<br/> System Center DPM: 하루 2 일 <br/> IaaS Vm: 하루에 한 번  |
| 백업을 위해 Azure VM에 연결 된 데이터 디스크입니다. | 16 |
| 백업을 위해 Azure VM에 연결 된 개별 데이터 디스크입니다.| 32TB|
