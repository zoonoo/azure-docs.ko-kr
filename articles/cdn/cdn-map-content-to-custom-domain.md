---
title: "Azure CDN 콘텐츠를 사용자 지정 도메인 매핑 | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>Azure CDN 콘텐츠를 사용자 지정 도메인에 매핑
azureedge.net의 하위 도메인을 사용하는 대신 캐시된 콘텐츠에 대한 URL에 고유한 도메인 이름을 사용하려면 사용자 지정 도메인을 CDN 끝점에 매핑할 수 있습니다.

사용자 지정 도메인을 CDN 끝점에 매핑하는 두 가지 방법이 있습니다.

1. [도메인 등록 기관을 사용하여 CNAME 레코드를 만들고 사용자 지정 도메인과 하위 도메인을 CDN 끝점에 매핑](#register-a-custom-domain-for-an-azure-cdn-endpoint)
   
    CNAME 레코드는 `www.contosocdn.com` 또는 `cdn.contoso.com` 같은 원본 도메인을 대상 도메인에 매핑하는 DNS 기능입니다. 이 경우 원본 도메인은 사용자 지정 도메인과 하위 도메인입니다(**www** 또는 **cdn** 같은 하위 도메인은 항상 필수임). 대상 도메인은 CDN 끝점입니다.  
   
    그러나 사용자 지정 도메인을 CDN 끝점에 매핑하는 프로세스로 인해 Azure Portal에서 도메인을 등록하는 동안 도메인이 잠시 가동 중지될 수 있습니다.
2. [**cdnverify**를 사용하여 중간 등록 단계 추가](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    사용자 지정 도메인에서 가동 중지 시간이 없어야 하는 SLA(서비스 수준 계약)가 설정된 응용 프로그램을 현재 지원하고 있는 경우 DNS 매핑이 진행되는 동안 사용자가 도메인에 액세스할 수 있도록 중간 등록 단계를 제공하는 데 Azure **cdnverify** 하위 도메인을 사용할 수 있습니다.  

위의 절차 중 하나를 사용하여 사용자 지정 도메인을 등록한 후 [사용자 지정 하위 도메인이 CDN 끝점을 참조하는지 확인](#verify-that-the-custom-subdomain-references-your-cdn-endpoint)할 수 있습니다.

> [!NOTE]
> 사용 중인 도메인을 CDN 끝점에 매핑하려면 도메인 등록 기관을 사용하여 CNAME 레코드를 만들어야 합니다. CNAME 레코드는 `www.contoso.com` 또는 `cdn.contoso.com` 같은 특정 하위 도메인을 매핑합니다. `contoso.com`같은 루트 도메인에는 CNAME 레코드를 매핑할 수 없습니다.
> 
> 하위 도메인은 하나의 CDN 끝점에만 연결될 수 있습니다. 만든 CNAME 레코드는 하위 도메인 주소가 지정된 모든 트래픽을 지정한 끝점으로 라우팅합니다.  예를 들어 `www.contoso.com` 을 CDN 끝점에 연결하는 경우 저장소 계정 끝점이나 클라우드 서비스 끝점과 같은 다른 Azure 끝점에는 연결할 수 없습니다. 그러나 동일한 도메인의 다른 하위 도메인은 다른 서비스 끝점에 사용할 수 있습니다. 여러 하위 도메인을 동일한 CDN 끝점에 매핑할 수도 있습니다.
> 
> **Verizon의 Azure CDN**(Standard 및 Premium) 끝점의 경우, CDN 에지 노드에 사용자 지정 도메인 변경 내용이 전파되려면 최대 **90분**이 걸립니다.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Azure CDN 끝점에 대한 사용자 지정 도메인 등록
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **찾아보기**, **CDN 프로필**을 차례로 클릭한 다음 사용자 지정 도메인에 매핑하려는 끝점을 가진 CDN 프로필을 클릭합니다.  
3. **CDN 프로필** 블레이드에서 하위 도메인을 연결하려는 CDN 끝점을 클릭합니다.
4. 끝점 블레이드의 맨 위에서 **사용자 지정 도메인 추가** 단추를 클릭합니다.  **사용자 지정 도메인 추가** 블레이드에서 새 CNAME 레코드를 만드는 데 사용할 끝점 호스트 이름이 CDN 끝점에서 파생되어 표시됩니다. 호스트 이름 주소의 형식은 **&lt;EndpointName>.azureedge.net**으로 표시됩니다.  이 호스트 이름을 복사하여 CNAME 레코드를 만드는 데 사용할 수 있습니다.  
5. 도메인 등록 기관의 웹 사이트로 이동한 다음 DNS 레코드를 만들기 위한 섹션을 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.
6. CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 CNAME, 별칭 또는 하위 도메인과 같은 단어를 찾아야 할 수도 있습니다.
7. 선택한 하위 도메인(예: **www** 또는 **cdn**)을 **사용자 지정 도메인 추가** 블레이드에 제공된 호스트 이름에 매핑하는 새 CNAME 레코드를 만듭니다. 
8. **사용자 지정 도메인 추가** 블레이드로 돌아가 하위 도메인을 포함하는 사용자 지정 도메인을 대화 상자에 입력합니다. 예를 들어 `www.contoso.com` 또는 `cdn.contoso.com` 형식으로 도메인 이름을 입력합니다.   
   
   Azure에서 입력한 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다.  **Verizon의 Azure CDN** (Standard 및 Premium) 끝점의 경우, 모든 CDN 에지 노드에 사용자 지정 도메인 설정이 전파되려면 최대 90분이 걸립니다.  
   
   CNAME 레코드가 인터넷의 이름 서버로 전파되는 데 시간이 걸리는 경우도 있습니다. 도메인의 유효성이 즉시 검사되지 않고 CNAME 레코드가 올바른 경우 잠시 기다린 후 다시 시도하세요.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>중간 cdnverify 하위 도메인을 사용하여 Azure CDN 끝점에 대한 사용자 지정 도메인 등록
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **찾아보기**, **CDN 프로필**을 차례로 클릭한 다음 사용자 지정 도메인에 매핑하려는 끝점을 가진 CDN 프로필을 클릭합니다.  
3. **CDN 프로필** 블레이드에서 하위 도메인을 연결하려는 CDN 끝점을 클릭합니다.
4. 끝점 블레이드의 맨 위에서 **사용자 지정 도메인 추가** 단추를 클릭합니다.  **사용자 지정 도메인 추가** 블레이드에서 새 CNAME 레코드를 만드는 데 사용할 끝점 호스트 이름이 CDN 끝점에서 파생되어 표시됩니다. 호스트 이름 주소의 형식은 **&lt;EndpointName>.azureedge.net**으로 표시됩니다.  이 호스트 이름을 복사하여 CNAME 레코드를 만드는 데 사용할 수 있습니다.
5. 도메인 등록 기관의 웹 사이트로 이동한 다음 DNS 레코드를 만들기 위한 섹션을 찾습니다. **도메인 이름**, **DNS** 또는 **이름 서버 관리**와 같은 섹션에서 이를 찾을 수 있습니다.
6. CNAME을 관리하기 위한 섹션을 찾습니다. 고급 설정 페이지로 이동하여 **CNAME**, **별칭** 또는 **하위 도메인**과 같은 단어를 찾아야 할 수도 있습니다.
7. 새 CNAME 레코드를 만들고 **cdnverify** 하위 도메인을 포함하는 하위 도메인 별칭을 지정합니다. 예를 들어 지정하는 하위 도메인은 **cdnverify.www** 또는 **cdnverify.cdn** 형식이 됩니다. 그런 다음 **cdnverify.&lt;EndpointName>.azureedge.net** 형식으로 CDN 끝점인 호스트 이름을 제공합니다. DNS 매핑은 다음과 같습니다.`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. **사용자 지정 도메인 추가** 블레이드로 돌아가 하위 도메인을 포함하는 사용자 지정 도메인을 대화 상자에 입력합니다. 예를 들어 `www.contoso.com` 또는 `cdn.contoso.com` 형식으로 도메인 이름을 입력합니다. 이 단계에서는 하위 도메인 앞에 **cdnverify**를 추가할 필요가 없습니다.  
   
    Azure에서 입력한 cdnverify 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다.
9. 이제 사용자 지정 도메인이 Azure에서 확인되었지만 도메인에 대한 트래픽은 아직 CDN 끝점으로 라우팅되지 않습니다. 사용자 지정 도메인 설정이 CDN 에지 노드에 전파되기에 충분한 시간을 기다린 후(**Verizon의 Azure CDN**의 경우 90분, **Akamai의 Azure CDN**의 경우 1-2분), DNS 등록 기관의 웹 사이트로 돌아가서 하위 도메인을 CDN 끝점에 매핑하는 다른 CNAME 레코드를 만듭니다. 예를 들어 하위 도메인을 **www** 또는 **cdn**으로 지정하고 호스트 이름을 **&lt;EndpointName>.azureedge.net**으로 지정합니다. 이 단계에서 사용자 지정 도메인 등록이 완료됩니다.
10. 끝으로, **cdnverify**를 사용하여 만든 CNAME 레코드는 중간 단계로만 필요하므로 삭제할 수 있습니다.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>사용자 지정 하위 도메인이 CDN 끝점을 참조하는지 확인
* 사용자 지정 도메인 등록을 완료한 후 사용자 지정 도메인을 사용하여 CDN 끝점에 캐시된 콘텐츠에 액세스할 수 있습니다.
  먼저 끝점에 캐시된 공용 콘텐츠가 있는지 확인합니다. 예를 들어 CDN 끝점이 저장소 계정과 연결되어 있는 경우 CDN은 공용 Blob 컨테이너에 콘텐츠를 캐시합니다. 사용자 지정 도메인을 테스트하려면 컨테이너가 공용 액세스를 허용하도록 설정되고 Blob을 하나 이상 포함하는지 확인합니다.
* 브라우저에서 사용자 지정 도메인을 사용하는 Blob의 주소로 이동합니다. 예를 들어 사용자 지정 도메인이 `cdn.contoso.com`인 경우 캐시된 Blob에 대한 URL은 다음 URL과 유사합니다. http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>참고 항목
[Azure에 대해 CDN(콘텐츠 배달 네트워크)을 사용하도록 설정하는 방법](cdn-create-new-endpoint.md)  

