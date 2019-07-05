---
title: Azure 데이터 상자 과도 한 Azure Data Box에 대 한 문제 해결 | Microsoft Docs
description: 이러한 장치에 데이터를 복사 하는 경우 Azure Data Box 및 Azure 데이터 많은 상자에 표시 되는 문제를 해결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439394"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure 데이터 많은 상자와 관련 된 문제 해결

이 문서에서는 Azure Data Box를 Azure 데이터 많은 상자를 사용 하는 경우 표시 될 수 있습니다 문제를 해결 하는 방법에 대 한 정보를 자세히 설명 합니다. 문서는 Data Box 또는 데이터 상자에서 데이터를 업로드 하는 경우 데이터를 복사 하는 경우에 표시 되는 가능한 오류 목록이 포함 되어 있습니다.

## <a name="error-classes"></a>Error 클래스

Data Box 및 상자에 과도 한 데이터의 오류는 다음과 같이 요약 됩니다.

| 오류 범주 *        | 설명        | 권장 작업    |
|----------------------------------------------|---------|--------------------------------------|
| 컨테이너 또는 공유 이름 | 컨테이너 또는 공유 이름은 Azure 명명 규칙을 따르지 않습니다.  |오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유의 이름을 바꿉니다. [자세히 알아보기](#container-or-share-name-errors).  |
| 컨테이너 또는 공유 크기 제한 | 컨테이너 또는 공유의 총 데이터를 Azure 한도 초과합니다.   |오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유에서 전체 데이터를 줄입니다. [자세히 알아보기](#container-or-share-size-limit-errors).|
| 개체 또는 파일 크기 제한 | 개체 또는 컨테이너 또는 공유에서 파일을 Azure 한도 초과 합니다.|오류 목록을 다운로드 합니다. <br> 컨테이너 또는 공유에서 파일 크기를 줄입니다. [자세히 알아보기](#object-or-file-size-limit-errors). |    
| 데이터 또는 파일 형식 | 데이터 형식 또는 파일 형식이 지원 되지 않습니다. |오류 목록을 다운로드 합니다. <br> 페이지 blob 또는 관리 되는 디스크에 대 한 데이터를 미리 생성된 된 폴더에 복사한 정렬 512 바이트를 확인 합니다. [자세히 알아보기](#data-or-file-type-errors). |
| 중요 하지 않은 blob 또는 파일 오류  | Blob 또는 파일 이름은 Azure 명명 규칙을 따르지 않습니다 또는 파일 형식이 지원 되지 않습니다. | 이 blob 또는 파일을 복사할 수 있습니다 또는 이름을 변경할 수 있습니다. [이러한 오류를 해결 하는 방법을 알아봅니다](#non-critical-blob-or-file-errors)합니다. |

\* 처음 4 개의 오류 범주는 중요 한 오류 및 배송 준비를 진행 하기 전에 수정 해야 합니다.


## <a name="container-or-share-name-errors"></a>컨테이너 또는 공유 이름 오류

이 컨테이너 및 공유 이름을 관련 된 오류입니다.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**오류 설명:** 컨테이너 또는 공유 이름은 3자~63자여야 합니다. 

**제안 된 해결 방법:** 데이터를 복사한 Data Box 또는 데이터 상자 많은 share(SMB/NFS) 아래의 폴더에는 Azure 저장소 계정의 컨테이너를 됩니다. 

- 에 **연결 및 복사** 페이지 장치의 로컬 웹 UI, 다운로드 및 문제를 사용 하 여 폴더를 식별 하는 오류 파일의 이름을 검토 합니다.
- Data Box 또는 상자에 과도 한 데이터 공유 되도록 아래 폴더 이름을 변경 합니다.

    - 이름은 3 ~ 63 자 사이 있습니다.
    - 이름은 문자, 숫자 및 하이픈 하나만 사용할 수 있습니다.
    - 이름을 시작 하거나 하이픈으로 끝날 수 없습니다.
    - 이름에는 하이픈을 연속으로 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 유효 하지 않은 이름의 예: `my-folder_1`, `my`하십시오 `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 참조에 대 한 Azure 명명 규칙 [컨테이너 이름은](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)합니다.


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**오류 설명:** 컨테이너 또는 공유 이름은 문자, 숫자 또는 하이픈만으로 구성되어야 합니다.

**제안 된 해결 방법:** 데이터를 복사한 Data Box 또는 데이터 상자 많은 share(SMB/NFS) 아래의 폴더에는 Azure 저장소 계정의 컨테이너를 됩니다. 

- 에 **연결 및 복사** 페이지 장치의 로컬 웹 UI, 다운로드 및 문제를 사용 하 여 폴더를 식별 하는 오류 파일의 이름을 검토 합니다.
- Data Box 또는 상자에 과도 한 데이터 공유 되도록 아래 폴더 이름을 변경 합니다.

    - 이름은 3 ~ 63 자 사이 있습니다.
    - 이름은 문자, 숫자 및 하이픈 하나만 사용할 수 있습니다.
    - 이름을 시작 하거나 하이픈으로 끝날 수 없습니다.
    - 이름에는 하이픈을 연속으로 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 유효 하지 않은 이름의 예: `my-folder_1`, `my`하십시오 `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 참조에 대 한 Azure 명명 규칙 [컨테이너 이름은](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)합니다.

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**오류 설명:** 컨테이너 이름 및 공유 이름을 시작할 수 없습니다 또는 하이픈으로 끝날 및 하이픈을 연속으로 사용할 수 없습니다.

**제안 된 해결 방법:** 데이터를 복사한 Data Box 또는 데이터 상자 많은 share(SMB/NFS) 아래의 폴더에는 Azure 저장소 계정의 컨테이너를 됩니다. 

- 에 **연결 및 복사** 페이지 장치의 로컬 웹 UI, 다운로드 및 문제를 사용 하 여 폴더를 식별 하는 오류 파일의 이름을 검토 합니다.
- Data Box 또는 상자에 과도 한 데이터 공유 되도록 아래 폴더 이름을 변경 합니다.

    - 이름은 3 ~ 63 자 사이 있습니다.
    - 이름은 문자, 숫자 및 하이픈 하나만 사용할 수 있습니다.
    - 이름을 시작 하거나 하이픈으로 끝날 수 없습니다.
    - 이름에는 하이픈을 연속으로 사용할 수 없습니다.
    - 유효한 이름의 예: `my-folder-1`, `my-really-extra-long-folder-111`
    - 유효 하지 않은 이름의 예: `my-folder_1`, `my`하십시오 `--myfolder`, `myfolder--`, `myfolder!`

    자세한 내용은 참조에 대 한 Azure 명명 규칙 [컨테이너 이름은](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)합니다.

## <a name="container-or-share-size-limit-errors"></a>컨테이너 또는 공유 크기 제한 오류

이 컨테이너 또는 공유에서 허용 되는 데이터의 크기를 초과 하는 데이터와 관련 된 오류입니다.

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**오류 설명:** Azure 파일 공유에 5TB의 데이터가 공유를 제한합니다. 일부 공유에 대 한이 제한을 초과 했습니다.

**제안 된 해결 방법:** 에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.

오류 로그에서이 문제가 발생 하 고 해당 폴더의 파일에서 5TB 되는지 확인 하는 폴더를 식별 합니다.


## <a name="object-or-file-size-limit-errors"></a>개체 또는 파일 크기 제한 오류

이 개체 또는 Azure에서 허용 되는 파일의 최대 크기를 초과 하는 데이터와 관련 된 오류입니다. 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**오류 설명:** 파일 크기가 업로드할 수 있는 최대 파일 크기를 초과합니다.

**제안 된 해결 방법:** Blob 또는 파일 크기가 업로드에 허용 된 최대 한도 초과 합니다.

- 에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
- Blob 및 파일 크기는 Azure 개체 크기 제한을 초과 하지 있는지 확인 합니다.

## <a name="data-or-file-type-errors"></a>데이터 또는 파일 형식 오류

이 지원 되지 않는 파일 형식 또는 데이터 형식 컨테이너 또는 공유와 관련 된 오류입니다. 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**오류 설명:** Blob 또는 파일이 잘못 정렬되었습니다.

**제안 된 해결 방법:** Data Box 또는 데이터 상자 많은 지원 파일에 512 바이트 페이지 blob 공유 (예: VHD/VHDX) 정렬 합니다. 페이지 blob 공유에 복사 된 모든 데이터는 페이지 blob으로 Azure에 업로드 됩니다.

페이지 blob 공유에서 VHD/VHDX 아닌 데이터를 제거 합니다. 블록 blob 또는 일반 데이터에 대 한 Azure 파일 공유를 사용할 수 있습니다.

자세한 내용은 [개요의 페이지 blob](../storage/blobs/storage-blob-pageblob-overview.md)합니다.

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**오류 설명:** 지원 되지 않는 파일 형식 관리 디스크 공유에 없는 경우 고정된 Vhd만 허용 됩니다.

**제안 된 해결 방법:**

- 관리 디스크 만들기 고정된 Vhd를 업로드만 있는지 확인 합니다.
- VHDX 파일 또는 **동적** 하 고 **차이점 보관용** Vhd는 지원 되지 않습니다.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**오류 설명:** 관리 되는 디스크에 대 한 디렉터리의 기존 폴더에 허용 되지 않습니다. 이러한 폴더에는 고정된 Vhd만 허용 됩니다.

**제안 된 해결 방법:** 각 공유 내에서 관리 되는 디스크에 대 한 저장소 계정의 컨테이너에 해당 하는 다음 세 개의 폴더가 생성 됩니다. Premium SSD, HDD 표준 및 표준 SSD 이러한 폴더는 관리 디스크에 대 한 성능 계층에 해당합니다.

- 페이지 blob 데이터 (Vhd) 이러한 기존 폴더 중 하나에 복사 했는지 확인 합니다.
- 폴더 또는 디렉터리를 이러한 기존 폴더에 허용 되지 않습니다. 사용자가 만든 기존 폴더 내의 모든 폴더를 제거 합니다.

자세한 내용은 [managed disks로 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)합니다.

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

**오류 설명:** 기호화 된 링크는 Linux에서 허용 되지 않습니다. 

**제안 된 해결 방법:** 기호화 된 링크는 일반적으로 링크, 파이프 및 기타 이러한 파일. 링크를 제거 하거나 링크를 해결 하 고 데이터 복사 합니다.


## <a name="non-critical-blob-or-file-errors"></a>중요 하지 않은 blob 또는 파일 오류

데이터를 복사 하는 동안 표시 되는 모든 오류는 다음 섹션에 요약 되어 있습니다.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**오류 설명:** Blob 또는 파일 이름을 지원 되지 않는 제어 문자를 포함 합니다.

**제안 된 해결 방법:** Blob 또는 복사 된 파일에는 지원 되지 않는 문자를 사용 하 여 이름을 포함 합니다.

에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
지원 되지 않는 문자를 제거 하려면 파일 이름을 바꾸거나 제거 합니다.

자세한 내용은 Azure 명명 규칙에 대 한 참조 [이름의 blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)합니다.

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**오류 설명:** 잘못 된 문자를 포함 하는 blob 또는 파일 이름입니다.

**제안 된 해결 방법:** Blob 또는 복사 된 파일에는 지원 되지 않는 문자를 사용 하 여 이름을 포함 합니다.

에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
지원 되지 않는 문자를 제거 하려면 파일 이름을 바꾸거나 제거 합니다.

자세한 내용은 Azure 명명 규칙에 대 한 참조 [이름의 blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)합니다.


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**오류 설명:** Blob 또는 파일 이름이 잘못 된 문자를 사용 하 여 종료 합니다.

**제안 된 해결 방법:** Blob 또는 복사 된 파일에는 지원 되지 않는 문자를 사용 하 여 이름을 포함 합니다.

에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
지원 되지 않는 문자를 제거 하려면 파일 이름을 바꾸거나 제거 합니다.

자세한 내용은 Azure 명명 규칙에 대 한 참조 [이름의 blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)합니다.


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**오류 설명:** 너무 많은 경로 세그먼트를 포함 하는 blob 또는 파일 이름입니다.

**제안 된 해결 방법:** Blob 또는 복사 된 파일 경로 세그먼트의 최대 수를 초과 합니다. 경로 세그먼트는 연속 구분 문자를 예를 들어, 슬래시 사이의 문자열 /입니다.

- 에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
- 있는지 확인 합니다 [blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 따라야 합니다.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**오류 설명:** Blob 또는 파일 이름이 너무 깁니다.

**제안 된 해결 방법:** Blob 또는 파일 이름에는 최대 길이 초과 합니다.

- 에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
- Blob 이름을 1,024 자를 초과할 수 없습니다.
- 제거 하거나 이름을 blob 또는 파일 이름에는 1024 자를 초과 하지 않습니다.

자세한 내용은 blob 이름 및 파일 이름에 대 한 Azure 명명 규칙을 참조 하세요.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**오류 설명:** Blob 또는 파일의 세그먼트 중 하나가 너무 깁니다.

**제안 된 해결 방법:** Blob 또는 파일 이름에서 경로 세그먼트 중 하나는 최대 문자 수를 초과합니다. 경로 세그먼트는 연속 구분 문자를 예를 들어, 슬래시 사이의 문자열 /입니다.

- 에 **연결 및 복사** 페이지의 로컬 웹 UI에서 다운로드 하 고 오류 파일을 검토 합니다.
- 있는지 확인 합니다 [blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 따라야 합니다.


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**오류 설명:** 관리 디스크 공유에 대 한 부적절 한 컨테이너 이름이 지정 됩니다.

**제안 된 해결 방법:** 각 공유 내에서 관리 되는 디스크에 대 한 저장소 계정의 컨테이너에 해당 하는 다음 폴더가 생성 됩니다. Premium SSD, HDD 표준 및 표준 SSD 이러한 폴더는 관리 디스크에 대 한 성능 계층에 해당합니다.

- 페이지 blob 데이터 (Vhd) 이러한 기존 폴더 중 하나에 복사 했는지 확인 합니다. 이러한 기존 컨테이너에서 데이터에만 Azure에 업로드 됩니다.
- Premium SSD, HDD 표준 및 표준 SSD와 같은 수준에서 생성 된 다른 폴더는 올바른 성능 계층에 해당 하지 않습니다 및 사용할 수 없습니다.
- 성능 계층 외부에 생성 하는 폴더나 파일을 제거 합니다.

자세한 내용은 [managed disks로 복사](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)합니다.


## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 합니다 [데이터 상자 Blob 저장소에 대 한 시스템 요구 사항](data-box-system-requirements-rest.md)합니다.
