---
title: Internet Analyzer 클라이언트 포함 | Microsoft Docs
description: 이 문서에서는 애플리케이션에 Internet Analyzer JavaScript 클라이언트를 포함하는 방법에 대해 알아봅니다.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744054"
---
# <a name="embed-the-internet-analyzer-client"></a>Internet Analyzer 클라이언트 포함

이 문서에서는 애플리케이션에 Internet Analyzer JavaScript 클라이언트를 포함하는 방법을 안내합니다. 테스트를 실행하고 성과 기록표 분석을 받으려면 이 클라이언트를 설치해야 합니다. **첫 번째 테스트가 구성된 후 프로필별 JavaScript 클라이언트가 제공됩니다.** 거기서 새 스크립트를 포함할 필요 없이 해당 프로필에 테스트를 계속 추가하거나 제거할 수 있습니다. Internet Analyzer에 대한 자세한 내용은 [개요](internet-analyzer-overview.md)를 참조하세요. 

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

Internet Analyzer가 제대로 작동하려면 Azure와 기타 Microsoft 서비스에 액세스해야 합니다. 클라이언트를 포함하기 전에 `fpc.msedge.net` 및 사전 구성된 엔드포인트 URL([CLI](internet-analyzer-cli.md)를 통해 볼 수 있음)에 대한 네트워크 액세스를 허용해야 합니다.

## <a name="find-the-client-script-url"></a>클라이언트 스크립트 URL

테스트 구성 후 Azure Portal이나 Azure 명령줄 인터페이스를 통해 스크립트 URL을 찾을 수 있습니다. 자세한 내용은 [Internet Analyzer 리소스 만들기](internet-analyzer-create-test-portal.md)를 참조하세요.

옵션 1. Azure Portal에서 [이 링크](https://aka.ms/InternetAnalyzerPreviewPortal)를 사용하여 Azure Internet Analyzer의 미리 보기 포털 페이지를 엽니다. **설정 > 구성**으로 이동하여 Internet Analyzer 프로필을 탐색하고 스크립트 URL을 확인합니다.

옵션 2. Azure 명령줄 인터페이스를 사용하여 `scriptFileUri` 속성을 확인합니다.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>클라이언트 정보

프로필과 테스트에 대한 스크립트가 생성됩니다. 스크립트는 로드된 후 2초 지연되어 실행됩니다. 먼저 Internet Analyzer 서비스에 연결하여 테스트에 구성된 엔드포인트의 목록을 가져옵니다. 그런 다음, 측정을 실행하고 시간이 기록된 결과를 다시 Internet Analyzer에 업로드합니다.

## <a name="client-examples"></a>클라이언트 예제

이 예제에서는 클라이언트 JavaScript를 웹 페이지나 애플리케이션에 포함하는 몇 가지 기본적인 방법을 보여 줍니다. `0bfcb32638b44927935b9df86dcfe397`이 스크립트 URL의 예제 프로필 ID로 사용됩니다.

### <a name="run-on-page-load"></a>페이지 로드 시 실행
가장 간단한 방법은 메타 태그 블록 내에 스크립트 태그를 사용하는 것입니다. 이 태그는 페이지 로드당 한 번씩 스크립트를 실행합니다.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>다음 단계

[Internet Analyzer FAQ](internet-analyzer-faq.md) 읽기
