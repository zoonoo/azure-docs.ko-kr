---
title: Azure 파일 공유 – Azure 파일 공유에서 파일을 삭제하지 못함
description: Azure 파일 공유에서 파일을 삭제하지 못하는 문제를 식별하고 해결합니다.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: d8cc0cb7df4bb7bfff5a6b9d2f159cb674532927
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789756"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure 파일 공유 – Azure 파일 공유에서 파일을 삭제하지 못함

Azure 파일 공유에서 파일을 삭제하지 못하면 다음과 같은 다양한 증상이 발생할 수 있습니다.

**증상 1:**

다음 두 가지 문제 중 하나 때문에 Azure 파일 공유에서 파일을 삭제할 수 없습니다.

* 삭제 표시된 파일
* 지정된 리소스가 SMB 클라이언트에서 사용 중임

**증상 2:**

Not enough quota is available to process this command

## <a name="cause"></a>원인

파일 공유가 탑재되어 있는 컴퓨터의 파일에 허용되는 동시 오픈 핸들의 상한값에 도달하면 오류 1816이 발생합니다. 자세한 내용은 [Azure Storage 성능 및 확장성 검사 목록](../blobs/storage-performance-checklist.md)을 참조하세요.

## <a name="resolution"></a>해상도

일부 핸들을 닫아 동시 열린 핸들 수를 줄입니다.

## <a name="prerequisite"></a>필수 조건

### <a name="install-the-latest-azure-powershell-module"></a>최신 Azure PowerShell 모듈 설치

* [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Azure에 연결:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>대상 스토리지 계정의 구독 선택:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>대상 스토리지 계정의 컨텍스트 생성:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>파일 공유의 현재 열린 핸들 가져오기:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>결과 예제:

|HandleId|경로|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|새 폴더/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>열린 핸들을 닫습니다.

열린 핸들을 닫으려면 다음 명령을 사용하세요.

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>다음 단계

* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure 파일 동기화 문제 해결](../file-sync/file-sync-troubleshoot.md)