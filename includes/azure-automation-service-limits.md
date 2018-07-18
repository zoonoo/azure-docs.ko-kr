---
title: 포함 파일
description: 포함 파일
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852044"
---
| 리소스 | 최대 제한 |메모|
| --- | --- |---|
| Automation 계정당 30초마다 전송할 수 있는 새 작업의 최대 수(예약되지 않은 작업) |100 |이 제한에 도달하면 작업을 만들기 위한 후속 요청이 실패합니다. 클라이언트는 오류 응답을 받습니다.|
| Automation 계정당 동일한 인스턴스 시간에 동시 실행 작업의 최대 수(예약되지 않은 작업) |200 |이 제한에 도달하면 작업을 만들기 위한 후속 요청이 실패합니다. 클라이언트는 오류 응답을 받습니다.|
| Automation 계정당 30초마다 가져올 수 있는 모듈의 최대 수 |5 ||
| 모듈의 최대 크기 |100 MB ||
| 작업 런타임 - 무료 계층 |월별 구독당 500분 ||
| 샌드박스당 허용되는 디스크 공간의 최대 크기**<sup>1</sup>** |1 GB |Azure 샌드박스에만 적용됩니다.|
| 샌드박스에 지정된 최대 메모리 양**<sup>1</sup>** |400 MB |Azure 샌드박스에만 적용됩니다.|
| 샌드박스당 허용되는 최대 네트워크 소켓 수**<sup>1</sup>** |1000 |Azure 샌드박스에만 적용됩니다.|
| 구독에서 Automation 계정의 최대 수 |제한 없음 ||
|단일 Hybrid Runbook Worker에서 실행할 최대 동시 작업 수|50 ||

**<sup>1</sup>** 샌드박스는 여러 작업에서 사용할 수 있는 공유 환경으로, 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다.
