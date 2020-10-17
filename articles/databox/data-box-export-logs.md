---
title: 내보내기 순서에 대 한 Azure Data Box, Azure Data Box Heavy 이벤트 추적 및 기록 | Microsoft Docs
description: Azure Data Box의 다양 한 단계에서 이벤트를 추적 하 고 기록 하는 방법 및 Azure Data Box Heavy 내보내기 순서에 대해 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 01eb35a60a6d51b5742d8fedd2ee0631aa86c924
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147957"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Azure Data Box 및 Azure Data Box Heavy 내보내기 주문에 대 한 추적 및 이벤트 로깅

Data Box 또는 Data Box Heavy 내보내기 순서는 순서, 설정, 데이터 복사, 반환 및 데이터 지우기와 같은 단계를 거칩니다. 주문에 대 한 각 단계에 해당 하는 여러 작업을 수행 하 여 주문에 대 한 액세스를 제어 하 고, 이벤트를 감사 하 고, 주문을 추적 하 고, 생성 되는 다양 한 로그를 해석할 수 있습니다.

이 문서에서는 Data Box 또는 Data Box Heavy에 대 한 내보내기 주문을 추적 하 고 감사 하는 데 사용할 수 있는 다양 한 메커니즘 또는 도구에 대해 자세히 설명 합니다. 이 문서의 정보는, Data Box 및 Data Box Heavy에 모두 적용 됩니다. 이후 섹션에서는 Data Box에 대 한 모든 참조가 Data Box Heavy에도 적용 됩니다.

다음 표에서는 각 단계에서 순서를 추적 하 고 감사 하는 데 사용할 수 있는 Data Box 내보내기 주문 단계 및 도구를 요약 하 여 보여 줍니다.

| Data Box 내보내기 주문 단계       | 추적 및 감사 도구                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 주문 만들기               | [Azure RBAC를 통해 주문에 대 한 액세스 제어 설정](#set-up-access-control-on-the-order) <br> [자세한 로그를 순서 대로 사용](#enable-verbose-log-in-the-order)                                                    |
| 처리 된 주문            | [주문 추적](#track-the-order) <ul><li> Azure portal </li><li> 운송 업체 웹 사이트 </li><li>이메일 알림</ul> |
| 디바이스 설정              | 장치 자격 증명 액세스는 [활동 로그](#query-activity-logs-during-setup) 에 기록 됩니다.              |
| 장치에서 데이터 복사        | [복사 로그 검토](#copy-log) <br> 데이터를 복사 하기 전에 [자세한 정보 표시 로그 검토](#verbose-log)            |
| 장치에서 데이터 지우기   | 감사 로그 및 주문 기록을 포함 하 [는 관리 권의 로그의 체인 보기](#get-chain-of-custody-logs-after-data-erasure)                |


## <a name="set-up-access-control-on-the-order"></a>주문에 대 한 액세스 제어 설정

주문이 처음 생성 될 때 주문에 액세스할 수 있는 사용자를 제어할 수 있습니다. 다양 한 범위에서 Azure 역할을 설정 하 여 Data Box 주문에 대 한 액세스를 제어 합니다. Azure 역할은 작업의 하위 집합에 대 한 읽기-쓰기, 읽기 전용, 읽기/쓰기 액세스 유형을 결정 합니다.

Azure Data Box 서비스에 대해 정의할 수 있는 두 가지 역할은 다음과 같습니다.

- **Data Box 판독기** -범위에 정의 된 주문에 대 한 읽기 전용 액세스 권한이 있습니다. 주문에 대 한 세부 정보만 볼 수 있습니다. 저장소 계정과 관련 된 다른 정보에 액세스 하거나 주소 등의 주문 세부 정보를 편집할 수 없습니다.
- **Data Box 참여자** - *저장소 계정에 대 한 쓰기 권한이 이미 있는 경우*지정 된 저장소 계정으로 데이터를 전송 하는 순서만 만들 수 있습니다. 저장소 계정에 대 한 액세스 권한이 없는 경우 계정에 데이터를 복사 하는 Data Box 순서를 만들 수 없습니다. 이 역할은 저장소 계정 관련 사용 권한을 정의 하지 않으며 저장소 계정에 대 한 액세스 권한을 부여 하지 않습니다.  

주문에 대 한 액세스를 제한 하려면 다음을 수행할 수 있습니다.

- 순서 수준에서 역할을 할당 합니다. 사용자에 게는 해당 특정 Data Box 순서만 상호 작용 하기 위해 역할에 정의 된 권한만 있고 다른 작업은 없습니다.
- 리소스 그룹 수준에서 역할을 할당 합니다. 사용자는 리소스 그룹 내의 모든 Data Box 주문에 액세스할 수 있습니다.

권장 되는 Azure RBAC 사용에 대 한 자세한 내용은 [AZURE rbac에 대 한 모범 사례](../role-based-access-control/best-practices.md)를 참조 하세요.

## <a name="enable-verbose-log-in-the-order"></a>자세한 로그를 순서 대로 사용

Data Box에 대 한 내보내기 순서를 배치할 때 자세한 로그 컬렉션을 사용 하도록 설정 하는 옵션이 있습니다. 자세한 로그를 사용 하도록 설정할 수 있는 주문 화면은 다음과 같습니다.

![내보내기 옵션 선택](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

**자세한 정보 표시 로그 포함** 옵션을 선택 하면 Azure Storage 계정에서 데이터를 복사할 때 자세한 로그 파일이 생성 됩니다. 이 로그에는 성공적으로 내보낸 모든 파일의 목록이 포함 됩니다.      

내보내기 순서에 대 한 자세한 내용은 [Data Box 내보내기 순서 만들기](data-box-deploy-export-ordered.md) 를 참조 하세요.

## <a name="track-the-order"></a>주문 추적

Azure Portal 및 운송 업체 웹 사이트를 통해 주문을 추적할 수 있습니다. 다음 메커니즘은 언제 든 지 Data Box 순서를 추적 하는 데 사용할 수 있습니다.

- 장치가 Azure 데이터 센터 또는 온-프레미스에 있는 경우 순서를 추적 하려면 Azure Portal에서 **Data Box > 개요** 로 이동 합니다.

    ![주문 상태 보기 및 추적 안 함](media/data-box-logs/overview-view-status-1.png)

- 장치가 전송 되는 동안 주문을 추적 하려면 지역 통신 회사 웹 사이트 (예: US의 UPS 웹 사이트)로 이동 합니다. 주문과 관련 된 추적 번호를 제공 합니다.
- 또한 Data Box는 주문 생성 시 제공 된 전자 메일에 따라 주문 상태가 변경 될 때마다 전자 메일 알림을 보냅니다. 모든 Data Box 주문 [상태 목록은 주문 상태 보기](data-box-portal-admin.md#view-order-status)를 참조 하세요. 주문과 연결 된 알림 설정을 변경 하려면 [알림 세부 정보 편집](data-box-portal-admin.md#edit-notification-details)을 참조 하세요.

## <a name="query-activity-logs-during-setup"></a>설치 하는 동안 활동 로그 쿼리

- Data Box가 잠긴 상태에서 온-프레미스에 도착 합니다. Azure Portal에서 사용할 수 있는 장치 자격 증명을 순서 대로 사용할 수 있습니다.  

    Data Box 설정 되 면 장치 자격 증명에 액세스 한 사람을 알아야 할 수 있습니다. **장치 자격 증명** 블레이드에 액세스 한 사람을 파악 하기 위해 활동 로그를 쿼리할 수 있습니다.  **장치 세부 정보 > 자격 증명에 대** 한 액세스를 포함 하는 모든 작업은 작업으로 작업 로그에 기록 됩니다 `ListCredentials` .

    ![활동 로그 쿼리](media/data-box-logs/query-activity-log-1.png)

- Data Box에 대 한 각 로그인은 실시간으로 기록 됩니다. 그러나이 정보는 주문이 성공적으로 완료 된 후에 [관리 권의 감사 로그 체인](#chain-of-custody-audit-logs) 에서만 사용할 수 있습니다.

## <a name="view-logs-during-data-copy"></a>데이터를 복사 하는 동안 로그 보기

Data Box에서 데이터를 복사 하기 전에 Data Box 복사 된 데이터에 대 한 *복사 로그* 및 *자세한 정보 로그* 를 다운로드 하 고 검토할 수 있습니다. 이러한 로그는 Azure의 저장소 계정에서 Data Box 데이터를 복사할 때 생성 됩니다. 

### <a name="copy-log"></a>로그 복사

Data Box에서 데이터를 복사 하기 전에 **연결 및 복사** 페이지에서 복사 로그를 다운로드 합니다.

오류가 없고 Azure에서 Data Box 장치로 데이터를 복사 하는 동안 모든 파일이 복사 된 경우 *복사 로그* 의 샘플 출력은 다음과 같습니다.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
*복사 로그* 에 오류가 발생 하 고 일부 파일을 Azure에서 복사 하지 못한 경우의 샘플 출력은 다음과 같습니다.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

해당 파일을 내보낼 수 있는 다음과 같은 옵션이 있습니다. 

- 네트워크를 통해 복사할 수 없는 파일을 전송할 수 있습니다. 
- 데이터 크기가 사용 가능한 디바이스 용량보다 큰 경우 부분 복사가 발생하고 복사되지 않은 모든 파일이 이 로그에 나열됩니다. 이 로그를 입력 XML로 사용하여 새 Data Box 순서를 만든 다음, 이러한 파일을 복사할 수 있습니다.

### <a name="verbose-log"></a>자세한 정보 표시 로그

*자세한 정보 로그*에는 Azure Storage 계정에서 성공적으로 내보낸 모든 파일의 목록이 포함되어 있습니다. 로그에는 파일 크기 및 체크섬 계산도 포함됩니다.

자세한 정보 표시 로그는 다음과 같은 형식으로 되어 있습니다.

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

자세한 로그의 샘플 출력은 다음과 같습니다. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

자세한 로그도 Azure 저장소 계정에 복사 됩니다. 기본적으로 로그는 `copylog`라는 컨테이너에 기록됩니다. 로그는 다음과 같은 명명 규칙을 사용 하 여 저장 됩니다.

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

또한 포털에 대 한 **개요** 블레이드에서 복사 로그 경로도 표시 됩니다.

<!-- add a screenshot-->

이러한 로그를 사용 하 여 Azure에서 복사한 파일이 온-프레미스 서버에 복사 된 데이터와 일치 하는지 확인할 수 있습니다. 

자세한 로그 파일을 사용 합니다.

- 실제 이름과 Data Box에서 복사한 파일 수를 확인 합니다.
- 파일의 실제 크기를 확인 합니다.
- *Crc64* 이 0이 아닌 문자열에 해당 하는지 확인 합니다. Azure에서 내보내는 동안 CRC (순환 중복 검사) 계산이 수행 됩니다. 내보내기의 CRCs와 Data Box에서 온-프레미스 서버로 데이터를 복사한 후 비교할 수 있습니다. CRC 불일치는 해당 파일을 제대로 복사 하지 못했음을 나타냅니다.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>데이터 지우기 후 관리 권의 로그 체인 가져오기

NIST SP 800-88 수정 1 지침에 따라 Data Box 디스크에서 데이터를 지운 후에는 관리 권의 로그 체인을 사용할 수 있습니다. 이러한 로그에는 관리 권의 감사 로그와 주문 기록의 체인이 포함 됩니다. BOM 또는 매니페스트 파일도 감사 로그를 사용 하 여 복사 됩니다.

### <a name="chain-of-custody-audit-logs"></a>관리 권의 감사 로그 체인

관리 권의 감사 로그 체인에는 Azure 데이터 센터 외부에 있을 때 Data Box 또는 Data Box Heavy의 공유 전원을 켜고 액세스 하는 방법에 대 한 정보가 포함 되어 있습니다. 이러한 로그는 다음 위치에 있습니다. `storage-account/azuredatabox-chainofcustodylogs`

다음은 Data Box의 감사 로그 샘플입니다.

```output
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```

## <a name="download-order-history"></a>주문 기록 다운로드

Azure Portal에서 주문 기록을 사용할 수 있습니다. 주문이 완료 되 고 장치 정리 (디스크에서 데이터 지우기)가 완료 되 면 장치 주문으로 이동 하 여 **주문 정보**로 이동 합니다. **주문 기록 다운로드** 옵션을 사용할 수 있습니다. 자세한 내용은 [다운로드 주문 기록](data-box-portal-admin.md#download-order-history)을 참조 하세요.

주문 내역을 스크롤하면 다음과 같이 표시 됩니다.

- 장치에 대 한 반송파 추적 정보입니다.
- *Secureerase* 작업을 사용 하는 이벤트입니다. 이러한 이벤트는 디스크의 데이터를 지울 때 해당 됩니다.
- 로그 링크를 Data Box 합니다. *감사 로그*, *복사 로그*및 *BOM* 파일의 경로가 표시 됩니다.

다음은 Azure Portal의 주문 기록 로그 샘플입니다.

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>다음 단계

- [Data Box 및 Data Box Heavy에 대 한 문제를 해결](data-box-troubleshoot.md)하는 방법을 알아봅니다.
