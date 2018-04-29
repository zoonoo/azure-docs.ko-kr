---
title: Azure 스택 포털을 사용 하 여 | Microsoft Docs
description: 사용자 포털을 사용 하 여 Azure 스택에 액세스 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 5aa00123-5b87-45e0-a671-4165e66bfbc6
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: mabrigg
ms.openlocfilehash: 279722cc53889cb0a261fcffde0c7e0f86be6dc5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="using-the-azure-stack-portal"></a>Azure 스택 포털을 사용 하 여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 서비스의 사용자를 Azure 스택 포털을 사용 하 여 공용 제공 서비스를 구독할 수 있으며 이러한 제안을 제공 하는 서비스를 사용 하 여 키를 누릅니다. 하기 전에 Azure 포털을 사용한 경우 익숙한 이미 사용자 인터페이스입니다.

## <a name="access-the-portal"></a>포털에 액세스

Azure 스택 연산자 (서비스 공급자 또는 조직에서 관리자 권한)을 포털에 액세스 하는 올바른 URL를 알 수 있습니다.

- 통합된 된 시스템에 대 한 URL 운영자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식에 포함 될 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
- Azure 스택 개발 키트를 사용 하는 경우 포털 주소는 https://portal.local.azurestack.external합니다.

![Azure 스택 사용자 포털의 스크린샷](media/azure-stack-use-portal/UserPortal.png)

## <a name="customize-the-dashboard"></a>대시보드 사용자 지정

대시보드 타일의 기본 집합을 포함합니다. 클릭할 수 있는 **대시보드 편집** 기본 대시보드를 수정 하거나 클릭 **새 대시보드** 사용자 지정 대시보드를 만드는 합니다. 추가 하거나 타일을 제거 하 여 대시보드를 쉽게 사용자 지정할 수 있습니다. 예를 들어 계산 타일을 추가 하려면 클릭 **새로**합니다. 마우스 오른쪽 단추로 클릭 **계산**, 클릭 하 고 **대시보드에 고정**합니다.

## <a name="create-subscription-and-browse-available-resources"></a>구독 만들기 및 사용 가능한 리소스를 찾아보기
 
구독이 없는 경우 가장 먼저 해야 할 제공 하는 서비스에 가입 됩니다. 그 후 사용 가능한 리소스를 찾아볼 수 있습니다. 를 찾아서 리소스를 만들고 다음 방법 중 하나를 사용 합니다.

- 클릭는 **마켓플레이스** 대시보드에서 타일을 합니다.
- 에 **모든 리소스** 타일을 클릭 하 여 **리소스 만들기**합니다.
- 왼쪽된 탐색 창에서 클릭 **새로**합니다.

## <a name="learn-how-to-use-available-services"></a>사용 가능한 서비스를 사용 하는 방법에 알아봅니다

사용 가능한 서비스를 사용 하는 방법에 대 한 지침을 해야 할 경우 있을 수 있습니다 다양 한 옵션을 사용할 수 있습니다.

- 사용자의 조직 또는 서비스 공급자는 일반적으로 사용자 지정 된 서비스 또는 응용 프로그램을 제공 하는 경우 자신의 설명서를 제공할 수 있습니다.
- 타사 앱은 자신의 설명서입니다.
- Azure에 일관 된 서비스에 대 한 Azure 스택 설명서를 먼저 검토 하는 것이 좋습니다. Azure 스택 사용자 설명서에 액세스 하려면 도움말 아이콘을 클릭 한 다음 클릭 **도움말 + 지원**합니다.
 
    ![UI에 도움말 및 지원 옵션의 스크린 샷](media/azure-stack-use-portal/HelpAndSupport.png)

    특히, 시작 하려면 다음 문서를 검토 하는 것이 좋습니다.

    - [주요 고려 사항: Azure 스택에 대 한 앱을 빌드 또는 서비스를 사용 하 여](azure-stack-considerations.md)
    - 설명서의 "서비스를 사용 하 여" 섹션에는 각 서비스에 대 한 "고려 사항" 문서입니다. "고려 사항" 페이지는 Azure에서 제공 하는 서비스와 Azure 스택에서 제공 하는 동일한 서비스의 차이점을 설명 합니다. 예를 들어 참조 [VM 고려 사항](azure-stack-vm-considerations.md)합니다. Azure 스택에 고유한 "서비스를 사용 하 여" 섹션에 다른 정보가 있을 수 있습니다.
     
      서비스에 대 한 일반 참조로 Azure 설명서를 사용할 수 있지만 이러한 차이에 주의 해야 합니다. 설명서의 링크를 클릭 이해는 **빠른 시작 자습서** 지점 Azure 설명서를 바둑판식으로 배열입니다.

## <a name="get-support"></a>지원 받기

추가 지원이 필요한 경우 조직 또는 서비스 공급자를에 문의 하십시오.

Azure 스택 개발 키트를 사용 하는 경우는 [Azure 스택 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 유일한 소스는 지원 됩니다.

## <a name="next-steps"></a>다음 단계

[주요 고려 사항: Azure 스택에 대 한 앱을 빌드 또는 서비스를 사용 하 여](azure-stack-considerations.md)
