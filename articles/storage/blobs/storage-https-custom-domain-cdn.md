---
title: Azure CDN을 사용하여 HTTP를 통한 사용자 지정 도메인으로 Blob 액세스
description: Azure CDN을 Blob Storage와 통합하여 HTTPS를 통한 사용자 지정 도메인으로 Blob에 액세스하는 하는 방법 알아보기
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 90ddb58f3499180e17df559a98ac8a46b53fb824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392531"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Azure CDN을 사용하여 HTTP를 통한 사용자 지정 도메인으로 Blob 액세스

이제 Azure CDN(Content Delivery Network)은 사용자 지정 도메인 이름에 대한 HTTPS를 지원합니다. Azure CDN을 사용하면 HTTPS를 통해 사용자 지정 도메인 이름을 사용하여 Blob에 액세스할 수 있습니다. 이렇게 하려면 Blob 또는 웹 엔드포인트에서 Azure CDN을 사용하도록 설정한 다음, Azure CDN을 사용자 지정 도메인 이름에 매핑합니다. 완료되면 Azure에서는 원클릭 액세스 및 완벽한 인증서 관리를 통해 사용자 지정 도메인에 대한 HTTPS 사용을 간소화합니다. 일반 Azure CDN 가격 책정에서 증가는 없습니다.

Azure CDN은 전송 중에 웹 애플리케이션 데이터의 개인정보처리방침 및 데이터 무결성을 보호할 수 있습니다. HTTPS를 통해 트래픽을 제공하기 위해 SSL 프로토콜을 사용하면 Azure CDN은 인터넷 간에 전송되는 데이터를 암호화할 수 있습니다. Azure CDN을 통해 HTTPS를 사용하면 공격에서 웹 애플리케이션을 보호할 수 있습니다.

> [!NOTE]  
> Azure CDN은 사용자 지정 도메인 이름에 대한 SSL 지원을 제공할 뿐 아니라 전 세계에 고대역폭 콘텐츠를 제공하는 애플리케이션의 크기를 조정할 수도 있습니다. 자세한 내용은 [Azure CDN 개요](../../cdn/cdn-overview.md)를 참조하세요.

## <a name="quickstart"></a>빠른 시작

사용자 지정 Blob Storage 엔드포인트에 대한 HTTPS를 사용하도록 설정하려면 다음을 수행합니다.

1.  [Azure Storage 계정과 Azure CDN을 통합합니다](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    이 문서에서는 아직 수행하지 않은 경우 Azure Portal에서 스토리지 계정을 만드는 과정을 안내합니다.

    > [!NOTE]  
    > Azure Storage에서 정적 웹 사이트 지원의 미리 보기 중 스토리지 웹 엔드포인트를 추가하려면 **원본 형식** 드롭다운 목록에서 **사용자 지정 원본**을 선택합니다. Azure Portal에서 스토리지 계정에서 직접 하는 대신 Azure CDN 프로필에서 이 작업을 수행해야 합니다.

2.  [Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑합니다](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Azure CDN 사용자 지정 도메인에서 HTTPS를 사용하도록 설정합니다](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>공유 액세스 서명

기본적으로 Blob Storage 엔드포인트는 익명 읽기 액세스를 허용하지 않습니다. 익명 읽기 액세스를 허용하지 않도록 Blob Storage 엔드포인트를 구성한 경우 각 요청의 [공유 액세스 서명](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 토큰을 사용자 지정 도메인에 제공합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.

Azure CDN은 공유 액세스 서명 토큰에 추가되는 제한 사항을 준수하지 않습니다. 예를 들어 모든 공유 액세스 서명 토큰이 만료됩니다. Azure CDN 에지 노드에서 콘텐츠가 제거될 때까지 만료된 공유 액세스 서명 사용하여 해당 콘텐츠에 계속 액세스할 수 있습니다. 캐시 응답 헤더를 설정하여 데이터를 Azure CDN에 얼마나 오래 캐시할 것인지 제어할 수 있습니다. 그 기간은 [Azure CDN에서 Azure Storage Blob의 만료 관리](../../cdn/cdn-manage-expiration-of-blob-content.md)를 참조하세요.

동일한 Blob 엔드포인트에 대한 두 개 이상의 공유 액세스 서명 URL을 만드는 경우 Azure CDN에 대해 쿼리 문자열 캐싱을 설정하는 것이 좋습니다. 이렇게 하면 Azure에서는 각 URL을 고유 엔터티로 처리해야 합니다. 자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](../../cdn/cdn-query-string.md)를 참조하세요.

## <a name="http-to-https-redirection"></a>HTTP에서 HTTPS로 리디렉션

HTTP 트래픽을 HTTPS로 리디렉션할 수 있습니다. 그러려면 Verizon의 Azure CDN 프리미엄 제품을 사용해야 합니다. 다음 규칙을 적용하면 [Azure CDN 규칙 엔진을 사용하여 HTTP 동작을 재정의합니다](../../cdn/cdn-rules-engine.md).

![HTTP에서 HTTPS로 리디렉션 규칙](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

드롭다운 목록에서 선택하는 *Cdn-endpoint-name*은 사용자의 Azure CDN 엔드포인트에 대해 구성된 이름을 참조합니다. *Origin-path*는 정적 콘텐츠를 저장하는 원본 스토리지 계정 내의 경로를 참조합니다. 단일 컨테이너의 모든 정적 콘텐츠를 호스팅하는 경우 *origin-path*를 해당 컨테이너의 이름으로 바꿉니다.

규칙에 자세히 알아보려면 [Azure CDN 규칙 엔진 기능](../../cdn/cdn-rules-engine-reference-features.md)을 참조하세요.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

Azure CDN을 통해 Blob에 액세스하는 경우 에지 노드와 원본(Blob Storage) 사이의 트래픽에 대해 [Blob Storage 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 지불합니다. 에지 노드에서 액세스하는 데이터에 대해 [Azure CDN 가격](https://azure.microsoft.com/pricing/details/cdn/)을 지불합니다.

예를 들어 Azure CDN을 통해 액세스되는 스토리지 계정이 미국 서부에 있다고 가정해 보겠습니다. 영국에서 Azure CDN을 통해 해당 스토리지 계정의 Blob에 액세스하려고 시도하는 경우 Azure에서는 먼저 영국에 가장 가까이 있는 에지 노드에서 해당 Blob을 확인합니다. Blob을 찾는 경우 Azure CDN이 액세스할 수 있으므로 Azure는 복사본에 액세스하고 Azure CDN 가격 책정을 사용합니다. Blob을 찾지 못하는 경우 Azure는 에지 노드에 Blob을 복사합니다. Blob Storage 가격 책정에서 지정된 대로 이 작업을 통해 송신 및 트랜잭션 비용이 발생됩니다. 그런 다음, Azure는 에지 노드의 파일에 액세스하고 결국 Azure CDN 대금 청구가 발생됩니다.

[Azure CDN 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cdn/)에서 사용자 지정 도메인 이름에 대한 HTTPS 지원은 Verizon 표준 제품 및 프리미엄 제품의 Azure CDN에만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Blob Storage 엔드포인트에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
* [Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)
