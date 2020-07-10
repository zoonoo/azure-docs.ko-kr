---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.openlocfilehash: ccad51d18a5e76f68633103af64e9ba6cc3f19c0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203390"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅

*$web*이라는 스토리지 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 사용할 수 있습니다. Azure Storage에서 콘텐츠를 호스팅하면 [Azure Functions](/azure/azure-functions/functions-overview) 및 기타 PaaS(Platform as a service) 서비스를 포함하는 서버리스 아키텍처를 사용할 수 있습니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> 사이트가 서버 쪽 코드에 종속되는 경우 대신 [Azure App Service](/azure/app-service/overview)를 사용합니다.
범용 v2 Standard storage 계정을 만들어야 합니다. 정적 웹 사이트는 다른 유형의 스토리지 계정에서 사용할 수 없습니다.

## <a name="setting-up-a-static-website"></a>정적 웹 사이트 설정

정적 웹 사이트 호스팅은 스토리지 계정에서 사용하도록 설정해야 하는 기능입니다.

정적 웹 사이트 호스팅을 사용하도록 설정하려면 기본 파일의 이름을 선택한 다음 필요에 따라 사용자 지정 404 페이지의 경로를 제공합니다. 계정에 **$web**이라는 Blob 스토리지 컨테이너가 아직 없는 경우 하나 만듭니다. 이 컨테이너에 사이트의 파일을 추가합니다.

단계별 지침은 [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)를 참조하세요.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** 컨테이너의 파일은 대/소문자를 구분하고 익명 액세스 요청을 통해 제공되며 읽기 작업을 통해서만 사용할 수 있습니다.

## <a name="uploading-content"></a>콘텐츠 업로드

다음 도구 중 하나를 사용하여 **$web** 컨테이너에 콘텐츠를 업로드할 수 있습니다.

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell 모듈](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 확장](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>콘텐츠 보기

사용자는 웹 사이트의 공용 URL을 사용하여 브라우저에서 사이트 콘텐츠를 볼 수 있습니다. Azure Portal, Azure CLI 또는 PowerShell을 사용하여 URL을 찾을 수 있습니다. [웹 사이트 URL 찾기](storage-blob-static-website-how-to.md#portal-find-url)를 참조하세요.

서버에서 404 오류를 반환하는 경우, 웹 사이트를 사용하도록 설정할 때 오류 문서를 지정하지 않았으면, 사용자에게 기본 404 페이지가 반환됩니다.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)는 정적 웹 사이트에서 지원되지 않습니다.

### <a name="regional-codes"></a>지역 코드

사이트의 URL은 지역 코드를 포함합니다. 예를 들어 URL `https://contosoblobaccount.z22.web.core.windows.net/`에는 지역 코드 `z22`가 포함되어 있습니다.

이 코드는 URL에 남아 있어야 하지만 내부용으로만 사용되며 다른 방법으로는 해당 코드를 사용할 필요가 없습니다.

정적 웹 사이트 호스팅을 사용하도록 설정할 때 지정한 인덱스 문서는 사용자가 사이트를 열고 특정 파일을 지정하지 않을 때 표시됩니다(예: `https://contosoblobaccount.z22.web.core.windows.net`).

### <a name="secondary-endpoints"></a>보조 엔드포인트

[보조 지역의 중복성](../common/storage-redundancy.md#redundancy-in-a-secondary-region)을 설정한 경우 보조 엔드포인트를 사용하여 웹 사이트 콘텐츠에 액세스할 수도 있습니다. 데이터는 보조 지역에 비동기적으로 복제되므로 보조 엔드포인트에서 사용할 수 있는 파일이 기본 엔드포인트에서 사용할 수 있는 파일과 항상 동기화되지는 않습니다.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>웹 컨테이너의 공용 액세스 수준을 설정하는 경우의 영향

**$web** 컨테이너의 공용 액세스 수준을 수정할 수 있습니다. 하지만 이러한 파일은 익명 액세스 요청을 통해 제공되므로 기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않습니다. 이는 모든 파일에 대한 공용(읽기 전용) 액세스를 의미합니다.

다음 스크린샷은 Azure Portal의 공용 액세스 수준 설정을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정하는 방법을 보여 주는 스크린샷](./media/anonymous-read-access-configure/configure-public-access-container.png)

기본 정적 웹 사이트 엔드포인트에는 영향을 주지 않지만 공용 액세스 수준 변경은 주 Blob 서비스 엔드포인트에는 영향을 줍니다.

예를 들어 **$web** 컨테이너의 공용 액세스 수준을 **개인(익명 액세스 없음)** 에서 **Blob(Blob만 익명 읽기 액세스)** 으로 변경하는 경우 기본 정적 웹 사이트 엔드포인트 `https://contosoblobaccount.z22.web.core.windows.net/index.html`에 대한 공용 액세스 수준은 변경되지 않습니다.

그러나 기본 Blob 서비스 엔드포인트 `https://contosoblobaccount.blob.core.windows.net/$web/index.html`에 대한 공용 액세스가 개인에서 공용으로 변경됩니다. 이제 사용자는 이들 두 엔드포인트 중 하나를 사용하여 해당 파일을 열 수 있습니다.

저장소 계정에 대 한 공용 액세스를 사용 하지 않도록 설정 해도 해당 저장소 계정에서 호스팅되는 정적 웹 사이트에는 영향을 주지 않습니다. 자세한 내용은 [컨테이너 및 blob에 대 한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)을 참조 하세요.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>정적 웹 사이트 URL에 사용자 지정 도메인 매핑

사용자 지정 도메인을 통해 정적 웹 사이트를 사용할 수 있도록 설정할 수 있습니다.

Azure Storage에서 기본적으로 지원하므로 사용자 지정 도메인에 대해 HTTP 액세스를 사용하는 것이 더 쉽습니다. HTTPS를 사용하려면 Azure CDN을 사용해야 합니다. Azure Storage는 기본적으로 사용자 지정 도메인에 대해 HTTPS를 지원하지 않습니다. 단계별 지침은 [Azure Blob Storage 엔드포인트 사용자 지정 도메인 매핑](storage-custom-domain-name.md)을 참조하세요.

스토리지 계정이 HTTPS를 통한 [보안 전송을 요구](../common/storage-require-secure-transfer.md)하도록 구성된 경우 사용자는 HTTPS 엔드포인트를 사용해야 합니다.

> [!TIP]
> Azure에서 도메인을 호스트하는 것이 좋습니다. 자세한 내용은 [Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)를 참조하세요.

## <a name="adding-http-headers"></a>HTTP 헤더 추가

정적 웹 사이트 기능의 일부로 헤더를 구성할 수 있는 방법은 없습니다. 그러나 Azure CDN을 사용하여 헤더를 추가하고 헤더 값을 추가(또는 덮어쓰기)할 수 있습니다. [Azure CDN에 대한 표준 규칙 엔진 참조](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)를 참조하세요.

캐싱을 제어하는 데 헤더를 사용하려는 경우 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)를 참조하세요.

## <a name="pricing"></a>가격 책정

정적 웹 사이트 호스팅은 무료로 사용할 수 있습니다. 사이트에서 활용하는 Blob 스토리지 및 운영 비용에 대해서만 요금이 청구됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="metrics"></a>메트릭

정적 웹 사이트 페이지에서 메트릭을 사용할 수 있습니다. 메트릭을 사용하도록 설정하면 **$web** 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

정적 웹 사이트 페이지에서 메트릭을 사용하도록 설정하려면 [정적 웹 사이트 페이지에서 메트릭 사용](storage-blob-static-website-how-to.md#metrics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)
* [Azure Blob Storage 엔드포인트에 사용자 지정 도메인 매핑](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
