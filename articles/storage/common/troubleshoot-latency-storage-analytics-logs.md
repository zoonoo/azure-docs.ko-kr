---
title: 스토리지 분석 로그를 사용하여 대기 시간 문제 해결
description: Azure Storage 분석 로그를 사용 하 여 대기 시간 문제를 식별 하 고 해결 하며 클라이언트 응용 프로그램을 최적화 합니다.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: efae9cd2a73bf6df89007ac313ca6dfe6efe6ddd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075943"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>스토리지 분석 로그를 사용하여 대기 시간 문제 해결

진단 및 문제 해결은 Azure Storage를 사용 하 여 클라이언트 응용 프로그램을 빌드 및 지원 하기 위한 핵심 기술입니다.

Azure 응용 프로그램의 분산 된 특성 때문에, 오류 및 성능 문제를 진단 하 고 해결 하는 것은 기존 환경 보다 더 복잡할 수 있습니다.

다음 단계에서는 Azure Storage 분석 로그를 사용 하 여 대기 시간 문제를 식별 하 고 해결 하며 클라이언트 응용 프로그램을 최적화 하는 방법을 보여 줍니다.

## <a name="recommended-steps"></a>권장되는 단계

1. [스토리지 분석 로그](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)를 다운로드 합니다.

2. 원시 형식 로그를 테이블 형식으로 변환 하려면 다음 PowerShell 스크립트를 사용 합니다.

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. 이 스크립트는 아래와 같이 열을 기준으로 정보를 필터링 할 수 있는 GUI 창을 시작 합니다.

   ![저장소 분석 로그 파서 창](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. "작업 유형"에 따라 로그 항목의 범위를 좁히고 문제 시간 프레임 중에 생성 된 로그 항목을 찾습니다.

   ![작업 유형 로그 항목](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 문제가 발생 한 시간 동안 다음 값이 중요 합니다.

   * Operation-type = GetBlob
   * 요청-상태 = SASNetworkError
   * 종단 간 대기 시간-Ms = 8453
   * 서버-대기 시간-Ms = 391

   다음 수식을 사용 하 여 종단 간 대기 시간을 계산 합니다.

   * 종단 간 대기 시간 = Server-Latency + 클라이언트 대기 시간

   로그 항목을 사용 하 여 클라이언트 대기 시간을 계산 합니다.

   * 클라이언트 대기 시간 = 종단 간 대기 시간-Server-Latency

        예: 8453 – 391 = 8062ms

   다음 표에서는 대기 시간이 긴 OperationType 및 RequestStatus 결과에 대 한 정보를 제공 합니다.

   | Blob 유형 |RequestStatus =<br>성공|RequestStatus =<br>SAS NetworkError|권장|
   |---|---|---|---|
   |GetBlob|예|아니요|[**Getblob 작업:** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|아니요|예|[**Getblob 작업:** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|예|아니요|[**Put 작업:** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|아니요|예|[**Put 작업:** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>상태 결과

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 작업: RequestStatus = Success

"권장 단계" 섹션의 5 단계에서 설명한 대로 다음 값을 확인 합니다.

* 종단 간 대기 시간
* Server-Latency
* Client-Latency

**Requeststatus가 Success**인 **Getblob 작업** 에서 **최대 시간이** **클라이언트 대기**시간에 소요 되는 경우이는 Azure Storage에서 클라이언트에 데이터를 쓰는 데 많은 시간을 소비 하 고 있음을 나타냅니다. 이 지연은 Client-Side 문제를 나타냅니다.

**권장 사항:**

* 클라이언트의 코드를 조사 합니다.
* Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용 하 여 클라이언트에서 네트워크 연결 문제를 조사 합니다. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 작업: RequestStatus = (SAS) NetworkError

"권장 단계" 섹션의 5 단계에서 설명한 대로 다음 값을 확인 합니다.

* 종단 간 대기 시간
* Server-Latency
* Client-Latency

**Requeststatus = (SAS) NetworkError**를 사용 하는 **Getblob 작업** 에서 **최대 시간이** **클라이언트 대기**시간에 소요 되는 경우 가장 일반적인 문제는 저장소 서비스에서 시간 제한이 만료 되기 전에 클라이언트의 연결을 끊는 것입니다.

**권장 사항:**

* 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
* Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용 하 여 클라이언트에서 네트워크 연결 문제를 조사 합니다. 

### <a name="put-operation-requeststatus--success"></a>Put 작업: RequestStatus = Success

"권장 단계" 섹션의 5 단계에서 설명한 대로 다음 값을 확인 합니다.

* 종단 간 대기 시간
* Server-Latency
* Client-Latency

**Requeststatus가 Success**인 **Put 작업** 에서 **최대 시간이** **클라이언트 대기**시간에 소요 되는 경우 클라이언트에서 Azure Storage 데이터를 전송 하는 데 더 많은 시간이 소요 됨을 나타냅니다. 이 지연은 Client-Side 문제를 나타냅니다.

**권장 사항:**

* 클라이언트의 코드를 조사 합니다.
* Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용 하 여 클라이언트에서 네트워크 연결 문제를 조사 합니다. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put 작업: RequestStatus = (SAS) NetworkError

"권장 단계" 섹션의 5 단계에서 설명한 대로 다음 값을 확인 합니다.

* 종단 간 대기 시간
* Server-Latency
* Client-Latency

**Requeststatus = (SAS) NetworkError**를 사용 하는 **Putblob 작업** 에서 **최대 시간이** **클라이언트 대기**시간에 소요 되는 경우 가장 일반적인 문제는 저장소 서비스에서 시간 제한이 만료 되기 전에 클라이언트의 연결을 끊는 것입니다.

**권장 사항:**

* 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
* Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용 하 여 클라이언트에서 네트워크 연결 문제를 조사 합니다.

