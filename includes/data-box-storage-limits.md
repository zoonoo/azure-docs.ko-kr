---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: 1935e6a95906473fdb4c1c25039cfbca5bace526
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86202664"
---
이 섹션에서는 Azure Storage 서비스에 대한 제한 및 Azure Files, Azure 블록 Blob 및 Azure 페이지 Blob에 대한 필수 명명 규칙을 Data Box 서비스에 적용 가능한 것으로 설명합니다. 스토리지 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure Storage 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 blob 및 페이지 blob 규칙](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Storage 서비스 제한을 초과 하거나 Azure Files/Blob 명명 규칙을 따르지 않는 파일이 나 디렉터리가 있는 경우 이러한 파일이 나 디렉터리는 Data Box 서비스를 통해 Azure Storage에 수집 되지 않습니다.
