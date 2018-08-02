---
title: Azure Storage에서 정적 웹 사이트 호스팅(미리 보기) | Microsoft Docs
description: Azure Storage는 이제 최신 웹 응용 프로그램 호스팅에 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅을 제공합니다(미리 보기).
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: e53b573a27f0b1462ccf1170bbde2f8af01d0d3a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397478"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Azure Storage에서 정적 웹 사이트 호스팅(미리 보기)
Azure Storage는 이제 Azure에서 비용 효율적이고 확장 가능한 최신 웹 응용 프로그램을 배포할 수 있도록 하는 정적 웹 사이트를 호스팅을 제공합니다(미리 보기). 정적 웹 사이트에서 웹 페이지는 정적 콘텐츠 및 JavaScript 또는 기타 클라이언트 쪽 코드를 포함합니다. 반면 동적 웹 사이트는 서버 쪽 코드에 따라 다르며 [Azure Web Apps](/app-service/app-service-web-overview.md)를 사용하여 호스팅할 수 있습니다.

배포는 탄력적이고, 비용 효율적인 모델을 향해 이동하므로 서버 관리의 필요 없이 웹 콘텐츠를 제공하는 능력은 중요합니다. Azure Storage에서 정적 웹 사이트 호스팅의 도입으로 [Azure Functions](/azure-functions/functions-overview.md) 및 기타 PaaS 서비스를 활용하는 서버가 없는 아키텍처를 가진 풍부한 백 엔드 기능을 활성화할 수 있습니다.

## <a name="how-does-it-work"></a>작동 원리
저장소 계정에서 정적 웹 사이트를 활성화하면 양식 `<account-name>.<zone-name>.web.core.windows.net`의 새 웹 서비스 엔드포인트가 생성됩니다.

웹 서비스 엔드포인트는 항상 익명 읽기 액세스를 허용하고, 서비스 오류에 대한 응답으로 서식이 지정된 HTML 페이지를 반환하고, 개체 읽기 작업만을 허용합니다. 웹 서비스 엔드포인트는 루트 및 모든 하위 디렉터리에 대한 요청된 디렉터리에서 인덱스 문서를 반환합니다. 저장소 서비스에서 404 오류를 반환할 때 웹 엔드포인트는 구성한 경우 사용자 지정 오류 문서를 반환합니다.

정적 웹 사이트에 대한 콘텐츠는 "$web"이라는 특별한 컨테이너에서 호스팅됩니다. 사용 프로세스의 일부로 이미 존재하지 않는 경우 "$web"이 생성됩니다. 웹 엔드포인트를 사용하여 계정 루트에서 "$web"의 콘텐츠에 액세스할 수 있습니다. 예를 들어 `https://contoso.z4.web.core.windows.net/`은 해당 이름의 문서가 $web의 루트 디렉터리에 있는 경우 웹 사이트에 대해 구성한 인덱스 문서를 반환합니다.

웹 사이트에 콘텐츠를 업로드하는 경우 Blob 저장소 엔드포인트를 사용합니다. 계정 루트에서 액세스할 수 있는 'image.jpg'라는 Blob을 업로드하려면 다음 URL `https://contoso.blob.core.windows.net/$web/image.jpg`를 사용합니다. 해당 웹 엔드포인트 `https://contoso.z4.web.core.windows.net/image.jpg`의 웹 브라우저에서 업로드된 이미지를 볼 수 있습니다.


## <a name="custom-domain-names"></a>사용자 지정 도메인 이름
사용자 지정 도메인을 사용하여 웹 콘텐츠를 호스팅할 수 있습니다. 이를 수행하려면 [Azure Storage 계정에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)의 지침을 따릅니다. HTTPS를 통해 사용자 지정 도메인 이름에서 호스팅되는 웹 사이트에 액세스하려면 [Azure CDN을 사용하여 HTTPS를 통해 사용자 지정 도메인으로 Blob에 액세스](storage-https-custom-domain-cdn.md)를 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구
정적 웹 사이트 호스팅은 추가 비용 없이 제공됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="quickstart"></a>빠른 시작
### <a name="azure-portal"></a>Azure portal
Azure Storage에서 웹 응용 프로그램 호스팅을 시작하려면 Azure Portal을 사용하여 기능을 구성하고 왼쪽 탐색 모음의 "설정"에서 "정적 웹 사이트(미리 보기)"를 클릭할 수 있습니다. "사용"을 클릭하고 인덱스 문서의 이름 및 (경우에 따라) 사용자 지정 오류 문서 경로를 입력합니다.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

정적 웹 사이트 사용의 일부로 생성된 "$web" 컨테이너에 웹 자산을 업로드합니다. Azure Portal에서 직접 수행하거나 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 전체 디렉터리 구조를 업로드할 수 있습니다. 구성한 이름으로 인덱스 문서를 포함해야 합니다. 이 예제에서 문서의 이름은 "index.html"입니다.

> [!NOTE]
> 문서 이름은 대/소문자를 구분하므로 저장소파의 일 이름과 정확히 일치해야 합니다.

마지막으로 웹 엔드포인트로 이동하여 웹 사이트를 테스트합니다.

## <a name="faq"></a>FAQ
**정적 웹 사이트는 모든 저장소 계정 형식에 사용할 수 있나요?**  
아니요, 정적 웹 사이트 호스팅은 GPv2 표준 저장소 계정에서만 사용할 수 있습니다.

**Storage VNET 및 방화벽 규칙은 새 웹 엔드포인트에서 지원되나요?**  
예, 새 웹 엔드포인트는 저장소 계정에 대해 구성된 VNET 및 방화벽 규칙을 따릅니다.

## <a name="next-steps"></a>다음 단계
* [Azure CDN을 사용하여 HTTPS를 통한 사용자 지정 도메인으로 Blob 액세스](storage-https-custom-domain-cdn.md)
* [Blob 또는 웹 엔드포인트에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
* [Azure 기능](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [첫 번째 서버 없는 웹앱 빌드](https://aka.ms/static-serverless-webapp)
