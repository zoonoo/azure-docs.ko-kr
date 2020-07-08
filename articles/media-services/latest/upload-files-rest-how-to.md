---
title: REST를 사용 하 여 Azure Media Services v3 계정에 파일 업로드 Microsoft Docs
description: 자산을 만들고 업로드하여 Media Services에 미디어 콘텐츠를 가져오는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76705771"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>REST를 사용 하 여 Media Services v3 계정에 파일 업로드

Media Services에서 자산에 연결된 Blob 컨테이너에 디지털 파일을 업로드합니다. [자산](https://docs.microsoft.com/rest/api/media/operations/asset) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 닫힌 캡션 파일 (및 이러한 파일에 대 한 메타 데이터)이 포함 될 수 있습니다. 자산의 컨테이너에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

이 문서에서는 REST를 사용 하 여 로컬 파일을 업로드 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [자산 개념](assets-concept.md)을 검토합니다.
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 

## <a name="create-an-asset"></a>자산 만들기

이 섹션에서는 새 자산을 만드는 방법을 보여 줍니다.

1. 자산 **Assets**  ->  **만들기 또는 업데이트 자산**을 선택 합니다.
2. **보내기**를 누릅니다.

    ![자산 만들기](./media/upload-files/postman-create-asset.png)

새로 만든 자산에 대한 정보를 포함하는 **응답**이 표시됩니다.

## <a name="get-a-sas-url-with-read-write-permissions"></a>읽기/쓰기 권한을 사용하여 SAS URL을 가져오기 

이 섹션에서는 만든 자산에 대해 생성된 SAS URL을 가져오는 방법을 보여 줍니다. 읽기/쓰기 권한을 사용하여 SAS URL을 만든 후 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 수 있습니다.

1. 자산 **Assets**  ->  **url 나열 자산을**선택 합니다.
2. **보내기**를 누릅니다.

    ![파일 업로드](./media/upload-files/postman-create-sas-locator.png)

자산 URL에 대한 정보를 포함하는 **응답**이 표시됩니다. 첫 번째 URL을 복사한 후 파일을 업로드하는 데 사용합니다.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>업로드 URL을 사용하여 Blob Storage에 파일 업로드

Azure Storage Api 또는 Sdk (예: [저장소 REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.net sdk](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용 합니다.

## <a name="next-steps"></a>다음 단계

[자습서: URL을 기반으로 원격 파일 인코딩 및 비디오 스트리밍-REST](stream-files-tutorial-with-rest.md)
