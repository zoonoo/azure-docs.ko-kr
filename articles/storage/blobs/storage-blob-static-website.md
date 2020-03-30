---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370494"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅

$web라는 저장소 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript *$web*및 이미지 파일)를 제공할 수 있습니다. Azure Storage에서 콘텐츠를 호스팅하면 [Azure Functions](/azure/azure-functions/functions-overview) 및 기타 플랫폼을 서비스(PaaS) 서비스로 포함하는 서버없는 아키텍처를 사용할 수 있습니다.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> 사이트가 서버 쪽 코드에 종속된 경우 대신 [Azure 앱 서비스를](/azure/app-service/overview) 사용합니다.

## <a name="setting-up-a-static-website"></a>정적 웹 사이트 설정

정적 웹 사이트 호스팅은 저장소 계정에서 활성화해야 하는 기능입니다.

정적 웹 사이트 호스팅을 사용하려면 기본 파일의 이름을 선택한 다음 선택적으로 사용자 지정 404 페이지에 대한 경로를 제공합니다. **$web라는** Blob 저장소 컨테이너가 계정에 아직 없는 경우 해당 저장소저장소가 만들어집니다. 사이트의 파일을 이 컨테이너에 추가합니다.

단계별 지침은 [Azure Storage의 정적 웹 사이트 호스트를](storage-blob-static-website-how-to.md)참조하십시오.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** 컨테이너의 파일은 대/소문자를 구분하며 익명 액세스 요청을 통해 제공되며 읽기 작업을 통해서만 사용할 수 있습니다.

## <a name="uploading-content"></a>콘텐츠 업로드

다음 도구를 사용하여 **콘텐츠를 $web** 컨테이너에 업로드할 수 있습니다.

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 모듈](storage-blob-static-website-how-to.md#powershell)
> * [아즈카피](../common/storage-use-azcopy-v10.md)
> * [Azure 저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/)
> * [비주얼 스튜디오 코드 확장](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>콘텐츠 보기

사용자는 웹 사이트의 공개 URL을 사용하여 브라우저에서 사이트 콘텐츠를 볼 수 있습니다. Azure 포털, Azure CLI 또는 PowerShell을 사용하여 URL을 찾을 수 있습니다. 이 표를 가이드로 참조하세요.

|도구| 지침 |
|----|----|
|**Azure 포털** | [Azure 포털을 사용하여 웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Azure CLI를 사용하여 웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 모듈** | [PowerShell을 사용하여 웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#powershell-find-url) |

사이트의 URL에는 지역 코드가 포함되어 있습니다. 예를 들어 `https://contosoblobaccount.z22.web.core.windows.net/` URL에는 `z22`지역 코드가 포함되어 있습니다.

해당 코드는 URL에 남아 있어야 하지만 내부 용으로만 사용되며 다른 방식으로 해당 코드를 사용할 필요가 없습니다.

정적 웹 사이트 호스팅을 사용하도록 설정할 때 지정하는 인덱스 문서는 사용자가 사이트를 열고 특정 파일을 `https://contosoblobaccount.z22.web.core.windows.net`지정하지 않을 때 나타납니다(예: ).  

서버가 404 오류를 반환하고 웹 사이트를 활성화할 때 오류 문서를 지정하지 않은 경우 기본 404 페이지가 사용자에게 반환됩니다.

> [!NOTE]
> [CORS는](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 정적 웹 사이트에서 지원되지 않습니다.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>웹 컨테이너의 공용 액세스 수준 설정의 영향

**$web** 컨테이너의 공용 액세스 수준을 수정할 수 있지만 이러한 파일은 익명 액세스 요청을 통해 제공되므로 기본 정적 웹 사이트 끝점에는 영향을 미치지 않습니다. 즉, 모든 파일에 대한 공개(읽기 전용) 액세스 권한을 의미합니다.

다음 스크린샷은 Azure 포털의 공용 액세스 수준 설정을 보여 주며 있습니다.

![포털에서 공용 액세스 수준을 설정하는 방법을 보여주는 스크린샷](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

기본 정적 웹 사이트 끝점은 영향을 받지 않지만 공용 액세스 수준을 변경하면 기본 Blob 서비스 끝점에 영향을 줍니다.

예를 들어 **$web** 컨테이너의 공용 액세스 수준을 **Private(익명 액세스 없음)에서** **Blob(Blob에 대한 익명 읽기 액세스)으로**변경하면 기본 정적 `https://contosoblobaccount.z22.web.core.windows.net/index.html` 웹 사이트 끝점에 대한 공용 액세스 수준이 변경되지 않습니다.

그러나 기본 Blob 서비스 끝점에 `https://contosoblobaccount.blob.core.windows.net/$web/index.html` 대한 공용 액세스는 개인에서 공용으로 변경됩니다. 이제 사용자는 이 두 끝점 중 하나를 사용하여 해당 파일을 열 수 있습니다.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>사용자 지정 도메인을 정적 웹 사이트 URL로 매핑

사용자 지정 도메인을 통해 정적 웹 사이트를 사용할 수 있도록 할 수 있습니다. 

Azure Storage는 기본적으로 이를 지원하므로 사용자 지정 도메인에 대한 HTTP 액세스를 활성화하는 것이 더 쉽습니다. HTTPS를 사용하려면 Azure Storage가 아직 사용자 지정 도메인을 사용하여 HTTPS를 기본적으로 지원하지 않으므로 Azure CDN을 사용해야 합니다. 단계별 지침을 [보려면 사용자 지정 도메인을 Azure Blob 저장소 끝점에 매핑을](storage-custom-domain-name.md) 참조하세요.

저장소 계정이 HTTPS를 통해 [보안 전송을 요구하도록](../common/storage-require-secure-transfer.md) 구성된 경우 사용자는 HTTPS 끝점을 사용해야 합니다. 

> [!TIP]
> Azure에서 도메인을 호스팅하는 것이 좋습니다. 자세한 내용은 [Azure DNS에서 도메인 호스트를](../../dns/dns-delegate-domain-azure-dns.md)참조하십시오.

## <a name="adding-http-headers"></a>HTTP 헤더 추가

정적 웹 사이트 기능의 일부로 헤더를 구성할 수 있는 방법은 없습니다. 그러나 Azure CDN을 사용하여 헤더를 추가하고 헤더 값을 추가(또는 덮어쓰기)할 수 있습니다. [Azure CDN에 대한 표준 규칙 엔진 참조를](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)참조하십시오.

헤더를 사용하여 캐싱을 제어하려면 [캐싱 규칙이 있는 Azure CDN 캐싱 동작 제어를](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)참조하십시오.

## <a name="pricing"></a>가격 책정

정적 웹 사이트 호스팅을 무료로 활성화할 수 있습니다. 사이트에서 사용하는 Blob 저장소및 운영 비용에 대해서만 요금이 청구됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="metrics"></a>메트릭

정적 웹 사이트 페이지에서 메트릭을 활성화할 수 있습니다. 메트릭을 사용하도록 설정하면 **$web** 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

정적 웹 사이트 페이지에서 메트릭을 사용하려면 [정적 웹 사이트 페이지에서 메트릭 활성화를](storage-blob-static-website-how-to.md#metrics)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 저장소에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)
* [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
