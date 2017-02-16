---
title: "Azure Import/Export 도구 가져오기 작업 명령에 대한 빠른 참조 | Microsoft Docs"
description: "가져오기 작업에 자주 사용되는 Azure Import/Export 도구 명령에 대한 명령 참조"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 41bc5585f3d9dea2a08dc8a6bc1e4fdf9f0c8fc4
ms.openlocfilehash: 79b1d3f92671638ba43bac2f5428a1f903d11080


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>가져오기 작업에 자주 사용 되는 명령에 대한 빠른 참조

이 문서에서는 몇 가지 자주 사용하는 명령에 대한 빠른 참조를 제공합니다. 자세한 사용법은 [가져오기 작업을 위한 하드 드라이브 준비](storage-import-export-tool-preparing-hard-drives-import.md)를 참조하세요.

## <a name="import-job-quick-reference"></a>가져오기 작업 빠른 참조

첫 번째 세션:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

두 번째 세션:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

마지막 세션에 대해:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

중단된 마지막 세션 다시 시작:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

마지막 세션에 드라이브 추가:

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>다음 단계

[가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)



<!--HONumber=Dec16_HO3-->


