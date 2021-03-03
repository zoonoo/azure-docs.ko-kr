---
title: Azure 전면 도어 표준/프리미엄 (미리 보기)에서 파일을 압축 하 여 성능 향상
description: Azure Front 도어에서 파일을 압축 하 여 파일 전송 속도를 개선 하 고 페이지 로드 성능을 높이는 방법에 대해 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099677"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)에서 파일을 압축 하 여 성능 향상

> [!Note]
> 이 설명서는 Azure Front 도어 Standard/Premium (미리 보기)에 대 한 것입니다. Azure Front 문에 대 한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

파일 압축은 파일 전송 속도를 개선 하 고 페이지 로드 성능을 높이는 효과적인 방법입니다. 압축은 서버에서 전송 되기 전에 파일 크기를 줄입니다. 파일 압축은 대역폭 비용을 줄이고 사용자에 게 더 나은 환경을 제공할 수 있습니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
파일 압축을 사용하도록 설정하는 두 가지 방법이 있습니다.

- 원본 서버에서 압축을 사용 하도록 설정 합니다. Azure 전면 도어는 압축 된 파일을 따라 전달 하 고이를 요청 하는 클라이언트에 전달 합니다.
- Azure Front 도어 POP 서버에서 직접 압축을 사용 하도록 설정 (*즉시 압축*) 이 경우 Azure Front 문이 파일을 압축 하 여 최종 사용자에 게 보냅니다.

> [!IMPORTANT]
> Azure 전면 도어 구성 변경은 네트워크 전체에 전파 되는 데 최대 10 분이 걸립니다. CDN 끝점에 대해 처음으로 압축을 설정 하는 경우에는 문제를 해결 하기 전에 1-2 시간 동안 기다린 후 압축 설정이 모든 Pop에 전파 되었는지 확인 하십시오.

## <a name="enabling-compression"></a>압축을 사용하도록 설정

> [!Note]
> Azure 전면 도어에서 압축은 경로에서 **캐싱 사용** 의 일부입니다. **캐싱을 사용 하도록 설정** 하는 경우에만 Azure Front 도어가 압축을 활용할 수 있습니다.

다음과 같은 방법으로 압축을 사용 하도록 설정할 수 있습니다.
* 빠른 생성 중-캐싱을 사용 하도록 설정 하는 경우 압축을 사용 하도록 설정할 수 있습니다.
* 사용자 지정 만들기-경로를 추가할 때 캐싱 및 압축을 사용 하도록 설정 합니다. 
* 끝점 관리자 경로에 있습니다.
* 최적화 페이지에서

### <a name="enable-compression-in-endpoint-manager"></a>끝점 관리자에서 압축 사용

1. Azure 전면 도어 표준/프리미엄 프로필 페이지에서 **끝점 관리자** 로 이동 하 고 압축을 사용 하도록 설정할 끝점을 선택 합니다.

1. **끝점 편집** 을 선택한 다음 압축을 사용 하도록 설정할 **경로** 를 선택 합니다. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="끝점 관리자 방문 페이지의 스크린샷" lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. **캐싱 사용** 이 선택 되어 있는지 확인 한 다음 **압축 사용** 확인란을 선택 합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="끝점 관리자에서 압축을 사용 하도록 설정 합니다.":::   

1. **업데이트** 를 선택 하 여 구성을 저장 합니다.

### <a name="enable-compression-in-optimization"></a>최적화에서 압축 사용

1. Azure 전면 도어 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동 합니다. 끝점을 확장 하 여 경로 목록을 표시 합니다. 

1. 압축을 *사용 하지 않도록 설정* 된 **경로** 옆의 점 세 개를 선택 합니다. 그런 다음 **경로 구성** 을 선택 합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="최적화 페이지에서 압축 사용 화면입니다." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. **캐싱 사용** 이 선택 되어 있는지 확인 한 다음 **압축 사용** 확인란을 선택 합니다.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="끝점 관리자에서 압축을 사용 하도록 설정 하는 스크린샷"::: 

1. **업데이트** 를 클릭합니다.

## <a name="modify-compression-content-type"></a>압축 내용 유형 수정

최적화 페이지에서 MIME 형식의 기본 목록을 수정할 수 있습니다.

1. Azure 전면 도어 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동 합니다. 그런 다음 압축을 *사용 하도록 설정* 된 **경로** 를 선택 합니다.

1. 압축을 *사용 하도록 설정* 된 **경로** 옆에 있는 세 개의 점을 선택 합니다. 그런 다음 **압축 파일 형식 보기** 를 선택 합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="최적화 페이지의 스크린샷" lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. 기본 형식을 삭제 하거나 **추가** 를 선택 하 여 새 콘텐츠 형식을 추가 합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="파일 압축 사용자 지정 페이지의 스크린샷"::: 

1. **저장** 을 선택 하 여 압축 구성을 업데이트 합니다.

## <a name="disabling-compression"></a>압축 해제

다음과 같은 방법으로 압축을 사용 하지 않도록 설정할 수 있습니다.
* 끝점 관리자 경로에서 압축을 사용 하지 않도록 설정 합니다.
* 최적화 페이지에서 압축을 사용 하지 않습니다.

### <a name="disable-compression-in-endpoint-manager"></a>끝점 관리자에서 압축 사용 안 함

1. Azure 전면 도어 표준/프리미엄 프로필 페이지에서 설정 아래의 **끝점 관리자** 로 이동 합니다. 압축을 사용 하지 않도록 설정할 끝점을 선택 합니다.

1. **끝점 편집** 을 선택한 다음 압축을 사용 하지 않도록 설정할 **경로** 를 선택 합니다. **압축 사용** 확인란의 선택을 취소 합니다.

1. **업데이트** 를 선택 하 여 구성을 저장 합니다.

### <a name="disable-compression-in-optimizations"></a>최적화에서 압축 사용 안 함

1. Azure 전면 도어 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동 합니다. 그런 다음 압축을 *사용 하도록 설정* 된 **경로** 를 선택 합니다.

1. 압축을 *사용 하도록 설정한* **경로** 옆에 있는 세 개의 점을 선택 하 고 *경로 구성* 을 선택 합니다.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="최적화 페이지에서 압축 사용 안 함의 스크린샷"::: 

1. **압축 사용** 확인란의 선택을 취소 합니다.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="압축을 사용 하지 않도록 설정 하는 업데이트 경로 페이지의 스크린샷"::: 

1. **업데이트** 를 선택 하 여 구성을 저장 합니다.

## <a name="compression-rules"></a>압축 규칙

Azure Front 도어에서는 적격 파일만 압축 됩니다. 압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
* MIME 형식 이어야 합니다. 
* 1kb 보다 커야 합니다.
* 8mb 보다 작아야 합니다.

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
* gzip(GNU zip)
* brotli 

요청에서 두 가지 이상의 압축 형식을 지원하는 경우 brotli 압축이 우선적으로 사용됩니다.

자산에 대 한 요청에서 gzip 압축을 지정 하 고 요청이 캐시 누락을 발생 하는 경우 Azure Front 도어가 POP 서버에서 직접 자산 압축을 수행 합니다. 이후 압축된 파일은 캐시에서 제공됩니다.

원본에서 CTE (청크 분할 전송 인코딩)를 사용 하 여 압축 된 데이터를 Azure 전면 도어 POP로 보내는 경우 8mb 보다 큰 응답 크기는 지원 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- 첫 번째 [규칙 집합](how-to-configure-rule-set.md) 을 구성 하는 방법 알아보기
- [규칙 집합 일치 조건](concept-rule-set-match-conditions.md) 에 대 한 자세한 정보
- [Azure Front 도어 규칙 집합](concept-rule-set.md) 에 대해 자세히 알아보기
