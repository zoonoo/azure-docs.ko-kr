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
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: cbc91181f2531ea5e60bb426d6b8b42e2c7684d3
ms.openlocfilehash: 0bb6db67df25fb3c3484c819398890efef343943


---
# <a name="azure-security-center-quick-start-guide"></a>Azure Security Center 빠른 시작 가이드
이 문서에서는 Azure Security Center를 빠르게 시작할 수 있도록 보안 센터의 보안 모니터링 및 정책 관리 구성 요소를 안내합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
>
>

## <a name="prerequisites"></a>필수 조건
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

Security Center의 무료 계층은 구독에서 자동으로 사용하도록 설정되고 Azure 리소스의 보안 상태에 대한 가시성을 제공합니다. Azure 파트너의 보안 제품과 서비스를 통해 기본 보안 정책 관리, 보안 권장 사항 및 통합이 제공됩니다.

[Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 Security Center에 액세스합니다. Azure 포털에 대한 자세한 내용은 [포털 문서](https://azure.microsoft.com/documentation/services/azure-portal/)를 참조하세요.

## <a name="permissions"></a>권한
Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 Azure 리소스와 관련된 항목만 볼 수 있습니다. Security Center의 역할 및 허용된 작업에 대한 자세한 내용은 [Azure Security Center의 권한](security-center-permissions.md)을 참조하세요.

## <a name="data-collection"></a>데이터 수집
Security Center는 해당 보안 상태를 평가하고 보안 권장 사항을 제공하며 위협에 경고하기 위해 VM(가상 컴퓨터)에서 데이터를 수집합니다. Security Center에 처음 액세스하는 경우 구독의 모든 VM에서 데이터 수집을 활성화합니다. 데이터 수집을 사용하는 것이 좋지만 Security Center 정책에서 데이터 수집 해제를 설정할 수 있습니다.

다음 단계에서는 Security Center의 구성 요소를 액세스하여 사용하는 방법을 설명합니다. 여기서는 데이터 수집을 해제하도록 선택하는 경우 이 기능을 해제하는 방법을 보여 줍니다.

## <a name="access-security-center"></a>Security Center 엑세스
포털에서 다음 단계에 따라 Security Center에 액세스합니다.

1. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다.

   ![Azure 메뉴][1]
2. 처음으로 Security Center에 액세스하는 경우 **시작** 블레이드가 열립니다. **Security Center** 블레이드를 열고  데이터 수집을 사용하도록 설정하려면 **예! Azure Security Center를 시작하겠습니다.**를 선택합니다.
   ![시작 화면][10]
3. 시작 블레이드에서 Security Center를 시작하거나 Microsoft Azure 메뉴에서 Security Center를 선택하면 **Security Center** 블레이드가 열립니다. 향후 **Security Center** 블레이드에 손쉽게 액세스하려면 오른쪽 위에 있는 **대시보드에 블레이드 고정** 옵션을 선택합니다.
   ![대시보드 옵션에 블레이드 고정][2]

## <a name="use-security-center"></a>Security Center 사용
Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성할 수 있습니다. 다음과 같이 구독에 대한 보안 정책을 구성하세요.

1. **Security Center** 블레이드에서 **정책** 타일을 선택합니다.
   ![보안 정책][3]
2. **보안 정책 – 구독 또는 리소스 그룹별 정책 정의** 블레이드에서 구독을 선택합니다.
3. **보안 정책** 블레이드에서 자동으로 로그를 수집하도록 **데이터 수집**을 활성화합니다. 구독에 있는 현재 VM과 새로운 VM에 모니터링 확장이 프로비전됩니다. (**데이터 수집**을 **끄기**로 설정하여 데이터 수집을 해제할 수 있지만, 이렇게 하면 Security Center에서 보안 경고 및 권장 사항을 제공하지 않습니다.)
4. **보안 정책** 블레이드에서 **지역별 저장소 계정 선택**을 선택합니다. VM이 실행 중인 각 영역에 대해 VM에서 수집한 데이터가 저장되는 저장소 계정을 선택합니다. 각 지역별로 저장소 계정을 선택하지 않으면 사용자를 위한 계정이 만들어집니다. 수집된 데이터는 보안상의 이유로 다른 고객의 데이터와 논리적으로 격리됩니다.

   > [!NOTE]
   > 먼저 구독 수준에서 데이터 수집을 활성화하고 저장소 계정을 선택하는 것이 좋습니다. Azure 구독 수준 및 리소스 그룹 수준의 보안 정책을 설정할 수 있지만, 데이터 수집 및 저장소 계정은 구독 수준에서만 구성됩니다.
   >
   >
5. **보안 정책** 블레이드에서 **방지 정책**을 선택합니다. 그러면 **방지 정책** 블레이드가 열립니다.
   ![방지 정책][4]
6. **방지 정책** 블레이드에서 보안 정책의 일부로 보려는 권장 사항을 설정합니다. 예제:

   * **시스템 업데이트**를 **켜기**로 설정하면 지원되는 모든 가상 컴퓨터에 대해 누락된 OS 업데이트가 있는지 검사합니다.
   * **OS 취약성**을 **켜기**로 설정하면 지원되는 모든 가상 컴퓨터를 검사하여 가상 컴퓨터를 공격에 더 취약하게 만들 수 있는 OS 구성을 식별합니다.

### <a name="view-recommendations"></a>권장 사항 보기
1. **Security Center** 블레이드로 돌아가서 **권장 사항** 타일을 선택합니다. Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. Security Center에서 잠재적인 보안 취약성을 식별하는 경우 **권장 사항** 블레이드에서 권장 사항을 보여 줍니다.
   ![Azure Security Center의 권장 사항][5]
2. **권장 사항을** 블레이드에서 권장 사항을 선택하여 자세한 정보를 표시하거나 문제를 해결하기 위한 조치를 선택합니다.

### <a name="view-the-health-and-security-state-of-your-resources"></a>리소스의 상태 및 보안 상태 보기
1. **Security Center** 블레이드로 돌아갑니다. **리소스 보안 상태** 타일에는 가상 컴퓨터, 네트워킹, 데이터 및 응용 프로그램의 보안 상태에 대한 지표가 포함되어 있습니다.
2. 자세한 내용을 보려면 **가상 컴퓨터** 를 선택합니다. **가상 컴퓨터** 블레이드가 열려 가상 컴퓨터의 맬웨어 방지 프로그램, 시스템 업데이트, 다시 시작 및 OS 취약성의 상태 요약을 보여 줍니다.
   ![Azure Security Center의 리소스 상태 타일][6]
3. **가상 컴퓨터 권장 사항**에서 권장 사항을 선택하여 자세한 정보를 보거나 필요한 제어를 구성하는 작업을 선택합니다.
4. **가상 컴퓨터** 아래에서 VM을 선택하여 세부 정보를 추가로 표시합니다.

### <a name="view-security-alerts"></a>보안 경고 보기
1. **Security Center** 블레이드로 돌아가서 **보안 경고** 타일을 선택합니다. **보안 경고** 블레이드가 열려 경고 목록을 보여 줍니다. 보안 로그 및 네트워크 활동의 보안 상태 분석에서 이러한 경고를 생성합니다. 통합된 파트너 솔루션에서 보내는 경고도 있습니다.
   ![Azure Security Center의 보안 경고][7]

   > [!NOTE]
   > Security Center의 표준 계층을 사용하도록 설정된 경우에만 보안 경고를 제공합니다. 표준 계층의 90일 무료 평가판을 사용할 수 있습니다. 표준 계층을 가져오는 방법에 대한 내용은 [다음 단계](#next-steps)를 참조하세요.
   >
   >
2. 경고를 선택하여 추가적인 정보를 표시합니다. 이 예에서는 **수정된 시스템 이진 파일 검색**을 선택합니다. 이렇게 하면 해당 경고에 대한 세부 정보를 추가로 제공하는 블레이드가 열립니다.
   ![Azure Security Center의 보안 경고 세부 정보][8]

### <a name="view-the-health-of-your-partner-solutions"></a>파트너 솔루션의 상태 보기
1. **Security Center** 블레이드로 돌아갑니다. **파트너 솔루션** 타일에서 Azure 구독과 통합된 파트너 솔루션의 상태를 한 눈에 모니터링할 수 있습니다.
2. **파트너 솔루션** 타일을 선택합니다. 블레이드가 열리고 Security Center에 연결된 파트너 솔루션의 목록을 보여 줍니다.
   ![파트너 솔루션][9]
3. 파트너 솔루션을 선택합니다. 이 예에서는 **F5-WAF2** 솔루션을 선택합니다.  블레이드가 열리고 파트너 솔루션의 상태 및 솔루션의 관련 리소스를 보여 줍니다. **솔루션 콘솔** 을 선택하여 이 솔루션에 대한 파트너 관리 환경을 엽니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 보안 모니터링 및 정책 관리 구성 요소를 소개했습니다. 이제 Security Center에 익숙해졌으므로 다음 단계를 수행하세요.

* Azure 구독의 보안 정책을 구성합니다. 자세한 내용은 [Azure Security Center의 보안 정책 설정](security-center-policies.md)을 참조하세요.
* Security Center에서 권장 사항을 사용하여 Azure 리소스를 보호합니다. 자세한 내용은 [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.
* 현재 보안 경고를 검토하고 관리합니다. 자세한 내용은 [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.
* Security Center의 [표준 계층](security-center-pricing.md)과 함께 제공되는 [고급 위협 탐지 기능](security-center-detection-capabilities.md)에 대해 알아봅니다. 표준 계층의 90일 무료 평가판을 사용할 수 있습니다.
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



<!--HONumber=Dec16_HO2-->


