---
title: 사용자 지정 엔드포인트 만들기 | Microsoft Docs
description: 이 문서에서는 Internet Analyzer 리소스로 측정할 사용자 지정 엔드포인트를 구성하는 방법에 대해 알아봅니다.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744088"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Internet Analyzer 테스트에서 평가할 사용자 지정 엔드포인트 측정 

이 문서에서는 Internet Analyzer 테스트의 일부로 측정할 사용자 지정 엔드포인트를 설정하는 방법에 대해 설명합니다. 사용자 지정 엔드포인트는 온-프레미스 워크로드, 다른 클라우드 공급자에서 실행 중인 워크로드 및 사용자 지정 Azure 구성을 평가하는 데 도움이 됩니다.  하나의 엔드포인트가 Azure 리소스인 경우 한 테스트에서 2개의 사용자 지정 엔드포인트를 비교할 수 있습니다. Internet Analyzer에 대한 자세한 내용은 [개요](internet-analyzer-overview.md)를 참조하세요. 

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

Internet Analyzer 리소스를 설정하고 "사용자 지정 엔드포인트" 옵션을 선택합니다. Internet Analyzer는 사용자 지정 엔드포인트에서 인터넷에 액세스할 수 있다고 가정합니다. 자세한 내용은 [Internet Analyzer 리소스 만들기](internet-analyzer-create-test-portal.md)를 참조하세요.


## <a name="create-custom-endpoint"></a>사용자 지정 엔드포인트 만들기

1. [여기](https://fpc.msedge.net/apc/trans.gif)에서 투명한 1픽셀 테스트 이미지를 다운로드합니다. 이 1픽셀 이미지는 클라이언트 JavaScript가 성능 측정을 위해 가져오는 자산입니다.
2. 사용자 지정 웹 애플리케이션에서 공개적으로 액세스 가능한 경로에 테스트 이미지를 배포합니다. 경로가 HTTPS에서 작동해야 합니다. 
3. 테스트 작성 중 전체 사용자 지정 엔드포인트 URL(예: `https://contoso.com/test/trans.gif`)을 사용자 지정 엔드포인트 필드에 복사합니다.

## <a name="next-steps"></a>다음 단계

[Internet Analyzer FAQ](internet-analyzer-faq.md) 읽기

