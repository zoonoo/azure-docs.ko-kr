---
title: '자습서: Azure CDN을 사용하여 정적 웹 사이트의 SSL에서 사용자 지정 도메인 사용 - Azure Storage'
description: 정적 웹 사이트 호스팅에서 사용자 지정 도메인을 구성하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226153"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>자습서: Azure CDN을 사용하여 정적 웹 사이트에서 SSL과 함께 사용자 지정 도메인을 사용하도록 설정

이 자습서는 시리즈의 2부입니다. 여기에서는 정적 웹 사이트에서 SSL과 함께 사용자 지정 도메인 엔드포인트를 사용하는 방법을 알아봅니다. 

자습서에서는 [Azure CDN](../../cdn/cdn-overview.md)을 사용하여 정적 웹 사이트에서 사용자 지정 도메인 엔드포인트를 구성하는 방법을 보여줍니다. Azure CDN을 사용하면 사용자 지정 SSL 인증서를 프로비전하고, 사용자 지정 도메인을 사용하고, 동시에 모든 사용자 지정 다시 쓰기 규칙을 구성할 수 있습니다. Azure CDN을 구성하면 추가 비용이 발생하지만 전세계 어디에서나 웹 사이트에 대해 일관되게 낮은 대기 시간을 제공합니다. 또한 Azure CDN은 고유한 인증서를 사용하여 SSL 암호화를 제공합니다. Azure CDN 가격 책정에 대한 정보는 [Azure CDN 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 정적 웹 사이트 엔드포인트에서 CDN 엔드포인트 만들기
> * 사용자 지정 도메인 및 SSL 사용

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 1부, [자습서: Blob Storage에서 정적 웹 사이트 호스트](storage-blob-static-website-host.md)를 완료하세요. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>정적 웹 사이트 엔드포인트에서 CDN 엔드포인트 만들기

1. Azure Portal에서 스토리지 계정을 찾아 계정 개요를 표시합니다.
1. **Blob 서비스** 메뉴에서 **Azure CDN**을 선택하여 Azure CDN을 구성합니다.
1. **CDN 프로필** 섹션에서 새 CDN 프로필 또는 기존 CDN 프로필을 지정합니다. 자세한 내용은 [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](../../cdn/cdn-create-new-endpoint.md)를 참조하세요.
1. CDN 엔드포인트에 대한 가격 책정 계층을 지정합니다. 이 자습서에서는 **표준 Akamai** 가격 책정 계층을 사용합니다. 이는 일반적으로 몇 분 내에 빠르게 전파되기 때문입니다. 다른 가격 책정 계층은 전파하는 데 시간이 더 오래 걸릴 수 있지만 다른 이점을 누릴 수도 있습니다. 자세한 내용은 [Azure CDN 제품 기능 비교](../../cdn/cdn-features.md)를 참조하세요.
1. **CDN 엔드포인트 이름** 필드에서 CDN 엔드포인트에 대한 이름을 지정합니다. CDN 엔드포인트는 Azure에서 고유해야 합니다.
1. **원본 호스트 이름** 필드에서 정적 웹 사이트 엔드포인트를 지정합니다. 정적 웹 사이트 엔드포인트를 찾으려면 스토리지 계정에 대한 **정적 웹 사이트** 설정으로 이동합니다. 기본 엔드포인트를 복사하여 CDN 구성에 붙여넣고, 프로토콜 식별자를 제거합니다(*예*: HTTPS).

    다음 이미지에서는 엔드포인트 구성의 예를 보여 줍니다.

    ![CDN 엔드포인트 구성 샘플을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. CDN 엔드포인트를 만들고, 전파될 때까지 기다립니다.
1. CDN 엔드포인트가 올바르게 구성되었는지 확인하려면 엔드포인트를 클릭하여 해당 설정으로 이동합니다. 스토리지 계정에 대한 CDN 개요에서 다음 이미지와 같이 엔드포인트 호스트 이름을 찾아 해당 엔드포인트로 이동합니다. CDN 엔드포인트의 형식은 `https://staticwebsitesamples.azureedge.net`과 비슷합니다.

    ![CDN 엔드포인트의 개요를 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    CDN 엔드포인트 전파가 완료되어 CDN 엔드포인트로 이동하면 이전에 정적 웹 사이트에 업로드한 index.html 파일의 내용이 표시됩니다.

1. CDN 엔드포인트에 대한 원본 설정을 검토하려면 CDN 엔드포인트에 대한 **설정** 섹션 아래에서 **원본**으로 이동합니다. **원본 형식** 필드가 *사용자 지정 원본*으로 설정되어 있고 **원본 호스트 이름** 필드에 정적 웹 사이트 엔드포인트가 표시됩니다.

    ![CDN 엔드포인트에 대한 원본 설정을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>사용자 지정 도메인 및 SSL 사용

1. 도메인 이름 공급자를 사용하여 CNAME 레코드를 만들고 사용자 지정 도메인을 CDN 엔드포인트에 리디렉션합니다. *www* 하위 도메인에 대한 CNAME 레코드는 다음과 비슷해야 합니다.

    ![www 하위 도메인에 대한 CNAME 레코드 지정](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Azure Portal에서 CDN 엔드포인트에 대한 설정을 표시합니다. **설정** 아래의 **사용자 지정 도메인**으로 이동하여 사용자 지정 도메인 및 SSL 인증서를 구성합니다.
1. **사용자 지정 도메인 추가**를 선택하고 도메인 이름을 입력한 다음, **추가**를 클릭합니다.
1. 새 사용자 지정 도메인 매핑을 선택하여 SSL 인증서를 프로비저닝합니다.
1. **사용자 지정 도메인 HTTPS**를 **켜기**로 설정한 다음, **저장**을 클릭합니다. 사용자 지정 도메인을 구성하는 데 몇 시간이 걸릴 수 있습니다. 포털에서 다음 이미지와 같이 진행 상황을 표시합니다.

    ![사용자 지정 도메인 구성의 진행 상황을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. 사용자 지정 도메인에 대한 URL에 액세스하여 정적 웹 사이트와 사용자 지정 도메인의 매핑을 테스트합니다.

사용자 지정 도메인에서 HTTPS를 사용하도록 설정하는 방법에 대한 자세한 내용은 [자습서: Azure CDN 사용자 지정 도메인에서 HTTPS 구성](../../cdn/cdn-custom-ssl.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서의 2부에서는 Azure CDN에서 SSL을 사용하여 정적 웹 사이트에 대해 사용자 지정 도메인을 구성하는 방법을 알아보았습니다.

Azure CDN을 구성하고 사용하는 방법에 대한 자세한 내용은 [Azure CDN이란?](../../cdn/cdn-overview.md)을 참조하세요.