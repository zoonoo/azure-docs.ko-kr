---
title: "Azure Import/Export 도구 가져오기 작업 명령에 대한 빠른 참조 - v1 | Microsoft Docs"
description: "자주 사용되는 가져오기 작업 명령에 대한 Azure Import/Export 도구 명령 참조 Import/Export 도구 v1을 나타냅니다."
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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 632100f324c47d69f64cff020aafbaa300ae8c2b
ms.lasthandoff: 03/30/2017


---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>가져오기 작업에 자주 사용 되는 명령에 대한 빠른 참조
이 섹션에서는 몇 가지 자주 사용하는 명령에 대한 빠른 참조를 제공합니다. 자세한 사용법은 [가져오기 작업을 위한 하드 드라이브 준비](storage-import-export-tool-preparing-hard-drives-import-v1.md)를 참조하세요.  

## <a name="prepare-the-disks-when-data-already-copied-to-the-disks"></a>이미 디스크에 데이터가 복사되어 있는 경우 디스크 준비
 다음은 BitLocker로 아직 암호화되지 않은 하드 드라이브에 이미 데이터가 복사된 경우 디스크를 준비하는 샘플 명령입니다.  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>하드 드라이브에 단일 디렉터리 복사  
 다음은 BitLocker로 아직 암호화되지 않은 하드 드라이브에 단일 원본 디렉터리를 복사하는 샘플 명령입니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-wwo-directories-to-a-hard-drive"></a>하드 드라이브에 wwo 디렉터리 복사  
 두 개의 소스 디렉터리를 드라이브에 복사하려면 두 개의 명령이 필요합니다.  
  
 첫 번째 명령은 일반적인 매개 변수 외에도 로그 디렉터리, 저장소 계정 키, 대상 드라이브 문자 및 `format/encrypt` 요구 사항을 지정합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 두 번째 명령은 저널 파일, 새 세션 ID 및 소스와 대상 위치를 지정합니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>두 번째 복사 세션에서 하드 드라이브에 큰 파일 복사  
 다음은 이전 복사 세션에서 준비된 드라이브에 하나의 큰 파일을 복사하는 샘플 명령입니다.  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브를 준비하는 샘플 워크플로](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)

