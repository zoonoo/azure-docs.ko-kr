---
title: Azure Data Box 제한 | Microsoft Docs
description: Microsoft Azure Data Box 구성 요소 및 연결에 대한 시스템 제한 및 권장 크기를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 11/16/2020
ms.author: alkohli
ms.openlocfilehash: 00ba711661d69bbaf53ff51fb2cb833d9b1c1ae2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660426"
---
# <a name="azure-data-box-limits"></a>Azure Data Box 제한

Microsoft Azure Data Box를 배포하고 운영할 때 이러한 제한을 고려합니다. 다음 표에서는 Data Box에 대한 이러한 제한을 설명합니다.

## <a name="data-box-service-limits"></a>Data Box 서비스 제한

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box 제한

- 가져오기 및 내보내기에 대 한 최대 5억 파일을 저장할 수 Data Box.
- Data Box는 클라우드에서 최대 512의 컨테이너 또는 공유를 지원 합니다. 사용자 공유 내의 최상위 디렉터리는 클라우드의 컨테이너 또는 Azure 파일 공유로 사용 됩니다. 
- ReFS 메타 데이터 공간 사용으로 인해 Data Box 사용 용량은 80 TB 미만이 될 수 있습니다.
- Data Box는 NFS 공유에서 한 번에 최대 10 개의 클라이언트 연결을 지원 합니다.

## <a name="azure-storage-limits"></a>Azure Storage 제한

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>데이터 업로드 주의 사항


### <a name="for-import-order"></a>가져오기 순서

가져오기 순서에 대 한 Data Box 주의 사항은 다음과 같습니다.

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

## <a name="for-export-order"></a>내보내기 주문

내보내기 주문에 대 한 Data Box 주의 사항은 다음과 같습니다.

- Data Box는 Windows 기반 장치 이며 대/소문자를 구분 하는 파일 이름을 지원 하지 않습니다. 예를 들어 Azure에는 대/소문자가 다른 두 개의 다른 파일이 있습니다. 장치에서 파일을 덮어쓸 때 Data Box를 사용 하 여 이러한 파일을 내보내지 마세요.
- 입력 파일이 나 태그에 동일한 데이터를 참조 하는 태그가 중복 된 경우 내보내기 Data Box 파일을 건너뛰거나 덮어쓸 수 있습니다. Azure Portal 표시 되는 데이터의 파일 수와 크기는 장치에 있는 데이터의 실제 크기와 다를 수 있습니다. 
- Data Box은 SMB를 통해 Windows 기반 시스템으로 데이터를 내보내고 파일 및 폴더에 대 한 SMB 제한 사항에 의해 제한 됩니다. 지원 되지 않는 이름이 있는 파일 및 폴더는 내보내지 않습니다.
- 접두사에서 컨테이너로의 1:1 매핑이 있습니다.
- 최대 파일 이름 크기는 1024 자입니다. 이 길이를 초과 하는 파일 이름은 내보내지 않습니다.
- *Xml* 파일의 중복 접두사 (주문 생성 중에 업로드 됨)를 내보냅니다. 중복 접두사는 무시 되지 않습니다.
- 페이지 blob 및 컨테이너 이름은 대/소문자를 구분 합니다. 대/소문자가 일치 하지 않으면 blob 및/또는 컨테이너가 검색 되지 않습니다.
 

## <a name="azure-storage-account-size-limits"></a>Azure Storage 계정 크기 제한

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure 개체 크기 제한

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure 블록 Blob과 페이지 Blob 및 파일 명명 규칙

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
