---
title: Microsoft Storage 탐색기를 사용 하 여 컨테이너 및 blob에 대 한 SAS (공유 액세스 서명) 토큰 만들기
description: Microsoft Storage 탐색기를 사용 하 여 컨테이너 및 blob에 대 한 SAS (공유 액세스 토큰)를 만드는 방법
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 49813a29009e04c81dae59a7d4da2bae411e07b2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642467"
---
# <a name="create-sas-tokens-for-document-translation"></a>문서 변환에 대 한 SAS 토큰 만들기

이 문서에서는 Azure Storage 탐색기 또는 Azure Portal를 사용 하 여 공유 액세스 서명 (SAS) 토큰을 만드는 방법에 대해 알아봅니다. SAS 토큰은 Azure 저장소 계정의 리소스에 대 한 안전한 위임 된 액세스를 제공 합니다.

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>Azure Storage 탐색기를 사용 하 여 SAS 토큰 만들기

### <a name="prerequisites"></a>필수 구성 요소

* Windows, macOS 또는 Linux 개발 환경에 [**Azure Storage 탐색기**](/azure/vs-azure-tools-storage-manage-with-storage-explorer) 앱이 설치 되어 있어야 합니다. Azure Storage 탐색기은 Azure 클라우드 저장소 리소스를 쉽게 관리할 수 있는 무료 도구입니다.
* Azure Storage 탐색기 앱이 설치 된 후 문서 번역에 사용 하는 [저장소 계정에 연결](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#connect-to-a-storage-account-or-service) 합니다.

### <a name="create-your-tokens"></a>토큰 만들기

### <a name="sas-tokens-for-containers"></a>[컨테이너에 대 한 SAS 토큰](#tab/Containers)

1. 로컬 컴퓨터에서 Azure Storage 탐색기 앱을 열고 연결 된 **저장소 계정** 으로 이동 합니다.
1. 저장소 계정 노드를 확장 하 고 **Blob 컨테이너** 를 선택 합니다.
1. Blob 컨테이너 노드를 확장 하 고 저장소 **컨테이너** 노드를 마우스 오른쪽 단추로 클릭 하거나 옵션 메뉴를 표시 합니다.
1. 옵션 메뉴에서 **공유 액세스 서명 가져오기** ...를 선택 합니다.
1. **공유 액세스 서명** 창에서 다음과 같이 선택 합니다.
    * **액세스 정책을** 선택 합니다 (기본값은 없음).
    * 서명 된 키 **시작** 및 **만료** 날짜와 시간을 지정 합니다. 수명이 생성 된 후에는 SAS를 취소할 수 없기 때문에 짧은 수명이 권장 됩니다.
    * 시작 및 만료 날짜 및 시간에 대 한 **표준 시간대** 를 선택 합니다 (기본값은 Local).
    * 적절 한 확인란을 선택 하거나 선택을 취소 하 여 컨테이너 **권한을** 정의 합니다.
    * 검토 하 고 **만들기** 를 선택 합니다.

1. **컨테이너 이름,** **URI** 및 컨테이너에 대 한 **쿼리 문자열이** 포함 된 새 창이 표시 됩니다.  
1. **컨테이너, URI 및 쿼리 문자열 값을 복사 하 여 안전한 위치에 붙여 넣습니다. 이는 한 번만 표시 되며 창을 닫은 후에는 검색할 수 없습니다.**
1. SAS URL을 생성 하려면 저장소 서비스의 URL에 SAS 토큰 (URI)을 추가 합니다.

### <a name="sas-tokens-for-blobs"></a>[Blob에 대 한 SAS 토큰](#tab/blobs)

1. 로컬 컴퓨터에서 Azure Storage 탐색기 앱을 열고 연결 된 **저장소 계정** 으로 이동 합니다.
1. 저장소 노드를 확장 하 고 **Blob 컨테이너** 를 선택 합니다.
1. Blob 컨테이너 노드를 확장 하 고 **컨테이너** 노드를 선택 하 여 주 창에 콘텐츠를 표시 합니다.
1. SAS 액세스를 위임 하려는 blob을 선택 하 고 마우스 오른쪽 단추를 클릭 하 여 옵션 메뉴를 표시 합니다.
1. 옵션 메뉴에서 **공유 액세스 서명 가져오기** ...를 선택 합니다.
1. **공유 액세스 서명** 창에서 다음과 같이 선택 합니다.
    * **액세스 정책을** 선택 합니다 (기본값은 없음).
    * 서명 된 키 **시작** 및 **만료** 날짜와 시간을 지정 합니다. 수명이 생성 된 후에는 SAS를 취소할 수 없기 때문에 짧은 수명이 권장 됩니다.
    * 시작 및 만료 날짜 및 시간에 대 한 **표준 시간대** 를 선택 합니다 (기본값은 Local).
    * 적절 한 확인란을 선택 하거나 선택을 취소 하 여 컨테이너 **권한을** 정의 합니다.
    * 검토 하 고 **만들기** 를 선택 합니다.
1. Blob에 대 한 **blob** 이름, **URI** 및 **쿼리 문자열이** 포함 된 새 창이 표시 됩니다.  
1. **Blob, URI 및 쿼리 문자열 값을 복사 하 여 안전한 위치에 붙여 넣습니다. 이는 한 번만 표시 되며 창을 닫은 후에는 검색할 수 없습니다.**
1. SAS URL을 생성 하려면 저장소 서비스의 URL에 SAS 토큰 (URI)을 추가 합니다.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Azure Portal blob에 대 한 SAS 토큰 만들기

> [!NOTE]
> Azure Portal에서 직접 컨테이너에 대 한 SAS 토큰을 만드는 것은 현재 지원 되지 않습니다. 그러나 [**Azure Storage 탐색기**](#create-sas-tokens-with-azure-storage-explorer) 를 사용 하 여 SAS 토큰을 만들거나 [프로그래밍 방식으로](/azure/storage/blobs/sas-service-create)작업을 완료할 수 있습니다.

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>필수 구성 요소

시작 하려면 다음이 필요 합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들**](https://azure.microsoft.com/free/)수 있습니다.
* Cognitive Services 다중 서비스 리소스가 **아니라** [**변환기**](https://ms.portal.azure.com/#create/Microsoft) 서비스 리소스입니다.   [새 Azure 리소스 만들기를](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)참조 하세요.  
* [**Azure blob storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Azure Storage에 대한 모든 액세스는 스토리지 계정을 통해 수행됩니다.

### <a name="create-your-tokens"></a>토큰 만들기

[Azure Portal](https://ms.portal.azure.com/#home) 로 이동 하 여 다음과 같이 이동 합니다.  

 **저장소 계정** → 컨테이너 → **컨테이너** **→ 사용자** **blob**

1. 페이지 맨 위 근처의 메뉴에서 **SAS 생성** 을 선택 합니다.

1. **서명 방법** → **사용자 위임 키** 를 선택 합니다.

1. 적절 한 확인란을 선택 하거나 선택을 취소 하 여 **사용 권한을** 정의 합니다.

1. 서명 된 키 **시작** 및 **만료** 시간을 지정 합니다.

1. 허용 되는 **ip** 주소 필드는 선택 사항이 며 요청을 수락 하는 ip 주소 또는 ip 주소의 범위를 지정 합니다. 요청 IP 주소가 SAS 토큰에 지정 된 IP 주소 또는 주소 범위와 일치 하지 않으면 권한이 부여 되지 않습니다.

1. 허용 되는 **프로토콜** 필드는 선택 사항이 며 SAS를 사용 하 여 만든 요청에 허용 되는 프로토콜을 지정 합니다. 기본값은 HTTPS입니다.

1. 검토 한 후 **SAS 토큰 및 URL 생성** 을 선택 합니다.

1. **BLOB sas 토큰** 쿼리 문자열 및 **blob sas URL** 이 창의 아래쪽 영역에 표시 됩니다.  

1. **Blob SAS 토큰 및 URL 값을 복사 하 여 안전한 위치에 붙여 넣습니다. 한 번만 표시 되며 창을 닫은 후에는 검색할 수 없습니다.**

1. SAS URL을 생성 하려면 저장소 서비스의 URL에 SAS 토큰 (URI)을 추가 합니다.

## <a name="learn-more"></a>자세한 정보

* [프로그래밍 방식으로 blob 또는 컨테이너에 대 한 SAS 토큰 만들기](/azure/storage/blobs/sas-service-create)
* [디렉터리, 컨테이너 또는 blob에 대 한 사용 권한](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [문서 번역 시작](get-started-with-document-translation.md)
>
>
