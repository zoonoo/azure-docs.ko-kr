---
title: 추적 및 Azure Data Box를 Azure 데이터 상자 많은 이벤트를 기록할 | Microsoft Docs
description: 추적 하 고 Azure Data Box 및 Azure 데이터 상자 많은 주문 다양 한 단계에서 이벤트를 기록 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495815"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>추적 및 Azure Data Box 및 Azure 데이터 많은 상자의 이벤트 로깅

Data Box 또는 상자에 과도 한 데이터 순서를 다음 단계를 거칩니다: 순서를 설정, 데이터 복사, 반환, Azure에 업로드 및 확인 및 데이터 지우기. 순서로 각 단계에 해당 순서에 대 한 액세스를 제어, 감사 이벤트의 순서를 추적 하는 여러 작업을 수행할 수 있으며 생성 되는 다양 한 로그를 해석할 수도 있습니다.

다음 표에서 Data Box 또는 데이터 상자 많은 순서 단계 및 추적 하 고 각 단계 순서를 감사를 사용할 수 있는 도구가 요약을 보여 줍니다.

| 데이터 상자 순서 단계       | 추적 및 감사 도구                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 주문 만들기               | [RBAC 통해 순서에 대 한 액세스 제어 설정](#set-up-access-control-on-the-order)                                                    |
| 처리 순서            | [순서를 추적](#track-the-order) 통해 <ul><li> Azure portal </li><li> 운송 업체 웹 사이트를 전달합니다. </li><li>전자 메일 알림</ul> |
| 디바이스 설정              | 장치에 로그인 하는 액세스 자격 증명 [활동 로그](#query-activity-logs-during-setup)                                              |
| 장치에 데이터 복사        | [뷰 *error.xml* 파일](#view-error-log-during-data-copy) 데이터 복사                                                             |
| 배송 준비            | [BOM 파일 검사](#inspect-bom-during-prepare-to-ship) 또는 장치에서 매니페스트 파일                                      |
| Azure에 데이터 업로드       | [검토 *copylogs* ](#review-copy-log-during-upload-to-azure) 데이터 중 오류에 대 한 Azure 데이터 센터에 업로드                         |
| 장치의 데이터 지우기   | [로그 관리 권의 체인을 볼](#get-chain-of-custody-logs-after-data-erasure) 감사 로그 등 주문 내역                                                   |

이 문서에서는 다양 한 메커니즘 또는 추적 하 고 Data Box 또는 상자에 과도 한 데이터 감사를 사용할 수 있는 도구를 자세히 설명 합니다. 이 문서의 정보는 Data Box 및 상자에 과도 한 데이터 모두에 적용 됩니다. 후속 섹션에서는 Data Box에 대 한 참조도 상자에 과도 한 데이터에 적용 됩니다.

## <a name="set-up-access-control-on-the-order"></a>순서에 대 한 액세스 제어 설정

순서를 처음 만들 때 주문 액세스할 수 있는 사용자를 제어할 수 있습니다. Data Box 주문에 대 한 액세스를 제어 하려면 다양 한 범위에서 역할 기반 Access Control (RBAC) 역할을 설정 합니다. RBAC 역할 액세스-읽기-쓰기, 읽기 전용, 읽기 / 쓰기 작업의 하위 집합의 형식을 결정합니다.

Azure Data Box 서비스에 대해 정의 될 수 있는 두 가지 역할이 있습니다.

- **데이터 상자 판독기** -범위에서 정의 된 대로 주문이에 대 한 읽기 전용으로 액세스 했습니다. 만 주문의 세부 정보를 볼 수 있습니다. 저장소 계정과 관련 된 기타 세부 정보에 액세스 하거나 등 주소 같은 주문 세부 정보를 편집할 수는 없습니다.
- **데이터 상자 기여자** -지정 된 저장소 계정에 데이터를 전송 하는 순서에만 만들 수 있습니다 *저장소 계정에 대 한 쓰기 액세스를 이미 있는 경우*합니다. 저장소 계정에 액세스할 수 없는 경우 계정에 데이터를 복사 하는 Data Box 주문도 만들 수 있습니다. 이 역할이 모든 저장소 계정을 정의 하지 않으면 권한 또는 권한 부여 액세스와 관련 된 저장소 계정입니다.  

주문에 대 한 액세스를 제한 하려면 다음을 수행할 수 있습니다.

- 주문 수준에서 역할을 할당 합니다. 사용자에만 해당 특정 Data Box 주문 및 아무 상호 작용 하는 역할에 의해 정의 된 대로만 해당 권한이 있습니다.
- 리소스 그룹 수준에서 역할을 할당, 사용자가 리소스 그룹 내 모든 Data Box 주문에 대 한 액세스.

제안 된 RBAC 사용에 대 한 자세한 내용은 참조 하세요. [RBAC에 대 한 모범 사례](../role-based-access-control/overview.md#best-practice-for-using-rbac)합니다.

## <a name="track-the-order"></a>주문 추적

Azure portal을 통해 전달 운송 업체 웹 사이트를 통해 주문을 추적할 수 있습니다. 같은 메커니즘을 언제 든 지 Data Box 주문 추적 하기:

- 장치를 Azure 데이터 센터 또는 온-프레미스 상태인 경우에 순서를 추적로 이동 하 **Data Box 주문 > 개요** Azure portal에서 합니다.

    ![주문 상태 보기 및 추적 없음](media/data-box-logs/overview-view-status-1.png)

- 순서를 추적 하는 장치가 전송에서 하는 동안, 예를 들어, UPS 웹 사이트에서 미국 지역 통신 업체 웹 사이트에서 이동 합니다. 관련 된 주문 추적 번호를 제공 합니다.
- Data Box는 순서를 만들 때 제공 된 전자 메일을 기반으로 주문 상태가 변경 될 때마다에 전자 메일 알림을 보냅니다. 모든 Data Box 주문 상태 목록을 참조 하세요 [주문 상태를 볼](data-box-portal-admin.md#view-order-status)합니다. 순서와 연결 된 알림 설정을 변경 하려면 [알림 세부 정보 편집](data-box-portal-admin.md#edit-notification-details)합니다.

## <a name="query-activity-logs-during-setup"></a>설치 하는 동안 활동 로그 쿼리

- 온-프레미스에서 잠긴된 상태 데이터 상자에 도착 합니다. 주문에 대 한 Azure portal에서 사용할 수 있는 장치 자격 증명을 사용할 수 있습니다.  

    Data Box를 설정 하는 경우 장치 자격 증명에 액세스 한 모든 사용자를 알고 해야 합니다. 액세스 한 사용자를 파악 하는 **장치 자격 증명** 블레이드에서 활동 로그를 쿼리할 수 있습니다.  액세스를 포함 하는 모든 작업 **장치 세부 정보 > 자격 증명** 블레이드로 활동 로그에 기록 됩니다 `ListCredentials` 작업 합니다.

    ![활동 로그 쿼리](media/data-box-logs/query-activity-log-1.png)

- 각 로그인 Data Box는 실시간으로 기록된 합니다. 그러나이 정보는 에서만 사용할 수는 [감사 로그](#audit-logs) 순서는 성공적으로 완료 합니다.

## <a name="view-error-log-during-data-copy"></a>데이터를 복사 하는 동안 오류 로그 보기

Data Box 또는 상자에 과도 한 데이터를 데이터 복사 중 오류 파일에 복사 되는 데이터를 사용 하 여 문제가 있는 경우 생성 됩니다.

### <a name="errorxml-file"></a>Error.xml 파일

복사 작업에는 오류 없이 완료 했는지 확인 합니다. 복사 하는 동안 오류가 있는 경우에서 로그를 다운로드 합니다 **연결 및 복사** 페이지입니다.

- 512 바이트 데이터 상자에 관리 디스크 폴더에 정렬 되지 않은 파일을 복사한 경우 파일이 준비 저장소 계정에 페이지 blob으로 업로드 되지 않습니다. 로그에 오류가 표시됩니다. 파일을 제거하고 512 바이트로 정렬된 파일을 복사합니다.
- VHDX를 동적 VHD 또는 차이점 보관용 VHD (이러한 파일은 지원 되지 않음)를 복사 하는 경우 로그에 오류가 표시 됩니다.

다음 샘플은는 *error.xml* 관리 디스크를 복사 하는 경우 다른 오류에 대 한 합니다.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

다음 샘플은는 *error.xml* 페이지 blob에 복사 하는 경우 다른 오류에 대 한 합니다.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


다음 샘플은는 *error.xml* 블록 blob에 복사 하는 경우 다른 오류에 대 한 합니다.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

다음 샘플은는 *error.xml* Azure Files로 복사 하는 경우 다른 오류에 대 한 합니다.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

위의 경우 각각의 다음 단계로 계속 진행 하기 전에 오류를 해결 합니다. SMB 또는 NFS 프로토콜을 통해 Data Box로 데이터 복사 중에 수신 하는 오류에 대 한 자세한 내용은 이동 [Data Box 문제를 해결 하 고 데이터 상자 부담 스러운 문제](data-box-troubleshoot.md)합니다. REST 통해 Data Box로 데이터 복사 중에 수신 된 오류에 대 한 자세한 내용은 이동 [문제 해결 데이터 상자 Blob 저장소 문제](data-box-troubleshoot-rest.md)합니다.

## <a name="inspect-bom-during-prepare-to-ship"></a>BOM을 검사 하는 동안 배송 준비

중 자재 청구 (BOM) 또는 매니페스트 파일을 만들 때 알려진 파일의 목록을 제공 하도록 준비 합니다.

- 이 파일을 사용 하 여 실제 이름과 Data Box에 복사 된 파일 수에 대하여 확인할 수 있습니다.
- 이 파일을 사용 하 여 파일의 실제 크기에 대하여 확인할 수 있습니다.
- 있는지 확인 합니다 *crc64* 0이 아닌 문자열에 해당 합니다. <!--A null value for crc64 indicates that there was a reparse point error)-->

배송을 이동할 준비 하는 동안 받은 오류에 대 한 자세한 내용은 [Data Box 문제를 해결 하 고 데이터 상자 부담 스러운 문제](data-box-troubleshoot.md)합니다.

### <a name="bom-or-manifest-file"></a>BOM 또는 매니페스트 파일

매니페스트 파일을 BOM Data Box 장치에 복사 되는 모든 파일의 목록을 포함 합니다. BOM 파일 체크섬 뿐만 아니라 파일 이름 및 해당 크기에 있습니다. 블록 blob, 페이지 blob에서 Azure Files를 관리 되는 디스크에 복사 하 고 REST Api를 통해 복사 Data Box에 대 한 별도 BOM 파일이 만들어집니다. 배송 준비 중 로컬 웹 UI는 장치에서 BOM 파일을 다운로드할 수 있습니다.

또한 이러한 파일 Data Box 장치에 상주 하 고 Azure 데이터 센터에 연결 된 저장소 계정에 업로드 됩니다.

### <a name="bom-file-format"></a>BOM 파일 형식

BOM 또는 매니페스트 파일은 다음 일반적인 형식을 사용 합니다.

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

데이터는 블록 blob 공유 Data Box에 복사 된 경우 생성 된 매니페스트의 예제는 다음과 같습니다.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

BOM 또는 매니페스트 파일을 Azure 저장소 계정에 복사 됩니다. BOM을 사용 하거나 매니페스트 파일을 Azure에 업로드 된 파일 Data Box에 복사 된 데이터와 일치 하는지 확인 합니다.

## <a name="review-copy-log-during-upload-to-azure"></a>Azure에 업로드 하는 동안 복사 로그를 검토 하십시오.

Azure에 데이터 업로드를 사용 하는 동안에 *copylog* 만들어집니다.

### <a name="copylog"></a>Copylog

처리 되는 각 주문에 대 한 Data Box 서비스를 만듭니다 *copylog* 연결된 된 저장소 계정에 있습니다. 합니다 *copylog* 에 업로드 된 파일의 총 수 및 Azure storage 계정에 데이터 상자에서 복사는 오류가 발생 한 동안 데이터 파일의 수입니다.

순환 중복 검사 (CRC) 계산을 Azure에 업로드 하는 동안 이루어집니다. Crc 데이터 복사본에서 전후 데이터 업로드를 비교 합니다. CRC 불일치가 해당 파일을 업로드 하지 못했음을 나타냅니다.

기본적으로 로그 copylog 라는 컨테이너에 기록 됩니다. 로그는 다음 명명 규칙을 사용 하 여 저장 됩니다.

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Copylog 경로에 표시 됩니다는 **개요** 포털 블레이드입니다.

![완료 되 면 개요 블레이드에서 copylog 경로](media/data-box-logs/copy-log-path-1.png)

다음 샘플 Data Box 성공적으로 완료 된 업로드에 대 한 일반 형식의 copylog 파일을 설명 합니다.

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Azure에 업로드 오류를 사용 하 여 완료할 수도 있습니다.

![개요 블레이드에서 완료 되었으나 오류가 발생 하는 경우 copylog 경로](media/data-box-logs/copy-log-path-2.png)

다음은 오류로 업로드가 완료 되는 위치를 copylog의 예:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>데이터 지우기 후 로그 관리 권의 체인 가져오기

데이터 수정 버전 1 NIST SP 800-88 지침에 따라 Data Box 디스크에서 제거 되 면 로그 관리 권의 체인을 사용할 수 있습니다. 이러한 로그에 감사 로그 및 주문 기록이 포함 됩니다. 감사 로그를 사용 하 여 BOM 또는 매니페스트 파일을 복사 됩니다.

### <a name="audit-logs"></a>감사 로그

전원 켜기에 대 한 정보를 포함 하는 감사 로그 및 Azure 데이터 센터 외부에 데이터 상자 또는 상자에 과도 한 데이터에 대 한 액세스를 공유 합니다. 이러한 로그 위치에 있습니다. `storage-account/azuredatabox-chainofcustodylogs`

Data Box에서 감사 로그의 예제는 다음과 같습니다.

```
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

순서 기록은 Azure portal에서 사용할 수 있습니다. 순서는 전체 장치 정리 (디스크에서 데이터 지우기)를 완료 하는 경우 다음 장치 주문으로 이동할 **Order details**. ** 주문 내역을 다운로드** 옵션을 사용할 수 있습니다. 자세한 내용은 [주문 기록과 다운로드](data-box-portal-admin.md#download-order-history)합니다.

주문 기록을 통해 스크롤 하는 경우 다음을 수행 표시 합니다.

- 운송 업체 장치에 대 한 정보를 추적 합니다.
- 사용 하 여 이벤트 *SecureErase* 활동입니다. 이러한 이벤트를 디스크 상의 데이터 지우기에 해당합니다.
- 데이터 상자 로그 링크 합니다. 에 대 한 경로 *감사 로그*를 *copylogs*, 및 *BOM* 파일이 표시 됩니다.

Azure portal에서 주문 기록 로그의 예제는 다음과 같습니다.

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

- 설명 하는 방법 [Data Box 및 상자에 과도 한 데이터에 대 한 문제를 해결](data-box-troubleshoot.md)합니다.
