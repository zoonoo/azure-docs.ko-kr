---
title: 포함 파일
description: 포함 파일
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444255"
---
#### <a name="process-automation"></a>프로세스 자동화

| 리소스 | 최대 제한 |메모|
| --- | --- |---|
| Automation 계정당 30초마다 전송할 수 있는 새 작업의 최대 수(예약되지 않은 작업) |100 |이 제한에 도달하면 작업을 만들기 위한 후속 요청이 실패합니다. 클라이언트는 오류 응답을 받습니다.|
| Automation 계정당 동일한 인스턴스 시간에 동시 실행 작업의 최대 수(예약되지 않은 작업) |200 |이 제한에 도달하면 작업을 만들기 위한 후속 요청이 실패합니다. 클라이언트는 오류 응답을 받습니다.|
| 30일 롤링 기간 동안 작업 메타데이터의 최대 스토리지 크기입니다. | 10GB(약 4백만 작업)|이 제한에 도달하면 작업을 만들기 위한 후속 요청이 실패합니다. |
| Automation 계정당 30초마다 가져올 수 있는 모듈의 최대 수 |5 ||
| 모듈의 최대 크기 |100 MB ||
| 작업 런타임 - 무료 계층 |월별 구독당 500분 ||
| 샌드박스당 허용되는 최대 디스크 공간 크기**<sup>1</sup>** |1 GB |Azure 샌드박스에만 적용됩니다.|
| 샌드박스에 지정된 최대 메모리 양**<sup>1</sup>** |400 MB |Azure 샌드박스에만 적용됩니다.|
| 샌드박스당 허용되는 최대 네트워크 소켓 수**<sup>1</sup>** |1000 |Azure 샌드박스에만 적용됩니다.|
| Runbook당 허용된 최대 런타임**<sup>1</sup>** |3시간 |Azure 샌드박스에만 적용됩니다.|
| 구독에서 Automation 계정의 최대 수 |제한 없음 ||
|단일 Hybrid Runbook Worker에서 실행할 최대 동시 작업 수|50 ||
| 최대 Runbook 작업 매개 변수의 최대 크기   | 512KB||
| Runbook 매개 변수의 최대 수   | 50|JSON 또는 XML 문자열을 매개 변수에 전달하고 50개의 매개 변수 제한에 도달하면 Runbook을 사용하여 구문 분석할 수 있습니다|
| 웹후크 페이로드의 최대 크기 |  512KB|
| 작업 데이터가 보존되는 최대 일 수|30일|
| 최대 PowerShell 워크플로 상태 크기 |5MB| 워크플로를 검사할 때 PowerShell 워크플로 Runbook에 적용됩니다.|

**<sup>1</sup>** 샌드박스는 여러 작업에서 사용할 수 있는 공유 환경으로, 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다.

#### <a name="change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리

다음 표는 변경 내용 추적에 대한 머신당 추적된 항목 한도를 보여 줍니다.

| **리소스** | **제한**| **참고 사항** |
|---|---|---|
|파일|500||
|레지스트리|250||
|Windows 소프트웨어|250|소프트웨어 업데이트를 포함하지 않음|
|Linux 패키지|1250||
|Services|250||
|디먼|250||
