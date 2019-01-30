---
title: Azure Stack 포털을 사용 하 여 | Microsoft Docs
description: 액세스 하 여 Azure Stack에서 사용자 포털을 사용 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 026e37534169fda8dbbf5a568f72046bc6ff55f7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252050"
---
# <a name="use-the-azure-stack-portal"></a>Azure Stack 포털 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack 포털을 사용 하 여 공용 제품을 구독할 수 있으며 이러한 제품을 제공 하는 서비스를 사용할 수 있습니다. 전역 Azure portal을 사용한 경우 익숙한 이미 사이트 작동 방식입니다.

## <a name="access-the-portal"></a>포털에 액세스

귀하가 Azure Stack 운영자 (서비스 공급자 또는 조직에서 관리자 권한)을 포털에 액세스 하는 올바른 URL을 알 수 있습니다.

- 통합된 시스템에 대 한 URL을 연산자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식 이어야 합니다 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
- Azure Stack 개발 키트를 사용 하는 경우 포털 주소가 https://portal.local.azurestack.external합니다.
- 모든 Azure Stack 배포에 대 한 기본 표준 시간대는 utc (협정 세계시)로 설정 됩니다. 선택할 수 있습니다 시간대 Azure Stack을 설치할 때 자동으로 되돌아갑니다 UTC 기본적으로 설치 하는 동안 있지만.

## <a name="customize-the-dashboard"></a>대시보드를 사용자 지정

대시보드 타일의 기본 집합을 포함합니다. 선택할 수 있습니다 **대시보드 편집** 기본 대시보드를 수정 하거나 선택 **새 대시보드** 사용자 지정 대시보드를 만듭니다. 쉽게 추가 하거나 타일을 제거 하 여 대시보드를 사용자 지정할 수 있습니다. 예를 들어 계산 타일을 추가 하려면 선택 **+ 리소스 만들기**합니다. 마우스 오른쪽 단추로 클릭 **계산**를 선택한 후 **대시보드에 고정**합니다.

![Azure Stack 사용자 포털의 화면 캡처](media/azure-stack-use-portal/userportal.png)

## <a name="create-subscription-and-browse-available-resources"></a>사용 가능한 리소스를 찾아 구독 만들기

구독에 아직 없는 경우 가장 먼저 해야 할 경우 제품 구독 그런 다음 사용 가능한 리소스를 찾아볼 수 있습니다. 를 찾아서 리소스를 만들려면 다음 방법 중 하나를 사용 합니다.

- 선택 된 **Marketplace** 대시보드에서 타일을 합니다.
- 에 **모든 리소스** 타일을 선택 **리소스를 만들**합니다.
- 왼쪽된 탐색 창에서 선택 **+ 리소스 만들기**합니다.

## <a name="learn-how-to-use-available-services"></a>사용 가능한 서비스를 사용 하는 방법 알아보기

사용 가능한 서비스를 사용 하는 방법에 대 한 지침을 할 경우 있을 수 있습니다 다른 옵션을 사용할 수 있습니다.

- 조직 또는 서비스 공급자는 일반적으로 사용자 지정된 서비스 또는 앱을 제공 하는 경우 자체 설명서를 제공할 수 있습니다.
- 타사 앱에 자체 설명서를 있습니다.
- Azure와 일관 된 서비스에 대 한 Azure Stack 설명서를 먼저 검토 하는 것이 좋습니다. Azure Stack 사용자 설명서에 액세스 하려면 도움말 아이콘을 선택 하 고 선택한 **도움말 + 지원**합니다.

    ![도움말 및 UI에 옵션을 지원 합니다.](media/azure-stack-use-portal/HelpAndSupport.png)

    특히 시작 하려면 다음 문서를 검토 하는 것이 좋습니다.

    - [주요 고려 사항: 서비스를 사용 하 여 또는 Azure Stack에 대 한 앱 빌드](azure-stack-considerations.md)
    - 에 **서비스를 사용 하 여** 섹션의 설명서는 각 서비스에 대 한 고려 사항 문서를 합니다. 고려 사항 페이지는 Azure에서 제공 하는 서비스 및 Azure Stack에서 제공 하는 동일한 서비스 간의 차이점을 설명 합니다. 예를 들어 참조 [VM 고려 사항](azure-stack-vm-considerations.md)합니다. 기타 정보에 있을 수 있습니다 합니다 **서비스를 사용 하 여** Azure Stack에 고유한 섹션입니다.

      서비스에 대 한 일반 참조로 Azure 설명서를 사용할 수 있지만의 이러한 차이점을 알도록 해야 합니다. 이해 하는 설명서의 링크를 클릭 합니다 **퀵 스타트 자습서** 타일 Azure 설명서를 가리킵니다.

## <a name="get-support"></a>지원 받기

지원이 필요 하면, 조직 또는 서비스 공급자에 게 문의 합니다.

Azure Stack Development Kit를 사용 하는 경우는 [Azure Stack 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 유일한 소스 지원입니다.

## <a name="next-steps"></a>다음 단계

[주요 고려 사항: 서비스를 사용 하 여 또는 Azure Stack에 대 한 앱 빌드](azure-stack-considerations.md)
