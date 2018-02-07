---
title: "SAS로 Azure CDN 사용 | Microsoft Docs"
description: 
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2017
ms.author: rli v-deasim
ms.openlocfilehash: de30f4319be75362131f8c8ad71aad57b0528f05
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2018
---
# <a name="using-azure-cdn-with-sas"></a>SAS로 Azure CDN 사용

저장소 계정의 저장소 컨테이너에서 콘텐츠를 제공할 때 저장소 컨테이너에 개인 액세스를 부여하여 사용자의 파일 액세스 방식을 보안할 수 있습니다. 개인 액세스를 부여하지 않으면 공용 액세스가 부여된 저장소 컨테이너를 URL을 알고 있으면 누구나 액세스할 수 있습니다. CDN(콘텐츠 배달 네트워크)에서 액세스할 수 있도록 허용한 저장소 계정을 보호하려면 Azure Storage의 SAS(공유 액세스 서명) 기능을 사용하여 개인 저장소 컨테이너에 대한 제한된 액세스 권한을 부여할 수 있습니다.

SAS는 계정 키를 노출하지 않고 Azure Storage 리소스에 대한 제한된 액세스 권한을 부여하는 URI입니다. 저장소 계정 키로 신뢰하지 않지만 특정 저장소 계정 리소스에 대한 액세스를 위임하려는 클라이언트에 SAS를 제공할 수 있습니다. 이러한 클라이언트에 공유 액세스 서명 URI를 배포하여 지정된 기간 동안 리소스에 액세스하도록 할 수 있습니다.
 
SAS를 사용하면 Blob의 액세스 권한에 대해 시작 및 만료 시간, 사용 권한(읽기/쓰기), IP 범위 등과 같은 다양한 매개 변수를 정의할 수 있습니다. 이 문서에서는 Azure CDN과 함께 SAS를 사용하는 방법에 대해 설명합니다. SAS를 만드는 방법과 해당 매개 변수 옵션 등 SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)을 참조하세요.

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>저장소 SAS를 사용하도록 Azure CDN 설정
Azure CDN에서 SAS를 사용하는 경우 다음 세 가지 옵션을 사용하는 것이 좋습니다. 모든 옵션에서는 작동하는 SAS를 이미 만들었다고 가정합니다(필수 조건 참조). 
 
### <a name="prerequisites"></a>필수 조건
시작하려면 저장소 계정을 만든 다음, 자산에 대한 SAS를 생성합니다. 서비스 SAS, 계정 SAS 등 두 가지 유형의 저장된 액세스 서명을 생성할 수 있습니다. 자세한 내용은 [공유 액세스 서명 유형](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures)을 참조하세요.

SAS를 생성했으면 다음 형식의 URL을 사용하여 Blob Storage 파일에 액세스할 수 있습니다. `https://<account>.blob.core.windows.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
예: 
 ```
https://democdnstorage1.blob.core.windows.net/container1/sasblob.txt?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

매개 변수 설정에 대한 자세한 내용은 [SAS 매개 변수 고려 사항](#sas-parameter-considerations) 및 [공유 액세스 서명 매개 변수](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters)를 참조하세요.

![CDN SAS 설정](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-the-cdn"></a>옵션 1: CDN에서 Blob Storage로 통과된 SAS 사용

이 옵션은 CDN에서 원본 서버로 전달된 단일 SAS 토큰만 사용하는 가장 단순한 방법입니다. 이 옵션은 Standard 및 Premium 프로필 모두에 대해 **Verizon의 Azure CDN**에서 지원되며 **Akamai의 Azure CDN**에서도 지원됩니다. 
 
1. 끝점을 선택하고 **캐싱 규칙**을 클릭한 다음, **쿼리 문자열 캐싱** 목록에서 **각 고유한 URL 캐시**를 선택합니다.

    ![CDN 캐싱 규칙](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. 저장소 계정에서 SAS를 설정한 후 CDN URL과 함께 SAS 토큰을 사용하여 파일에 액세스합니다. 
   
   결과 URL의 형식은 다음과 같습니다. `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`

   예:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. 캐싱 규칙을 사용하거나 원본에서 `Cache-Control` 헤더를 추가하여 캐시 지속 기간을 미세 조정합니다. CDN은 SAS 토큰을 일반 쿼리 문자열로 취급하므로 SAS 만료 시간 또는 이전에 만료되는 캐싱 기간을 설정하는 것이 좋습니다. 그러지 않으면 SAS가 활성 상태인 기간보다 오랫동안 파일이 캐시되는 경우 SAS 만료 시간이 지난 후에도 CDN 원본 서버에서 파일에 액세스할 수 있습니다. 이 경우 캐시된 파일에 액세스할 수 없도록 하려면 파일에서 제거 작업을 수행하여 캐시에서 파일을 지워야 합니다. CDN에서 캐시 기간을 설정하는 방법은 [캐싱 규칙으로 Azure Content Delivery Network 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

### <a name="option-2-hidden-cdn-security-token-using-rewrite-rule"></a>옵션 2: 다시 쓰기 규칙을 사용하여 숨긴 CDN 보안 토큰
 
이 옵션을 사용하면 CDN 사용자에 대한 SAS 토큰 없이도 원본 Blob Storage를 보호할 수 있습니다. 파일에 대한 특정 액세스 제한이 필요하지 않지만 사용자가 저장소 원본에 직접 액세스하못지 하게 하여 CDN 오프로드 시간을 개선하려는 경우 이 옵션을 사용할 수 있습니다. 이 옵션은 **Verizon의 Azure CDN Premium** 프로필에서만 사용할 수 있습니다. 
 
1. [규칙 엔진](cdn-rules-engine.md)을 사용하여 URL 다시 쓰기 규칙을 만듭니다. 새 규칙이 전파되는 데 90분 정도 걸립니다.

   ![CDN 관리 단추](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN 규칙 엔진 단추](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   이 간단한 URL 다시 쓰기 규칙의 패턴은 다음과 같습니다.
   
   원본:   
   `/test/demo.jpg`
   
   대상:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 다시 쓰기 규칙](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
 
2. 다음과 같은 형식으로 SAS 토큰 없이 CDN의 파일에 액세스합니다. `https://<endpoint>.azureedge.net/<folder>/<file>`
 
   예:    
   `https://demoendpoint.azureedge.net/test/demo.jpg`
       
   SAS 토큰 사용 여부와 관계없이 누구나 CDN 끝점에 액세스할 수 있습니다. 

3. 캐싱 규칙을 사용하거나 원본에서 `Cache-Control` 헤더를 추가하여 캐시 지속 기간을 미세 조정합니다. CDN은 SAS 토큰을 일반 쿼리 문자열로 취급하므로 SAS 만료 시간 또는 이전에 만료되는 캐싱 기간을 설정하는 것이 좋습니다. 그러지 않으면 SAS가 활성 상태인 기간보다 오랫동안 파일이 캐시되는 경우 SAS 만료 시간이 지난 후에도 CDN 원본 서버에서 파일에 액세스할 수 있습니다. 이 경우 캐시된 파일에 액세스할 수 없도록 하려면 파일에서 제거 작업을 수행하여 캐시에서 파일을 지워야 합니다. CDN에서 캐시 기간을 설정하는 방법은 [캐싱 규칙으로 Azure Content Delivery Network 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>옵션 3: 다시 쓰기 규칙과 함께 CDN 보안 토큰 인증 사용

이 옵션은 가장 안전하며 사용자 지정이 가능합니다. CDN 보안 토큰 인증을 사용하려면 **Verizon의 Azure CDN Premium** 프로필이 있어야 합니다. 클라이언트 액세스는 CDN 보안 토큰에 설정된 보안 매개 변수를 기반으로 합니다. 그러나 SAS가 잘못되는 경우 CDN은 원본 서버에서 콘텐츠의 유효성을 다시 검사할 수 없습니다.

1. [CDN 보안 토큰을 만들고](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) CDN 끝점에 대한 규칙 엔진과 사용자가 파일에 액세스할 수 있는 경로를 사용하여 이 토큰을 활성화합니다.

   SAS URL의 형식은 다음과 같습니다.   
   `https://<endpoint>.azureedge.net/<folder>/<file>?sv=<SAS_TOKEN>`
 
   예:    
   ```
   https://demoendpoint.azureedge.net/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   CDN 보안 토큰 인증에 대한 매개 변수 옵션은 SAS 토큰에 대한 매개 변수 옵션과 다릅니다. CDN 보안 토큰을 만들 때 만료 시간을 사용하는 경우 SAS 토큰의 만료 시간과 같은 값으로 설정합니다. 이렇게 하면 만료 시간을 예측할 수 있게 됩니다. 
 
2. [규칙 엔진](cdn-rules-engine.md)으로 URL 다시 쓰기 규칙을 만들어 컨테이너의 모든 Blob에 대한 토큰 액세스를 사용하도록 설정합니다. 새 규칙이 전파되는 데 90분 정도 걸립니다.

   이 간단한 URL 다시 쓰기 규칙의 패턴은 다음과 같습니다.
   
   원본:   
   `/test/demo.jpg`
   
   대상:   
   `/test/demo.jpg?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D`

   ![CDN URL 다시 쓰기 규칙](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)

3. SAS를 갱신하는 경우 새 SAS 토큰을 사용하도록 URL 다시 쓰기 규칙을 업데이트합니다. 

## <a name="sas-parameter-considerations"></a>SAS 매개 변수 고려 사항

SAS 매개 변수는 CDN에 표시되지 않으므로 CDN은 이 매개 변수를 기반으로 배달 동작을 변경할 수 없습니다. 정의된 매개 변수 제한은 클라이언트가 CDN에 하는 요청이 아니라 CDN이 원본 서버에 하는 요청에만 적용됩니다. SAS 매개 변수를 설정할 때 이 구분을 고려해야 합니다. 이러한 고급 기능이 필요하고 [옵션 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)을 사용하는 경우 CDN 보안 토큰에 대해 적절한 제한 사항을 설정합니다.

| SAS 매개 변수 이름 | 설명 |
| --- | --- |
| 시작 | CDN이 Blob 파일에 액세스하기 시작할 수 있는 시간입니다. 클록 스큐(clock skew)(신호가 구성 요소에 따라 다른 시간에 도착하는 경우)로 인해 자산을 즉시 사용할 수 있게 하려면 15분 이른 시간을 선택합니다. |
| 끝 | CDN이 Blob 파일에 더 이상 액세스할 수 없는 시간입니다. CDN에서 이전에 캐시된 파일은 계속 액세스할 수 있습니다. 파일 만료 시간을 제어하려면 CDN 보안 토큰에 적절한 만료 시간을 설정하거나 자산을 제거합니다. |
| 허용된 IP 주소 | 선택 사항입니다. **Verizon의 Azure CDN**을 사용하는 경우 이 매개 변수를 you can set this parameter to the ranges defined in [Azure CDN from Verizon Edge Server IP Ranges](https://msdn.microsoft.com/library/mt757330.aspx)(Verizon의 Azure CDN 에지 서버 IP 범위)에 정의된 범위로 설정할 수 있습니다. **Akamai의 Azure CDN**을 사용하는 경우에는 IP 주소가 고정 주소가 아니므로 IP 범위 매개 변수를 설정할 수 없습니다.|
| 허용되는 프로토콜 | 계정 SAS를 사용하여 요청하는 경우 허용되는 프로토콜입니다. HTTPS 설정을 사용하는 것이 좋습니다.|

## <a name="see-also"></a>참고 항목
- [SAS(공유 액세스 서명) 사용](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [공유 액세스 서명, 2부: Blob 저장소를 사용하여 SAS 만들기 및 사용](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [토큰 인증을 사용하여 Azure Content Delivery Network 자산 보안 유지](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
