---
title: Azure Storage에서 정적 웹 사이트 호스팅
description: 최신 웹 애플리케이션을 호스팅하는 비용 효율적이고 확장 가능한 솔루션을 제공하는 정적 웹 사이트 호스팅입니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427969"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스팅

정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일) 라는 저장소 컨테이너에서 직접 제공할 수 있습니다 *$web*합니다. 포함 하는 서버 리스 아키텍처를 사용할 수 있도록 Azure Storage에서 콘텐츠 호스팅 [Azure Functions](/azure/azure-functions/functions-overview) 및 다른 서비스 (PaaS) 플랫폼입니다.

> [!NOTE]
> 사이트 서버 쪽 코드에 의존 하는 경우 사용 하 여 [Azure App Service](/azure/app-service/overview) 대신 합니다.

## <a name="setting-up-a-static-website"></a>정적 웹 사이트 설정

정적 웹 사이트를 호스팅하는 저장소 계정에서 사용 하도록 설정 해야 하는 기능입니다.

정적 웹 사이트 호스팅에 사용 하려면 기본 파일의 이름을 선택한 다음 필요에 따라 사용자 지정 404 페이지에 대 한 경로 제공 합니다. 하는 경우 명명 된 blob storage 컨테이너 **$web** 존재 하지 않는 계정에서 하나 생성 됩니다. 이 컨테이너에 사이트의 파일을 추가 합니다.

단계별 지침은 [Azure Storage에서 정적 웹 사이트를 호스트](storage-blob-static-website-how-to.md)합니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

파일을 **$web** 컨테이너는 읽기 작업을 통해서만 제공 되며 익명 액세스 요청을 통해 제공 대/소문자를 구분 합니다.

## <a name="uploading-content"></a>콘텐츠 업로드

콘텐츠를 업로드 하려면 이러한 도구 중 하나를 사용할 수 있습니다 합니다 **$web** 컨테이너:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell 모듈](storage-blob-static-website-how-to.md#powershell)
> * [AZCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 확장](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>콘텐츠 보기

사용자는 웹 사이트의 공용 URL을 사용 하 여 브라우저에서 사이트 콘텐츠를 볼 수 있습니다. Azure portal, Azure CLI 또는 PowerShell을 사용 하 여 URL을 찾을 수 있습니다. 이 표를 가이드로 참조하세요.

|도구| 지침 |
|----|----|
|**Azure Portal** | [Azure portal을 사용 하 여 웹 사이트 URL을 찾으려면](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Azure CLI를 사용 하 여 웹 사이트 URL을 찾으려면](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell 모듈** | [PowerShell을 사용 하 여 웹 사이트 URL을 찾으려면](storage-blob-static-website-how-to.md#powershell-find-url) |

지역 코드를 포함 하는 사이트의 URL입니다. 예를 들어 URL `https://contosoblobaccount.z22.web.core.windows.net/` 지역 코드가 `z22`합니다.

해당 코드는 URL, 보존 해야 하는 동안 내부 용도로 이므로 다른 방식으로 해당 코드를 사용할 필요가 없습니다.

정적 웹 사이트 호스팅을 사용 하는 경우 지정 된 인덱스 문서에 사용자가 사이트를 열고 특정 파일을 지정 하지 않는 경우 표시 됩니다 (예: `https://contosoblobaccount.z22.web.core.windows.net`).  

서버는 404 오류를 반환 하 고 웹 사이트를 설정한 경우 오류 문서를 지정 하지 않은, 경우에 기본 404 페이지를 사용자에 게 반환 됩니다.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>설정의 영향을 웹 컨테이너의 공용 액세스 수준

공용 액세스 수준을 수정할 수 있습니다 합니다 **$web** 컨테이너 이지만이 영향을 주지 않습니다 기본 정적 웹 사이트 끝점에는 익명 액세스 요청을 통해 제공 되는 이러한 파일 때문입니다. 즉, 모든 파일에 대 한 공용 (읽기 전용) 액세스 합니다.

다음 스크린샷은 Azure portal에서 공용 액세스 수준 설정을 보여 줍니다.

![포털에서 공용 액세스 수준을 설정 하는 방법을 보여 주는 스크린샷](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

기본 정적 웹 사이트 끝점에 영향을 주지는 동안 기본 blob service 끝점을 공용 액세스 수준 변경에 영향 을지 않습니다.

예를 들어, 공용 액세스 수준을 변경 하면 합니다 **$web** 에서 컨테이너 **개인 (익명 액세스 없음)** 에 **Blob (blob 전용 익명 읽기 액세스)** , 그런 다음 기본 정적 웹 사이트 끝점에 대 한 공용 액세스 수준을 `https://contosoblobaccount.z22.web.core.windows.net/index.html` 변경 되지 않습니다.

그러나 주에 대 한 공용 액세스 blob 서비스 끝점 `https://contosoblobaccount.blob.core.windows.net/$web/index.html` 는 개인에서 public로 변경 합니다. 이제 사용자는 이러한 두 끝점 중 하나를 사용 하 여 해당 파일을 열 수 있습니다.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>콘텐츠 배달 네트워크 (CDN) 및 보안 소켓 레이어 (SSL) 지원

정적 웹 사이트 파일을 사용할 수 있도록 사용자 지정 도메인 및 HTTPS를 통해 참조 [HTTPS를 통한 사용자 지정 도메인을 사용 하 여 blob 액세스를 Azure CDN을 사용 하 여](storage-https-custom-domain-cdn.md)입니다. CDN 주를 가리키도록 해야이 프로세스의 일부로 *정적 웹 사이트* 주 데이터베이스와 달리 끝점 *blob 서비스* 끝점입니다. CDN 구성이 즉시 실행 되지 않습니다으로 내용을 표시 되기 전에 몇 분을 대기 해야 합니다.

정적 웹 사이트를 업데이트 하는 경우에 CDN 끝점을 제거 하 여 CDN에 지 서버에 캐시 된 콘텐츠를 선택 취소 해야 합니다. 자세한 내용은 [Azure CDN 엔드포인트 제거](../../cdn/cdn-purge-endpoint.md)를 참조하세요.

> [!NOTE]
> HTTPS 웹 끝점은 HTTP 및 HTTPS를 통해 액세스할 수 있도록 계정 웹 끝점을 통해 고유 하 게 지원 됩니다. 그러나 저장소 계정이 HTTPS를 통해 보안 전송 필요 하도록 구성 된, 경우 다음 사용자가 사용 해야 합니다 HTTPS 끝점입니다. 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md)합니다.
>
> 이 이번에 Azure CDN를 사용이 해야 하는 HTTPS 통해 사용자 지정 도메인을 사용 합니다.

## <a name="custom-domain-names"></a>사용자 지정 도메인 이름

사용자 지정 도메인을 통해 정적 웹 사이트를 사용할 수 있습니다. 자세한 내용은 참조 하세요 [Azure Storage 계정에 대 한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)합니다.

Azure에서 도메인 호스팅을 자세히 살펴보기를 참조 하세요 [도메인이 Azure DNS에서 호스트](../../dns/dns-delegate-domain-azure-dns.md)합니다.

## <a name="pricing"></a>가격

정적 웹 사이트 호스팅 설정할 수 있습니다. 무료입니다. 사이트를 활용 하는 blob 저장소 및 운영 비용에 대해서만 요금이 청구 됩니다. Azure Blob Storage에 대한 가격의 자세한 내용은 [Azure Blob Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="metrics"></a>metrics

정적 웹 사이트 페이지에 메트릭을 사용할 수 있습니다. 메트릭을 사용 하도록 설정한 후에 파일에 대 한 통계를 트래픽 합니다 **$web** 컨테이너 메트릭 대시보드에 보고 됩니다.

정적 웹 사이트 페이지에 대 한 메트릭을 사용 하려면 [정적 웹 사이트 페이지에 메트릭을 사용 하도록 설정할](storage-blob-static-website-how-to.md#metrics)합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-how-to.md)
* [Azure CDN를 사용 하 여 HTTPS를 통해 사용자 지정 도메인을 사용 하 여 blob 액세스](storage-https-custom-domain-cdn.md)
* [Blob 또는 웹 엔드포인트에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
