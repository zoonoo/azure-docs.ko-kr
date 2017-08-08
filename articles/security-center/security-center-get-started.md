---
title: "Azure Security Center 빠른 시작 가이드| Microsoft Docs"
description: "이 문서는 Azure Security Center를 빠르게 시작할 수 있도록 보안 모니터링 및 정책 관리 구성 요소를 안내하고 다음 단계로 연결하는 데 도움이 됩니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 392c814b7d3ff6b4f0f7850a51960576775e0307
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Azure Security Center 빠른 시작 가이드
이 문서에서는 Azure Security Center를 빠르게 시작할 수 있도록 보안 센터의 보안 모니터링 및 정책 관리 구성 요소를 안내합니다.

> [!NOTE]
> 2017년 6월 초를 시작으로 Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집 및 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 문서의 정보는 Microsoft Monitoring Agent로 전환된 후의 Security Center 기능을 나타냅니다.
>
>

## <a name="prerequisites"></a>필수 조건
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

Security Center의 무료 계층은 구독에서 자동으로 사용하도록 설정되고 Azure 리소스의 보안 상태에 대한 가시성을 제공합니다. Azure 파트너의 보안 제품과 서비스를 통해 기본 보안 정책 관리, 보안 권장 사항 및 통합이 제공됩니다.

[Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 Security Center에 액세스합니다. Azure 포털에 대한 자세한 내용은 [포털 문서](https://azure.microsoft.com/documentation/services/azure-portal/)를 참조하세요.

## <a name="permissions"></a>권한
Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 Azure 리소스와 관련된 항목만 볼 수 있습니다. Security Center의 역할 및 허용된 작업에 대한 자세한 내용은 [Azure Security Center의 권한](security-center-permissions.md)을 참조하세요.

## <a name="data-collection"></a>데이터 수집
Security Center는 해당 보안 상태를 평가하고 보안 권장 사항을 제공하며 위협에 경고하기 위해 VM(가상 컴퓨터)에서 데이터를 수집합니다. Security Center에 처음 액세스하는 경우 구독의 모든 VM에서 데이터 수집을 활성화합니다. Security Center는 지원되는 모든 기존 Azure VM 및 새로 만든 Azure VM에 Microsoft Monitoring Agent를 프로비전합니다. 데이터 수집 작동 방식에 대한 자세한 내용은 [데이터 수집 활성화](security-center-enable-data-collection.md)를 참조하세요.

데이터 수집을 사용하는 것이 좋습니다. Security Center의 무료 계층을 사용하는 경우 보안 정책에서 데이터 수집을 해제하여 VM에서 데이터 수집을 사용하지 않도록 설정할 수 있습니다. 데이터 수집은 Security Center의 표준 계층에 대한 구독에 필요합니다. 무료 및 표준 가격 책정 계층에 대한 자세한 내용은 [Security Center 가격 책정](security-center-pricing.md)을 참조하세요.

다음 단계에서는 Security Center의 구성 요소를 액세스하여 사용하는 방법을 설명합니다. 여기서는 데이터 수집을 해제하도록 선택하는 경우 이 기능을 해제하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
>
>

## <a name="access-security-center"></a>Security Center 액세스
포털에서 다음 단계에 따라 Security Center에 액세스합니다.

1. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다.

   ![Azure 메뉴][1]
2. 처음으로 Security Center에 액세스하는 경우 **시작** 블레이드가 열립니다. **Security Center 시작**을 선택하여 **Security Center** 블레이드를 열고 데이터 수집을 사용하도록 설정합니다.
   ![시작 화면][10]
3. 시작 블레이드에서 Security Center를 시작하거나 Microsoft Azure 메뉴에서 Security Center를 선택하면 **Security Center** 블레이드가 열립니다. 향후 **Security Center** 블레이드에 손쉽게 액세스하려면 오른쪽 위에 있는 **대시보드에 블레이드 고정** 옵션을 선택합니다.
   ![대시보드 옵션에 블레이드 고정][2]

## <a name="use-security-center"></a>Security Center 사용
Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성할 수 있습니다. 다음과 같이 구독에 대한 보안 정책을 구성하세요.

1. **Security Center** 블레이드에서 **정책** 타일을 선택합니다.
2. **보안 정책 – 구독별로 정책 정의** 블레이드에서 구독을 선택합니다.
3. **보안 정책** 블레이드에서 자동으로 로그를 수집하도록 **데이터 수집**을 활성화합니다. 구독에 있는 현재 VM과 새로운 VM에 모니터링 확장이 프로비전됩니다. (Security Center의 무료 계층에서 **데이터 수집**을 **끄기**로 설정하면 데이터 수집을 옵트아웃(opt out)할 수 있습니다. **데이터 수집**을 **끄기**로 설정하면 Security Center에서 보안 경고 및 권장 사항을 제공하지 않습니다.)
4. **보안 정책** 블레이드에서 **방지 정책**을 선택합니다. 그러면 **방지 정책** 블레이드가 열립니다.
5. **방지 정책** 블레이드에서 보안 정책의 일부로 보려는 권장 사항을 설정합니다. 예제:

   * **시스템 업데이트**를 **켜기**로 설정하면 지원되는 모든 VM에 대해 누락된 OS 업데이트가 있는지 검사합니다.
   * **OS 취약성**을 **켜기**로 설정하면 지원되는 모든 VM을 검사하여 VM을 공격에 더 취약하게 만들 수 있는 OS 구성을 식별합니다.

### <a name="view-recommendations"></a>권장 사항 보기
1. **Security Center** 블레이드로 돌아가서 **권장 사항** 타일을 선택합니다. Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. Security Center에서 잠재적인 보안 취약성을 식별하는 경우 **권장 사항** 블레이드에서 권장 사항을 보여 줍니다.
   ![Azure Security Center의 권장 사항][5]
2. **권장 사항을** 블레이드에서 권장 사항을 선택하여 자세한 정보를 표시하거나 문제를 해결하기 위한 조치를 선택합니다.

### <a name="view-the-security-state-of-your-resources"></a>리소스의 보안 상태 보기
1. **Security Center** 블레이드로 돌아갑니다. 대시보드의 **방지** 섹션에는 VM, 네트워킹, 데이터 및 응용 프로그램의 보안 상태에 대한 지표가 포함되어 있습니다.
2. **Compute**를 선택하여 자세한 정보를 봅니다. 세 개의 탭을 보여 주는 **Compute** 블레이드가 열립니다.

  - **개요** - 모니터링 및 VM 권장 사항이 포함되어 있습니다.
  - **가상 컴퓨터** - 모든 VM과 현재 보안 상태를 나열합니다.
  - **클라우드 서비스**: Security Center에서 모니터링하는 웹 및 작업자 역할을 나열합니다.

    ![Azure 보안 센터의 리소스 상태 타일][6]

3. **개요** 탭의 **가상 컴퓨터 권장 사항**에서 권장 사항을 선택하여 자세한 정보를 보거나 필요한 제어를 구성하는 작업을 선택합니다.
4. **가상 컴퓨터** 탭에서 VM을 선택하여 추가 세부 정보를 봅니다.

### <a name="view-security-alerts"></a>보안 경고 보기
1. **Security Center** 블레이드로 돌아가서 **보안 경고** 타일을 선택합니다. **보안 경고** 블레이드가 열려 경고 목록을 보여 줍니다. 보안 로그 및 네트워크 활동의 보안 상태 분석에서 이러한 경고를 생성합니다. 통합된 파트너 솔루션에서 보내는 경고도 있습니다.
   ![Azure Security Center의 보안 경고][7]

   > [!NOTE]
   > Security Center의 표준 계층을 사용하도록 설정된 경우에만 보안 경고를 제공합니다. 표준 계층을 60일 무료 평가판으로 사용할 수 있습니다. 표준 계층을 가져오는 방법에 대한 내용은 [다음 단계](#next-steps)를 참조하세요.
   >
   >
2. 경고를 선택하여 추가적인 정보를 표시합니다. 이 예에서는 **수정된 시스템 이진 파일 검색**을 선택합니다. 이렇게 하면 해당 경고에 대한 세부 정보를 추가로 제공하는 블레이드가 열립니다.
   ![Azure Security Center의 보안 경고 세부 정보][8]

### <a name="view-the-health-of-your-partner-solutions"></a>파트너 솔루션의 상태 보기
1. **Security Center** 블레이드로 돌아갑니다. **파트너 솔루션** 타일에서 Azure 구독과 통합된 파트너 솔루션의 상태를 한 눈에 모니터링할 수 있습니다.
2. **파트너 솔루션** 타일을 선택합니다. 블레이드가 열리고 Security Center에 연결된 파트너 솔루션의 목록을 보여 줍니다.
   ![파트너 솔루션][9]
3. 파트너 솔루션을 선택합니다. 이 예에서는 **QualysVa1** 솔루션을 선택합니다.  블레이드가 열리고 파트너 솔루션의 상태 및 솔루션의 관련 리소스를 보여 줍니다. **솔루션 콘솔** 을 선택하여 이 솔루션에 대한 파트너 관리 환경을 엽니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 보안 모니터링 및 정책 관리 구성 요소를 소개했습니다. 이제 Security Center에 익숙해졌으므로 다음 단계를 수행하세요.

* Azure 구독의 보안 정책을 구성합니다. 자세한 내용은 [Azure Security Center의 보안 정책 설정](security-center-policies.md)을 참조하세요.
* Security Center에서 권장 사항을 사용하여 Azure 리소스를 보호합니다. 자세한 내용은 [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.
* 현재 보안 경고를 검토하고 관리합니다. 자세한 내용은 [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.
- [Azure Security Center 데이터 보안](security-center-data-security.md) - Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
* Security Center의 [표준 계층](security-center-pricing.md)과 함께 제공되는 [고급 위협 탐지 기능](security-center-detection-capabilities.md)에 대해 알아봅니다. 표준 계층은 처음 60일간 무료로 제공됩니다.
* Security Center 사용에 대한 질문이 있으면 [Azure Security Center FAQ](security-center-faq.md)를 참조하세요.

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

