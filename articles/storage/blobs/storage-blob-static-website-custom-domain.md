---
title: '자습서: Azure CDN을 사용하여 정적 웹 사이트의 SSL에서 사용자 지정 도메인 사용 - Azure Storage'
description: 정적 웹 사이트 호스팅에서 사용자 지정 도메인을 구성하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 4b074c9dee93ba44659b0321ae3eee7fbea1c61f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145050"
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

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)을 엽니다. 
1. 스토리지 계정을 찾아 계정 개요를 표시합니다.
1. **Blob 서비스** 메뉴에서 **Azure CDN**을 선택하여 Azure CDN을 구성합니다.
1. **새 엔드포인트** 섹션에서 새 CDN 엔드포인트를 만들려면 필드를 채웁니다.
1. 엔드포인트 이름(예: *mystaticwebsiteCDN*)을 입력합니다.
1. 웹 사이트 도메인을 CDN 엔드포인트에 대한 호스트 이름으로 입력합니다.
1. 원본 호스트 이름에 대한 정적 웹 사이트 엔드포인트를 입력합니다. 정적 웹 사이트 엔드포인트를 찾으려면 스토리지 계정에 대한 **정적 웹 사이트** 섹션으로 이동하고 엔드포인트를 복사합니다. 
1. 브라우저에서 *mywebsitecdn.azureedge.net*으로 이동하여 CDN 엔드포인트를 테스트합니다.

## <a name="enable-custom-domain-and-ssl"></a>사용자 지정 도메인 및 SSL 사용

1. 도메인 이름 공급자를 사용하여 CNAME 레코드를 만들고 사용자 지정 도메인을 CDN 엔드포인트에 리디렉션합니다. *www* 하위 도메인에 대한 CNAME 레코드는 다음과 비슷해야 합니다.

    ![www 하위 도메인에 대한 CNAME 레코드 지정](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Azure Portal에서 새로 만든 엔드포인트를 클릭하여 사용자 지정 도메인 및 SSL 인증서를 구성합니다.
1. **사용자 지정 도메인 추가**를 선택하고 도메인 이름을 입력한 다음, **추가**를 클릭합니다.
1. SSL 인증서를 프로비전하려면 새로 만든 사용자 지정 도메인 매핑을 선택합니다.
1. **사용자 지정 도메인 HTTPS**를 **켜기**로 설정합니다. **CDN 관리됨**을 선택하여 Azure CDN에서 SSL 인증서를 관리하도록 합니다. **저장**을 클릭합니다.
1. 웹 사이트 URL에 액세스하여 웹 사이트를 테스트합니다.

## <a name="next-steps"></a>다음 단계

이 자습서의 2부에서는 Azure CDN에서 SSL을 사용하여 정적 웹 사이트에 대해 사용자 지정 도메인을 구성하는 방법을 알아보았습니다.

이 링크를 따라 Azure Storage에서 정적 웹 사이트를 호스팅하는 방법에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [정적 웹 사이트에 대한 자세한 정보](storage-blob-static-website.md)
