---
title: "Azure CDN에서 파일을 압축하여 성능 향상 | Microsoft Docs"
description: "Azure CDN에서 파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시키는 방법을 알아봅니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 7546650e6096a880f4fb4d0c94dd4ecc00b70160
ms.contentlocale: ko-kr
ms.lasthandoff: 08/28/2017

---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Azure CDN에서 파일을 압축하여 성능 향상
압축은 파일이 서버에서 전송되기 전에 파일 크기를 줄여서 파일 전송 속도를 개선하고 페이지 로드 성능을 높이는 간단하고 효과적인 방법입니다. 대역폭 비용을 절감하고 사용자에게 반응이 빠른 환경을 제공합니다.

압축을 사용하도록 설정하는 방법은 두 가지입니다.

* 원본 서버에서 압축을 사용하도록 설정할 수 있으며, 이런 경우 CDN은 압축된 파일을 거쳐서 압축된 파일을 요청한 클라이언트에 압축된 파일을 전달합니다.
* CDN 에지 서버에서 직접 압축을 사용하도록 설정할 수 있으며, 이런 경우 CDN이 파일을 압축하여(원본 서버에 의해 압축되지 않더라도) 최종 사용자에게 제공합니다.

> [!IMPORTANT]
> CDN 구성 변경이 네트워크 전체에 전파되려면 다소 시간이 걸립니다.  <b>Akamai의 Azure CDN</b> 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다.  <b>Verizon의 Azure CDN</b> 프로필의 경우, 변경 내용이 일반적으로 90분 내에 적용됩니다.  CDN 끝점에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다렸다가 문제 해결을 시도하는 것이 좋습니다.
> 
> 

## <a name="enabling-compression"></a>압축을 사용하도록 설정
> [!NOTE]
> 표준 및 프리미엄 CDN 계층은 동일한 압축 기능을 제공하지만 사용자 인터페이스는 다릅니다.  표준과 프리미엄 CDN 계층 간의 차이점에 대한 자세한 내용은 [Azure CDN 개요](cdn-overview.md)를 참조하세요.
> 
> 

### <a name="standard-tier"></a>표준 계층
> [!NOTE]
> 이 섹션은 **Verizon의 Azure CDN Standard** 및 **Akamai의 Azure CDN Standard** 프로필에 적용됩니다.
> 
> 

1. CDN 페이지에서 관리하려는 CDN 끝점을 클릭합니다.
   
    ![CDN 프로필 페이지 끝점](./media/cdn-file-compression/cdn-endpoints.png)
   
    CDN 끝점 페이지가 열립니다.
2. **구성** 단추를 클릭합니다.
   
    ![CDN 프로필 페이지 관리 단추](./media/cdn-file-compression/cdn-config-btn.png)
   
    CDN 구성 페이지가 열립니다.
3. **압축**을 켭니다.
   
    ![CDN 압축 옵션](./media/cdn-file-compression/cdn-compress-standard.png)
4. 기본 형식을 사용하거나 파일 형식을 추가 또는 제거하여 목록을 수정합니다.
   
   > [!TIP]
   > 가능하기는 하지만 ZIP, MP3, MP4, JPG, 등 압축된 형식에 압축을 적용하는 것은 좋지 않습니다.
   > 
   > 
5. 변경한 후 **저장** 단추를 클릭합니다.

### <a name="premium-tier"></a>프리미엄 계층
> [!NOTE]
> 이 섹션은 **Verizon의 Azure CDN Premium** 프로필에 적용됩니다.
> 
> 

1. CDN 프로필 페이지에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 페이지 관리 단추](./media/cdn-file-compression/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
2. **HTTP Large** 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다.  **압축**을 클릭합니다.

    ![파일 압축 선택](./media/cdn-file-compression/cdn-compress-select.png)
   
    압축 옵션이 표시됩니다.
   
    ![파일 압축](./media/cdn-file-compression/cdn-compress-files.png)
3. **압축 사용** 라디오 단추를 클릭하여 압축을 활성화합니다.  쉼표로 구분된 목록(공백 없음)으로 압축하려는 MIME 유형을 **파일 유형** 텍스트 상자에 입력합니다.
   
   > [!TIP]
   > 가능하기는 하지만 ZIP, MP3, MP4, JPG, 등 압축된 형식에 압축을 적용하는 것은 좋지 않습니다. 
   > 
   > 
4. 변경한 후 **업데이트** 단추를 클릭합니다.

## <a name="compression-rules"></a>압축 규칙
이 테이블은 모든 시나리오에 적용되는 Azure CDN 압축 동작을 설명합니다.

> [!IMPORTANT]
> **Verizon의 Azure CDN** (Standard 및 Premium)의 경우, 적합한 파일만 압축될 수 있습니다.  압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
> 
> * 128바이트 초과.
> * 1MB 미만.
> 
> **Akamai의 Azure CDN**의 경우, 모든 파일이 압축에 적합합니다.
> 
> 모든 Azure CDN 제품의 경우, 파일은 [압축용으로 구성된](#enabling-compression)MIME 형식이어야 합니다.
> 
> **Verizon의 Azure CDN** 프로필(Standard 및 Premium)은 **gzip**(GNU zip), **deflate** 또는 **bzip2** 또는 **br**(Brotli) 인코딩을 지원합니다. Brotli 인코딩의 경우 에지에서만 압축이 수행됩니다. 클라이언트/브라우저는 Brotli 인코딩에 대한 요청을 전송해야 하고 압축된 자산은 처음에 원본 쪽에서 먼저 압축되었을 것입니다. 
>
>**Akamai의 Azure CDN** 프로필은 **gzip** 인코딩만 지원합니다.
> 
> **Akamai의 Azure CDN** 끝점은 클라이언트 요청에 상관없이, 원본으로부터 항상 **gzip** 인코딩 파일을 요청합니다. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>압축이 비활성화되었거나 파일이 압축에 부적합
| 클라이언트 요청 형식(Accept-Encoding 헤더를 통한) | 캐시된 파일 형식 | 클라이언트에 대한 CDN 응답 | 참고 |
| --- | --- | --- | --- |
| 압축 |압축 |압축 | |
| 압축 |미압축 |미압축 | |
| 압축 |캐시되지 않음 |압축 또는 미압축 |원본 응답에 따라 결정 |
| 미압축 |압축 |미압축 | |
| 미압축 |미압축 |미압축 | |
| 미압축 |캐시되지 않음 |미압축 | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>압축이 활성화되고 파일이 압축에 적합
| 클라이언트 요청 형식(Accept-Encoding 헤더를 통한) | 캐시된 파일 형식 | 클라이언트에 대한 CDN 응답 | 참고 |
| --- | --- | --- | --- |
| 압축 |압축 |압축 |지원되는 형식 간 CDN 코드 변환 |
| 압축 |미압축 |압축 |CDN이 압축 수행 |
| 압축 |캐시되지 않음 |압축 |원본이 미압축 상태로 반환되면 CDN가 압축을 수행합니다.  **Verizon에서 Azure CDN** 은 첫 번째 요청에 압축되지 않은 파일을 전달한 다음 후속 요청에 대한 파일을 압축하고 캐시합니다.  `Cache-Control: no-cache` 헤더를 포함한 파일을 압축되지 않습니다. |
| 미압축 |압축 |미압축 |CDN이 압축 해제 수행 |
| 미압축 |미압축 |미압축 | |
| 미압축 |캐시되지 않음 |미압축 | |

## <a name="media-services-cdn-compression"></a>미디어 서비스 CDN 압축
미디어 서비스 CDN 사용 스트리밍 끝점의 경우 다음 콘텐츠 형식에 대해 기본적으로 압축이 사용됩니다. application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Azure 포털에서 언급된 형식에 대해 압축을 사용하거나 사용하지 않도록 설정할 수 없습니다.  

## <a name="see-also"></a>참고 항목
* [CDN 파일 압축 문제 해결](cdn-troubleshoot-compression.md)    


