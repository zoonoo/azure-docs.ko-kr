---
title: Azure 파일 공유 – Azure 파일 공유에서 파일을 삭제하지 못함
description: Azure 파일 공유에서 파일을 삭제하지 못하는 경우를 식별하고 해결합니다.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196473"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure 파일 공유 – Azure 파일 공유에서 파일을 삭제하지 못함

Azure 파일 공유에서 파일을 삭제하지 못하면 다음과 같은 몇 가지 증상이 있을 수 있습니다.

**증상 1:**

아래 두 가지 문제 중 하나로 인해 Azure 파일 공유에서 파일을 삭제하지 못했습니다.

* 삭제로 표시된 파일
* 지정된 리소스는 SMB 클라이언트에서 사용 중일 수 있습니다.

**증상 2:**

Not enough quota is available to process this command

## <a name="cause"></a>원인

오류 1816 파일 공유가 탑재되는 컴퓨터에서 파일에 대해 허용되는 동시 열린 핸들의 상한에 도달하면 발생합니다. 자세한 내용은 Azure [저장소 성능 및 확장성 검사 목록을](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)참조하십시오.

## <a name="resolution"></a>해결 방법

일부 핸들을 닫아 동시 열린 핸들 수를 줄입니다.

## <a name="prerequisite"></a>필수 요소

### <a name="install-the-latest-azure-powershell-module"></a>최신 Azure PowerShell 모듈 설치

* [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Azure에 연결:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>대상 저장소 계정의 구독을 선택합니다.

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>대상 저장소 계정에 대한 컨텍스트 만들기:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>파일 공유의 현재 열려 있는 핸들을 가져옵니다.

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>예제 결과:

|핸들 ID|경로|ClientIp|클라이언트 포트|오픈 타임|라스트리커넥트타임|FileId|부모 ID|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|새 폴더/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|테스트.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>열린 핸들 닫기:

열린 핸들을 닫려면 다음 명령을 사용합니다.

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>다음 단계

* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)