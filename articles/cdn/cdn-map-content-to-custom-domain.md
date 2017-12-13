---
title: "CDN 끝점에 사용자 지정 도메인 추가 | Microsoft Docs"
description: "Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑하는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>CDN 끝점에 사용자 지정 도메인 추가
프로필을 만든 후 일반적으로 하나 이상의 CDN [끝점](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)(`azureedge.net`의 하위 도메인)을 추가하여 HTTP 및 HTTPS를 통해 콘텐츠를 배달합니다. 기본적으로 이 끝점은 모든 URL에 포함됩니다(예: `https://contoso.azureedge.net/photo.png`). Azure CDN에서는 편리하게 사용자 지정 도메인(예: `www.contoso.com`)을 끝점과 연결할 수 있습니다. 이 옵션을 통해 끝점 대신 사용자 지정 도메인을 사용하여 콘텐츠를 배달합니다. 이 옵션은 브랜딩 목적으로 자체 도메인 이름을 고객에게 표시하려는 경우 등에 유용합니다.

사용자 지정 도메인이 없으면 먼저 도메인 공급자를 통해 구매해야 합니다. 사용자 지정 도메인을 구한 후에는 다음 단계를 따르세요.
1. [도메인 공급자의 DNS 레코드에 액세스](#step-1-access-dns-records-by-using-your-domain-provider)
2. [CNAME DNS 레코드 만들기](#step-2-create-the-cname-dns-records)
    - 옵션 1: 사용자 지정 도메인과 CDN 끝점 직접 매핑
    - 옵션 2: cdnverify를 사용하여 사용자 지정 도메인과 CDN 끝점 매핑 
3. [Azure에서 CNAME 레코드 매핑 사용](#step-3-enable-the-cname-record-mapping-in-azure)
4. [사용자 지정 하위 도메인이 CDN 끝점을 참조하는지 확인](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(종속 단계) 영구 사용자 지정 도메인을 CDN 끝점에 매핑](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>1단계: 도메인 공급자를 사용하여 DNS 레코드 액세스

Azure를 사용하여 [DNS 도메인](https://docs.microsoft.com/en-us/azure/dns/dns-overview)을 호스트하는 경우 도메인 공급자의 DND를 Azure DNS에 위임해야 합니다. 자세한 내용은 [Azure DNS에 도메인 위임](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)을 참조하세요.

그렇지 않고 도메인 공급자를 사용하여 DNS 도메인을 처리하는 경우 도메인 공급자의 웹 사이트에 로그인합니다. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS**, 또는 **이름 서버 관리**인 웹 사이트 부분을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 종종 계정 정보를 확인한 다음 **내 도메인**과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 일부 공급자에는 다른 레코드 형식을 추가하는 다양한 링크가 있습니다.

> [!NOTE]
> GoDaddy와 같은 특정 공급자의 경우 먼저 별도의 **변경 내용 저장** 링크를 클릭해야 DNS 레코드의 변경 내용이 적용됩니다. 


## <a name="step-2-create-the-cname-dns-records"></a>2단계: CNAME DNS 레코드 만들기 

Azure CDN 끝점에 사용자 지정 도메인을 사용하려면 먼저 해당 도메인 공급자를 통해 CNAME(Canonical Name) 레코드를 만들어야 합니다. CNAME 레코드는 "정식" 또는 실제 도메인 이름에 대한 별칭 도메인 이름을 지정하여 원본 도메인을 대상 도메인에 매핑하는 DNS(Domain Name System)의 레코드 형식입니다. Azure CDN의 경우 원본 도메인은 사용자 지정 도메인(및 하위 도메인), 대상 도메인은 CDN 끝점입니다. Azure CDN은 포털 또는 API로부터 끝점에 사용자 지정 도메인을 추가할 때 CNAME DNS 레코드를 확인합니다. 

CNAME 레코드는 특정 도메인과 하위 도메인(예: `www.contoso.com` 또는 `cdn.contoso.com`)을 매핑합니다. CNAME 레코드를 루트 도메인(예: `contoso.com`)에 매핑할 수는 없습니다. 하위 도메인은 하나의 CDN 끝점에만 연결할 수 있고, 만든 CNAME 레코드는 하위 도메인 주소가 지정된 모든 트래픽을 지정한 끝점으로 라우팅합니다. 예를 들어 `www.contoso.com`을 CDN 끝점에 연결하는 경우 저장소 계정 끝점이나 클라우드 서비스 끝점과 같은 다른 Azure 끝점에는 연결할 수 없습니다. 그러나 동일한 도메인의 다른 하위 도메인은 다른 서비스 끝점에 사용할 수 있습니다. 여러 하위 도메인을 동일한 CDN 끝점에 매핑할 수도 있습니다.

다음 옵션 중 하나를 사용하여 사용자 지정 도메인을 CDN 끝점에 매핑할 수 있습니다.

- 옵션 1: 직접 매핑. 사용자 지정 도메인에서 실행 중인 프러덕션 트래픽이 없는 경우 사용자 지정 도메인을 CDN 끝점에 직접 매핑할 수 있습니다. 사용자 지정 도메인을 CDN 끝점에 매핑하는 프로세스로 인해 Azure Portal에서 도메인을 등록하는 동안 도메인이 잠시 가동 중지될 수 있습니다. CNAME 매핑 항목은 다음과 같은 형식이어야 합니다. 
 
  | 이름             | 형식  | 값                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | contoso\.azureedge.net |


- 옵션 2: **cdnverify** 하위 도메인을 통한 매핑. 사용자 지정 도메인에 차단 불가능한 프러덕션 트래픽이 실행 중인 경우 CDN 끝점에 임시 CNAME 매핑을 만들 수 있습니다. 이 옵션에서는 DNS 매핑이 수행되는 동안 사용자가 중단 없이 도메인에 액세스할 수 있도록 Azure **cdnverify** 하위 도메인을 사용하여 중간 등록 단계를 제공합니다.

   1. 새 CNAME 레코드를 만들고 **cdnverify** 하위 도메인을 포함하는 하위 도메인 별칭을 지정합니다. 예를 들어 **cdnverify.www** 또는 **cdnverify.cdn**입니다. 
   2. `cdnverify.<EndpointName>.azureedge.net` 형식의 CDN 끝점인 호스트 이름을 제공합니다.   CNAME 매핑 항목은 다음과 같은 형식이어야 합니다. 

   | 이름                       | 형식  | 값                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>3단계: Azure에서 CNAME 레코드 매핑 사용

이전 절차 중 하나를 통해 사용자 지정 도메인을 등록한 후에는 Azure CDN에서 사용자 지정 도메인 기능을 활성화할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 사용자 지정 도메인에 매핑하려는 끝점을 가진 CDN 프로필을 클릭합니다.  
2. **CDN 프로필** 블레이드에서 하위 도메인을 연결하려는 CDN 끝점을 선택합니다.
3. 끝점 블레이드의 상단 왼쪽에서 **사용자 지정 도메인**을 클릭합니다. 

   ![사용자 지정 도메인 버튼](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. **사용자 지정 호스트 이름** 텍스트 상자에 하위 도메인을 포함한 사용자 지정 도메인을 입력합니다. 예를 들면 `www.contoso.com` 또는 `cdn.contoso.com`과 같습니다.

   ![사용자 지정 도메인 대화 상자 추가](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. **추가**를 클릭합니다.

   Azure에서 입력한 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다. CNAME 레코드가 이름 서버로 전파되는 데 시간이 걸릴 수 있습니다. 도메인의 유효성이 즉시 검사되지 않고 CNAME 레코드가 올바른 경우 잠시 기다린 후 다시 시도하세요. **Verizon의 Azure CDN**(Standard 및 Premium) 끝점의 경우, 모든 CDN 에지 노드에 사용자 지정 도메인 설정이 전파되려면 최대 90분이 걸릴 수 있습니다.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>4단계: 사용자 지정 하위 도메인이 CDN 끝점을 참조하는지 확인

사용자 지정 도메인의 등록을 완료한 후에는 사용자 지정 하위 도메인이 CDN 끝점을 참조하는지 확인합니다.
 
1. 끝점에 캐시된 공용 콘텐츠가 있는지 확인합니다. 예를 들어 CDN 끝점이 저장소 계정과 연결되어 있는 경우 CDN은 공용 Blob 컨테이너에 콘텐츠를 캐시합니다. 사용자 지정 도메인을 테스트하려면 컨테이너가 공용 액세스를 허용하도록 설정되고 Blob을 하나 이상 포함하는지 확인합니다.

2. 브라우저에서 사용자 지정 도메인을 사용하는 Blob의 주소로 이동합니다. 예를 들어, 사용자 지정 도메인이 `cdn.contoso.com`인 경우 캐시된 Blob의 URL은 URL `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`와 유사하게 됩니다.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>5단계(종속 단계): 영구 사용자 지정 도메인을 CDN 끝점에 매핑

이 단계는 2단계, 옵션 2(**cdnverify** 하위 도메인을 통해 매핑)에 따라 달라집니다. 임시 **cdnverify** 하위 도메인을 사용하고 이 방법이 작동하는 것을 확인했다면 영구 사용자 지정 도메인을 CDN 끝점에 매핑할 수 있습니다.

1. 도메인 공급자의 웹 사이트에서 영구 사용자 지정 도메인을 CDN 끝점에 매핑하기 위한 CNAME DNS 레코드를 만듭니다. CNAME 매핑 항목은 다음과 같은 형식이어야 합니다. 
 
   | 이름             | 형식  | 값                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | contoso\.azureedge.net |
2. 이전에 만든 **cdnverify** 하위 도메인이 있는 CNAME 레코드를 삭제합니다.

## <a name="see-also"></a>참고 항목
[Azure에 대해 CDN(Content Delivery Network)을 사용하도록 설정하는 방법](cdn-create-new-endpoint.md)  
[Azure DNS에 도메인 위임](../dns/dns-domain-delegation.md)
