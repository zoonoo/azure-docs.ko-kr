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
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554205"
---
#### <a name="process-automation"></a>프로세스 자동화

| 리소스 | 최대 제한 |메모|
| --- | --- |---|
| (임의 작업) Azure Automation 계정당 30 초 마다 전송할 수 있는 새 작업의 최대 수 |100 |이 한도 도달 하면 작업을 만드는 후속 요청이 실패 합니다. 클라이언트는 오류 응답을 받습니다.|
| 최대 시간 (임의 작업) Automation 계정당 동일한 인스턴스에서 실행 되는 동시 작업 수 |200 |이 한도 도달 하면 작업을 만드는 후속 요청이 실패 합니다. 클라이언트는 오류 응답을 받습니다.|
| 30 일 롤링 기간에 대 한 메타 데이터 작업의 최대 저장소 크기 | 10GB (약 4 백만 작업)|이 한도 도달 하면 작업을 만드는 후속 요청이 실패 합니다. |
| Automation 계정당 30 초 마다 가져올 수 있는 모듈의 최대 수 |5 ||
| 모듈의 최대 크기 |100 MB ||
| 작업 실행 시간, 무료 계층 |월별 구독당 500분 ||
| 샌드박스 당 허용 되는 디스크 공간의 최대 크기<sup>1</sup> |1GB |Azure 샌드박스에 적용 됩니다.|
| 샌드박스를 제공 하는 메모리의 최대 크기<sup>1</sup> |400 MB |Azure 샌드박스에 적용 됩니다.|
| 최대 샌드박스 당 허용 되는 네트워크 소켓<sup>1</sup> |1,000 |Azure 샌드박스에 적용 됩니다.|
| Runbook 당 허용 된 최대 런타임<sup>1</sup> |3시간 |Azure 샌드박스에 적용 됩니다.|
| 구독에서 Automation 계정의 최대 수 |제한 없음 ||
|최대 단일 Hybrid Runbook Worker에서 실행할 수 있는 동시 작업 수|50 ||
| 최대 runbook 작업 매개 변수 크기   | 킬로 비트||
| 최대 runbook 매개 변수   | 50|50 하이퍼 매개 변수 제한에 도달 하면 매개 변수에 JSON 또는 XML 문자열에 전달 하 고 runbook을 사용 하 여 구문 분석 합니다.|
| 최대 웹 후크 페이로드 크기 |  킬로 비트|
| 작업 데이터 보존 되는 최대 일 수|30일|
| 최대 PowerShell 워크플로 상태 크기 |5MB| PowerShell 워크플로 runbook에 적용 되는 경우 검사점 워크플로.|

<sup>1</sup>샌드박스는 여러 작업에서 사용할 수 있는 공유 환경입니다. 동일한 샌드박스를 사용 하는 작업 리소스에 대 한 제한인 샌드박스 연결 되어 있습니다.

#### <a name="change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리

다음 표에서 변경 내용 추적에 대 한 컴퓨터당 추적된 항목 제한 합니다.

| **Resource** | **제한**| **참고** |
|---|---|---|
|파일|500||
|레지스트리|250||
|Windows 소프트웨어|250|소프트웨어 업데이트를 포함 하지 않습니다.|
|Linux 패키지|1,250||
|Services|250||
|디먼|250||
