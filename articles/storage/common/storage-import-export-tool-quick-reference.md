---
title: Azure Import/Export 도구 가져오기 작업 명령에 대한 빠른 참조 | Microsoft Docs
description: 자주 사용되는 가져오기 작업 명령에 대한 Azure Import/Export 도구 명령 참조
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 8f3eb2bf2d9789b678849f9e2415816d15afc29e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526633"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>가져오기 작업에 자주 사용 되는 명령에 대한 빠른 참조

이 문서에서는 몇 가지 자주 사용하는 명령에 대한 빠른 참조를 제공합니다. 자세한 사용법은 [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import.md)를 참조하세요.

## <a name="first-session"></a>첫 번째 세션

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>두 번째 세션

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>마지막 세션 중단

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>중단된 마지막 세션 다시 시작

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>마지막 세션에 드라이브 추가

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
