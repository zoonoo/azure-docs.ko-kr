---
title: Azure Import/Export 가져오기 작업 복구-v1 | Microsoft Docs
description: Azure Import/Export 서비스를 사용 하 여 생성 및 실행 된 가져오기 작업을 복구 하는 방법에 대해 알아봅니다.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973886"
---
# <a name="repairing-an-import-job"></a>Importálási feladat javítása
Microsoft Azure Import/Export 서비스에서 일부 파일 또는 파일의 일부를 Windows Azure Blob service에 복사 하지 못할 수 있습니다. 몇 가지 오류의 원인은 다음과 같습니다.  
  
-   손상 된 파일  
  
-   손상 된 드라이브  
  
-   파일이 전송 되는 동안 저장소 계정 키가 변경 되었습니다.  
  
가져오기 작업의 복사 로그 파일을 사용 하 여 Microsoft Azure Import/Export 도구를 실행할 수 있습니다 .이 도구는 누락 된 파일 (또는 파일의 일부)을 Microsoft Azure 저장소 계정에 업로드 하 여 가져오기 작업을 완료 합니다.  
  
## <a name="repairimport-parameters"></a>RepairImport 매개 변수

다음 매개 변수는 **Repairimport**를 사용 하 여 지정할 수 있습니다. 
  
|||  
|-|-|  
|**/r:** < RepairFile\>|**Kötelező.** 복구의 진행 상황을 추적 하 고 중단 된 복구를 다시 시작할 수 있도록 하는 복구 파일의 경로입니다. 각 드라이브에는 하나의 복구 파일만 있어야 합니다. 지정 된 드라이브에 대 한 복구를 시작할 때 아직 존재 하지 않는 복구 파일의 경로를 전달 합니다. 중단 된 복구를 다시 시작 하려면 기존 복구 파일의 이름을 전달 해야 합니다. 대상 드라이브에 해당 하는 복구 파일은 항상 지정 해야 합니다.|  
|**/logdir:** < logdirectory\>|**필드.** 로그 디렉터리입니다. 자세한 로그 파일은이 디렉터리에 기록 됩니다. 로그 디렉터리를 지정 하지 않으면 현재 디렉터리가 로그 디렉터리로 사용 됩니다.|  
|**/d:** < targetdirectories\>|**Kötelező.** 가져온 원본 파일을 포함 하는 세미콜론으로 구분 된 하나 이상의 디렉터리입니다. 가져오기 드라이브도 사용할 수 있지만 원본 파일의 대체 위치를 사용할 수 있는 경우에는 필요 하지 않습니다.|  
|**/bk:** < BitLockerKey\>|**필드.** 도구에서 원본 파일을 사용할 수 있는 암호화 된 드라이브의 잠금을 해제 하려면 BitLocker 키를 지정 해야 합니다.|  
|**/wsn:** < storageaccountname\>|**Kötelező.** 가져오기 작업의 저장소 계정 이름입니다.|  
|**/.:** < StorageAccountKey\>|컨테이너 SAS가 지정 되지 않은 경우에만 **필수** 입니다. 가져오기 작업의 저장소 계정에 대 한 계정 키입니다.|  
|**/csas:** < s a s\>|저장소 계정 키가 지정 되지 않은 경우에만 **필요** 합니다. 가져오기 작업과 연결 된 blob에 액세스 하기 위한 컨테이너 SAS입니다.|  
|**/Svlogfile:** < DriveCopyLogFile\>|**Kötelező.** 드라이브 복사 로그 파일 (자세한 로그 또는 오류 로그)의 경로입니다. 이 파일은 Microsoft Azure Import/Export 서비스에 의해 생성 되 고 작업과 연결 된 blob 저장소에서 다운로드할 수 있습니다. 복사 로그 파일에는 실패 한 blob 또는 파일에 대 한 정보가 포함 되어 있으며이는 복구할 수 있습니다.|  
|**/Pathmapfile:** < DrivePathMapFile\>|**필드.** 동일한 작업에서 가져온 것과 같은 이름의 파일이 여러 개 있는 경우 모호성을 해결 하는 데 사용할 수 있는 텍스트 파일의 경로입니다. 도구를 처음 실행 하는 경우이 파일을 모호한 모든 이름으로 채울 수 있습니다. 이 도구를 이후에 실행 하면이 파일이 모호성을 해결 하는 데 사용 됩니다.|  
  
## <a name="using-the-repairimport-command"></a>RepairImport 명령 사용  
네트워크를 통해 데이터를 스트리밍하 여 데이터 가져오기를 복구 하려면 `/d` 매개 변수를 사용 하 여 가져온 원본 파일이 포함 된 디렉터리를 지정 해야 합니다. 또한 저장소 계정에서 다운로드 한 복사 로그 파일도 지정 해야 합니다. 부분 오류가 있는 가져오기 작업을 복구 하는 일반적인 명령줄은 다음과 같습니다.  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
다음 복사 로그 파일 예제에서는 가져오기 작업을 위해 배송 된 드라이브에서 파일의 1 64-K 조각이 손상 되었습니다. 이 오류는 유일 하 게 표시 되므로 작업의 나머지 blob은 성공적으로 가져왔습니다.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
이 복사 로그가 Azure Import/Export 도구에 전달 되 면 도구는 네트워크에서 누락 된 콘텐츠를 복사 하 여이 파일에 대 한 가져오기를 완료 하려고 시도 합니다. 위의 예제를 따라 도구는 두 디렉터리 `C:\Users\bob\Pictures` 및 `X:\BobBackup\photos`내에서 원래 파일 `\animals\koala.jpg`를 찾습니다. `C:\Users\bob\Pictures\animals\koala.jpg` 파일이 존재 하는 경우 Azure Import/Export 도구는 해당 blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`에 누락 된 데이터 범위를 복사 합니다.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>RepairImport를 사용할 때 충돌 해결  
경우에 따라 파일을 찾을 수 없거나, 파일에 액세스할 수 없거나, 파일 이름이 모호 하거나, 파일 내용이 더 이상 올바르지 않은 경우 도구에서 필요한 파일을 찾거나 열지 못할 수 있습니다.  
  
도구에서 `\animals\koala.jpg` 찾으려고 시도 하 고 `C:\Users\bob\pictures` 및 `X:\BobBackup\photos`에 해당 이름의 파일이 있는 경우 모호한 오류가 발생할 수 있습니다. 즉, `C:\Users\bob\pictures\animals\koala.jpg` 및 `X:\BobBackup\photos\animals\koala.jpg` 모두 가져오기 작업 드라이브에 있습니다.  
  
`/PathMapFile` 옵션을 사용 하면 이러한 오류를 해결할 수 있습니다. 도구가 제대로 식별할 수 없는 파일 목록이 포함 된 파일의 이름을 지정할 수 있습니다. 다음 명령줄 예제는 `9WM35C2V_pathmap.txt`을 채웁니다.  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
그러면 도구는 문제가 있는 파일 경로를 한 줄에 하나씩 `9WM35C2V_pathmap.txt`씁니다. 예를 들어 명령을 실행 한 후 파일에 다음 항목이 포함 될 수 있습니다.  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 목록에 있는 각 파일의 경우 도구에서 사용할 수 있도록 파일을 찾아서 열어야 합니다. 파일을 찾을 수 있는 위치를 도구에 명시적으로 지시 하려면 경로 맵 파일을 수정 하 고 각 파일의 경로를 탭 문자로 구분 하 여 동일한 줄에 추가 합니다.  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
도구에서 필요한 파일을 사용할 수 있도록 하거나 경로 맵 파일을 업데이트 한 후에는 도구를 다시 실행 하 여 가져오기 프로세스를 완료할 수 있습니다.  
  
## <a name="next-steps"></a>Következő lépések
 
* [Azure Import/Export 도구 설정](storage-import-export-tool-setup-v1.md)   
* [Merevlemezek előkészítése importálási feladatokhoz](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Exportálási feladat javítása](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Az Azure Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
