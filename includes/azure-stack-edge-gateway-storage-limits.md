---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901067"
---
이 섹션에서는 Azure Storage 서비스에 대 한 제한 및 Azure Files, Azure 블록 blob 및 Azure 페이지 blob에 대 한 필수 명명 규칙 (Azure Stack Edge 서비스에 해당)에 대해 설명 합니다. 스토리지 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure Storage 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 blob 및 페이지 blob 규칙](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Storage 서비스 제한을 초과 하거나 Azure Files/Blob 명명 규칙을 따르지 않는 파일이 나 디렉터리가 있는 경우 이러한 파일이 나 디렉터리는 Azure Stack Edge 서비스를 통해 Azure Storage에 수집 되지 않습니다.