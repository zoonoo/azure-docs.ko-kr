---
title: 스토리지 분석 로그를 사용하여 대기 시간 문제 해결
description: Azure 저장소 분석 로그를 사용하여 대기 시간 문제를 식별하고 해결하고 클라이언트 응용 프로그램을 최적화합니다.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196517"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>스토리지 분석 로그를 사용하여 대기 시간 문제 해결

진단 및 문제 해결은 Azure Storage를 통해 클라이언트 응용 프로그램을 빌드하고 지원하는 핵심 기술입니다.

Azure 응용 프로그램의 분산 특성으로 인해 오류 와 성능 문제를 진단하고 해결하는 것이 기존 환경보다 더 복잡할 수 있습니다.

다음 단계에서는 Azure Storage 분석 로그를 사용하여 대기 시간 문제를 식별하고 해결하는 방법을 보여 주며 클라이언트 응용 프로그램을 최적화합니다.

## <a name="recommended-steps"></a>권장되는 단계

1. 스토리지 [분석 로그를 다운로드합니다.](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)

2. 다음 PowerShell 스크립트를 사용하여 원시 형식 로그를 테이블 형식 형식으로 변환합니다.

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

3. 스크립트는 아래와 같이 열별로 정보를 필터링할 수 있는 GUI 창을 시작합니다.

   ![스토리지 분석 로그 파서 창](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. "작업 유형"을 기반으로 로그 항목의 범위를 좁혀 문제의 시간 프레임 동안 생성된 로그 항목을 찾습니다.

   ![작업 유형 로그 항목](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 문제가 발생한 시간 동안 다음 값이 중요합니다.

   * 작업 유형 = 겟블렙
   * 요청 상태 = SASNetworkError
   * 종단 간 대기 시간 - MS = 8453
   * 서버 대기 시간 -인-MS = 391

   종단 간 대기 시간은 다음 방정식을 사용하여 계산됩니다.

   * 종단 간 대기 시간 = 서버 대기 시간 + 클라이언트 대기 시간

   로그 항목을 사용하여 클라이언트 대기 시간을 계산합니다.

   * 클라이언트 대기 시간 = 종단 간 대기 시간 - 서버 대기 시간

          * Example: 8453 – 391 = 8062ms

   다음 표에서는 대기 시간이 많은 작업 유형 및 요청 상태 결과에 대한 정보를 제공합니다.

   |   |요청 상태 =<br>Success|요청 상태 =<br>(SAS) 네트워크 오류|권장|
   |---|---|---|---|
   |GetBlob|yes|예|[**겟블렙 작업:** 요청 상태 = 성공](#getblob-operation-requeststatus--success)|
   |GetBlob|예|yes|[**겟블렙 작업:** 요청 상태 = (SAS)네트워크 오류](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|yes|예|[**작업 넣기:** 요청 상태 = 성공](#put-operation-requeststatus--success)|
   |PutBlob|예|yes|[**작업 넣기:** 요청 상태 = (SAS)네트워크 오류](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>상태 결과

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 작업: 요청 상태 = 성공

"권장 단계" 섹션의 5단계에서 설명한 대로 다음 값을 확인합니다.

* 종단 간 대기 시간
* 서버 대기 시간
* 클라이언트 대기 시간

요청 상태가 있는 **GetBlob 작업에서** **최대** **시간이** 클라이언트 **대기 시간에**소요되는 경우 Azure Storage가 클라이언트에 데이터를 작성하는 데 많은 시간을 소비하고 있음을 나타냅니다. 이 지연은 클라이언트 측 문제를 나타냅니다.

**추천:**

* 클라이언트의 코드를 조사합니다.
* Wireshark, Microsoft 메시지 분석기 또는 Tcping을 사용하여 클라이언트의 네트워크 연결 문제를 조사합니다. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 작업: 요청 상태 = (SAS) 네트워크 오류

"권장 단계" 섹션의 5단계에서 설명한 대로 다음 값을 확인합니다.

* 종단 간 대기 시간
* 서버 대기 시간
* 클라이언트 대기 시간

**요청 상태 = (SAS)가**있는 **GetBlob 작업에서** **최대 시간이** 클라이언트 **대기 시간에**소요되는 경우 가장 일반적인 문제는 클라이언트가 저장소 서비스에서 시간 초과가 만료되기 전에 연결이 끊어지는 것입니다.

**추천:**

* 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
* Wireshark, Microsoft 메시지 분석기 또는 Tcping을 사용하여 클라이언트의 네트워크 연결 문제를 조사합니다. 

### <a name="put-operation-requeststatus--success"></a>작업 넣기: 요청 상태 = 성공

"권장 단계" 섹션의 5단계에서 설명한 대로 다음 값을 확인합니다.

* 종단 간 대기 시간
* 서버 대기 시간
* 클라이언트 대기 시간

**요청 Status를**가진 Put **작업에서** = 성공 , 클라이언트 **대기 시간에**최대 **시간이** 소요되는 경우 클라이언트가 Azure 저장소로 데이터를 보내는 데 더 많은 시간이 걸리고 있음을 나타냅니다. 이 지연은 클라이언트 측 문제를 나타냅니다.

**추천:**

* 클라이언트의 코드를 조사합니다.
* Wireshark, Microsoft 메시지 분석기 또는 Tcping을 사용하여 클라이언트의 네트워크 연결 문제를 조사합니다. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>작업 배치: 요청 상태 = (SAS) 네트워크 오류

"권장 단계" 섹션의 5단계에서 설명한 대로 다음 값을 확인합니다.

* 종단 간 대기 시간
* 서버 대기 시간
* 클라이언트 대기 시간

**요청 상태 = (SAS)가**있는 **PutBlob 작업에서** **최대 시간이** 클라이언트 **대기 시간에**소요되는 경우 가장 일반적인 문제는 클라이언트가 저장소 서비스에서 시간 초과가 만료되기 전에 연결이 끊어지는 것입니다.

**추천:**

* 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
* Wireshark, Microsoft 메시지 분석기 또는 Tcping을 사용하여 클라이언트의 네트워크 연결 문제를 조사합니다.

