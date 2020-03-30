---
title: Azure 데이터 상자, Azure 데이터 상자 중대성 문제 해결
description: 이러한 장치에 데이터를 복사할 때 Azure 데이터 상자 및 Azure 데이터 상자 헤비에서 볼 수 있는 문제를 해결하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560068"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure 데이터 상자 및 Azure 데이터 상자 중대와 관련된 문제 해결

이 문서에서는 Azure 데이터 상자 또는 Azure 데이터 상자 헤비를 사용할 때 발생할 수 있는 문제를 해결하는 방법에 대한 정보를 자세히 설명합니다. 이 문서에는 데이터가 데이터 상자에 복사될 때 또는 데이터 상자에서 데이터를 업로드할 때 발생할 수 있는 오류 목록이 포함되어 있습니다.

## <a name="error-classes"></a>오류 클래스

데이터 상자 및 데이터 상자 헤비의 오류는 다음과 같이 요약됩니다.

| 오류 범주*        | 설명        | 권장 작업    |
|----------------------------------------------|---------|--------------------------------------|
| 컨테이너 또는 공유 이름 | 컨테이너 또는 공유 이름은 Azure 이름 지정 규칙을 따르지 않습니다.  |오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유의 이름을 바꿉니다. [자세히 알아봅니다](#container-or-share-name-errors).  |
| 컨테이너 또는 공유 크기 제한 | 컨테이너 또는 공유의 총 데이터가 Azure 제한을 초과합니다.   |오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유의 전체 데이터를 줄입니다. [자세히 알아봅니다](#container-or-share-size-limit-errors).|
| 개체 또는 파일 크기 제한 | 컨테이너 또는 공유의 개체 또는 파일이 Azure 제한을 초과합니다.|오류 목록을 다운로드합니다. <br> 컨테이너 또는 공유의 파일 크기를 줄입니다. [자세히 알아봅니다](#object-or-file-size-limit-errors). |    
| 데이터 또는 파일 형식 | 데이터 형식 또는 파일 형식이 지원되지 않습니다. |오류 목록을 다운로드합니다. <br> 페이지 Blob 또는 관리 디스크의 경우 데이터가 512바이트로 정렬되어 미리 작성된 폴더에 복사되어 있는지 확인합니다. [자세히 알아봅니다](#data-or-file-type-errors). |
| 중요하지 않은 Blob 또는 파일 오류  | Blob 또는 파일 이름은 Azure 명명 규칙을 따르지 않거나 파일 형식이 지원되지 않습니다. | 이러한 Blob 또는 파일은 복사되지 않거나 이름이 변경될 수 있습니다. [이러한 오류를 수정하는 방법에 대해 알아봅니다.](#non-critical-blob-or-file-errors) |

\*처음 네 개의 오류 범주는 중요한 오류이며 배송 준비를 진행하기 전에 수정해야 합니다.


## <a name="container-or-share-name-errors"></a>컨테이너 또는 공유 이름 오류

컨테이너 및 공유 이름과 관련된 오류입니다.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**오류 설명:** 컨테이너 또는 공유 이름은 3자에서 63자 사이여야 합니다. 

**제안된 해결 방법:** 데이터를 복사한 데이터 상자 또는 데이터 상자 무거운 공유(SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 식별합니다.
- 데이터 상자 또는 데이터 상자 무거운 공유에서 폴더 이름을 변경하려면 다음을 확인합니다.

    - 이름은 3에서 63자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 있을 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝낼 수 없습니다.
    - 이름에 는 연속된 하이픈을 가질 수 없습니다.
    - 유효한 이름의 예: `my-folder-1``my-really-extra-long-folder-111`
    - 유효하지 않은 이름의 예: `my-folder_1`" `my` `--myfolder` `myfolder--``myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)대한 Azure 명명 규칙을 참조하십시오.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**오류 설명:** 컨테이너 또는 공유 이름은 문자, 숫자 또는 하이픈으로만 구성되어야 합니다.

**제안된 해결 방법:** 데이터를 복사한 데이터 상자 또는 데이터 상자 무거운 공유(SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 식별합니다.
- 데이터 상자 또는 데이터 상자 무거운 공유에서 폴더 이름을 변경하려면 다음을 확인합니다.

    - 이름은 3에서 63자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 있을 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝낼 수 없습니다.
    - 이름에 는 연속된 하이픈을 가질 수 없습니다.
    - 유효한 이름의 예: `my-folder-1``my-really-extra-long-folder-111`
    - 유효하지 않은 이름의 예: `my-folder_1`" `my` `--myfolder` `myfolder--``myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)대한 Azure 명명 규칙을 참조하십시오.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**오류 설명:** 컨테이너 이름과 공유 이름은 하이픈으로 시작하거나 끝낼 수 없으며 연속된 하이픈을 가질 수 없습니다.

**제안된 해결 방법:** 데이터를 복사한 데이터 상자 또는 데이터 상자 무거운 공유(SMB/NFS) 아래의 폴더는 저장소 계정의 Azure 컨테이너가 됩니다. 

- 장치 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토하여 문제가 있는 폴더 이름을 식별합니다.
- 데이터 상자 또는 데이터 상자 무거운 공유에서 폴더 이름을 변경하려면 다음을 확인합니다.

    - 이름은 3에서 63자 사이입니다.
    - 이름에는 문자, 숫자 및 하이픈만 있을 수 있습니다.
    - 이름은 하이픈으로 시작하거나 끝낼 수 없습니다.
    - 이름에 는 연속된 하이픈을 가질 수 없습니다.
    - 유효한 이름의 예: `my-folder-1``my-really-extra-long-folder-111`
    - 유효하지 않은 이름의 예: `my-folder_1`" `my` `--myfolder` `myfolder--``myfolder!`

    자세한 내용은 [컨테이너 이름](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) 및 [공유 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)대한 Azure 명명 규칙을 참조하십시오.

## <a name="container-or-share-size-limit-errors"></a>컨테이너 또는 공유 크기 제한 오류

컨테이너 또는 공유에서 허용되는 데이터 크기를 초과하는 데이터와 관련된 오류입니다.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**오류 설명:** Azure 파일 공유는 공유를 5TB의 데이터로 제한합니다. 일부 주식의 경우 이 한도가 초과되었습니다.

**제안된 해결 방법:** 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.

오류 로그에서 이 문제가 있는 폴더를 식별하고 해당 폴더의 파일이 5TB 미만인지 확인합니다.


## <a name="object-or-file-size-limit-errors"></a>개체 또는 파일 크기 제한 오류

이러한 오류는 개체의 최대 크기를 초과하는 데이터 또는 Azure에서 허용되는 파일과 관련된 오류입니다. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**오류 설명:** 파일 크기가 업로드의 최대 파일 크기를 초과합니다.

**제안된 해결 방법:** Blob 또는 파일 크기가 업로드할 수 있는 최대 제한을 초과합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- Blob 및 파일 크기가 Azure 개체 크기 제한을 초과하지 않는지 확인합니다.

## <a name="data-or-file-type-errors"></a>데이터 또는 파일 형식 오류

이러한 오류는 지원되지 않는 파일 형식 또는 컨테이너 또는 공유에 있는 데이터 형식과 관련된 오류입니다. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**오류 설명:** Blob 또는 파일이 잘못 정렬되었습니다.

**제안된 해결 방법:** 데이터 박스 또는 데이터 박스 헤비의 페이지 Blob 공유는 512바이트정렬된 파일(예: VHD/VHDX)만 지원합니다. 페이지 Blob 공유에 복사된 모든 데이터는 Azure에 페이지 Blob으로 업로드됩니다.

페이지 Blob 공유에서 VHD/VHDX가 아닌 데이터를 제거합니다. 일반 데이터에 대해 블록 Blob 또는 Azure 파일에 공유를 사용할 수 있습니다.

자세한 내용은 [페이지 Blob 의 개요를](../storage/blobs/storage-blob-pageblob-overview.md)참조하십시오.

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**오류 설명:** 지원되지 않는 파일 형식이 관리디스크 공유에 있습니다. 고정 된 VHD만 허용됩니다.

**제안된 해결 방법:**

- 고정 된 VHD만 업로드하여 관리되는 디스크를 만들어야 합니다.
- VHDX 파일 또는 **동적** 및 **기타** VHD는 지원되지 않습니다.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**오류 설명:** 디렉토리는 관리되는 디스크에 대한 기존 폴더에서 허용되지 않습니다. 이러한 폴더에는 고정 된 VHD만 허용됩니다.

**제안된 해결 방법:** 관리 디스크의 경우 각 공유 내에서 저장소 계정의 컨테이너에 해당하는 다음 세 개의 폴더가 만들어집니다: 프리미엄 SSD, 표준 HDD 및 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당합니다.

- 페이지 Blob 데이터(VHD)를 이러한 기존 폴더 중 하나에 복사해야 합니다.
- 이러한 기존 폴더에는 폴더 또는 디렉터리가 허용되지 않습니다. 기존 폴더 내에서 만든 폴더를 모두 제거합니다.

자세한 내용은 [관리디스크 복사를 참조하세요.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**오류 설명:** 리눅스에서는 기호 링크가 허용되지 않습니다. 

**제안된 해결 방법:** 기호 링크는 일반적으로 링크, 파이프 및 기타 파일입니다. 링크를 제거하거나 링크를 해결하고 데이터를 복사합니다.


## <a name="non-critical-blob-or-file-errors"></a>중요하지 않은 Blob 또는 파일 오류

데이터 복사 중에 볼 수 있는 Blob, 파일 또는 컨테이너의 이름과 관련된 중요하지 않은 모든 오류는 다음 섹션에 요약되어 있습니다. 이러한 오류가 있는 경우 Azure 명명 규칙을 준수하도록 이름이 수정됩니다. 데이터 업로드에 대한 해당 주문 상태는 **경고와 함께 완료됩니다.**  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**오류 설명:** Blob 또는 파일 이름에는 지원되지 않는 컨트롤 문자가 포함되어 있습니다.

**제안된 해결 방법:** 복사한 Blob 또는 파일에는 지원되지 않는 문자가 있는 이름이 포함됩니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
지원되지 않는 문자를 제거하기 위해 파일을 제거하거나 이름을 바꿉니다.

자세한 내용은 [Blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)대한 Azure 이름 지정 규칙을 참조하십시오.

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**오류 설명:** Blob 또는 파일 이름에는 잘못된 문자가 포함되어 있습니다.

**제안된 해결 방법:** 복사한 Blob 또는 파일에는 지원되지 않는 문자가 있는 이름이 포함됩니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
지원되지 않는 문자를 제거하기 위해 파일을 제거하거나 이름을 바꿉니다.

자세한 내용은 [Blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)대한 Azure 이름 지정 규칙을 참조하십시오.


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**오류 설명:** Blob 또는 파일 이름은 잘못된 문자로 끝납니다.

**제안된 해결 방법:** 복사한 Blob 또는 파일에는 지원되지 않는 문자가 있는 이름이 포함됩니다.

로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
지원되지 않는 문자를 제거하기 위해 파일을 제거하거나 이름을 바꿉니다.

자세한 내용은 [Blob 이름](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) 및 [파일 이름에](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)대한 Azure 이름 지정 규칙을 참조하십시오.


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**오류 설명:** Blob 또는 파일 이름에 너무 많은 경로 세그먼트가 포함되어 있습니다.

**제안된 해결 방법:** 복사한 Blob 또는 파일이 경로 세그먼트의 최대 수를 초과합니다. 경로 세그먼트는 연속된 구분 기호 문자(예: 정방향 슬래시 /) 사이의 문자열입니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- [Blob 이름과](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) [파일 이름이](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수하는지 확인합니다.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**오류 설명:** Blob 또는 파일 이름이 너무 깁니다.

**제안된 해결 방법:** Blob 또는 파일 이름이 최대 길이를 초과합니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- Blob 이름은 1,024자를 초과해서는 안 됩니다.
- 이름이 1024자를 초과하지 않도록 Blob 또는 파일의 이름을 제거하거나 이름을 바꿉니다.

자세한 내용은 Blob 이름 및 파일 이름에 대한 Azure 이름 지정 규칙을 참조하세요.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**오류 설명:** Blob 또는 파일 이름의 세그먼트 중 하나가 너무 깁니다.

**제안된 해결 방법:** Blob 또는 파일 이름의 경로 세그먼트 중 하나가 최대 문자 수를 초과합니다. 경로 세그먼트는 연속된 구분 기호 문자(예: 정방향 슬래시 /) 사이의 문자열입니다.

- 로컬 웹 UI의 **연결 및 복사** 페이지에서 오류 파일을 다운로드하고 검토합니다.
- [Blob 이름과](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) [파일 이름이](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) Azure 명명 규칙을 준수하는지 확인합니다.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**오류 설명:** 관리되는 디스크 공유에 대해 부적절한 컨테이너 이름이 지정됩니다.

**제안된 해결 방법:** 관리 디스크의 경우 각 공유 내에서 저장소 계정의 컨테이너에 해당하는 다음 폴더가 만들어집니다: 프리미엄 SSD, 표준 HDD 및 표준 SSD. 이러한 폴더는 관리 디스크의 성능 계층에 해당합니다.

- 페이지 Blob 데이터(VHD)를 이러한 기존 폴더 중 하나에 복사해야 합니다. 이러한 기존 컨테이너의 데이터만 Azure에 업로드됩니다.
- 프리미엄 SSD, 표준 HDD 및 표준 SSD와 동일한 수준으로 생성된 다른 폴더는 유효한 성능 계층에 해당하지 않으며 사용할 수 없습니다.
- 성능 계층 외부에서 만든 파일 또는 폴더를 제거합니다.

자세한 내용은 [관리디스크 복사를 참조하세요.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)


## <a name="next-steps"></a>다음 단계

- [데이터 상자 Blob 저장소 시스템 요구 사항에](data-box-system-requirements-rest.md)대해 알아봅니다.
