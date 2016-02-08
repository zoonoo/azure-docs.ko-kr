<properties
   pageTitle="Azure 보안 센터 시작 | Microsoft Azure"
   description="이 문서는 Azure 보안 센터를 신속하게 시작할 수 있도록 보안 모니터링 및 정책 관리 구성 요소를 안내하고 다음 단계로 연결해 줍니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Azure 보안 센터 시작

이 문서는 Azure 보안 센터를 신속하게 시작할 수 있도록 보안 모니터링 및 정책 관리 구성 요소를 안내하고 다음 단계로 연결해 줍니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다. 예제 배포를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.

## Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

## 필수 조건

Azure 보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. Azure 보안 센터는 구독을 사용하여 사용하도록 설정됩니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.

Azure 보안 센터는 [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. 자세한 내용은 [포털 설명서](https://azure.microsoft.com/documentation/services/azure-portal/)를 참조하세요.


## Azure 보안 센터 액세스

포털에서 다음 단계에 따라 Azure 보안 센터에 액세스합니다.

1. **찾아보기**를 선택하고 **보안 센터** 옵션으로 스크롤합니다. ![][1]

2. **보안 센터**를 선택합니다. 그러면 **보안 센터** 블레이드가 열립니다.
3. 나중에 **보안 센터** 블레이드에 간편하게 액세스하려면, **대시보드에 블레이드 고정** 옵션(오른쪽 위)을 선택합니다. ![][2]

## Azure 보안 센터 사용

구독에 대한 보안 **정책** 구성:

4. **보안 센터** 블레이드에서 **보안 정책** 타일을 클릭합니다.
5. **Security policy – Define policy per subscription**(보안 정책– 구독별 정책 정의) 블레이드에서 구독을 선택합니다.
6. **보안 정책** 블레이드에서 자동으로 로그를 수집하도록 **데이터 컬렉션**을 켭니다. **데이터 컬렉션**을 켜면 구독에 현재 포함된 VM과 새로운 VM에 모니터링 확장이 프로비전됩니다.
7. **저장소 계정 선택**을 클릭합니다. 가상 컴퓨터를 실행 중인 각 영역에 대해 가상 컴퓨터에서 수집한 데이터가 저장되는 저장소 계정을 선택합니다. 각 지역에 대한 저장소 계정을 선택하지 않으면, 사용자를 위해 계정이 생성됩니다. 수집된 데이터는 보안상의 이유로 다른 고객의 데이터와 논리적으로 격리됩니다.
8. 보안 정책의 일부로 표시하려는 **권장 사항**을 켭니다. 예제:

  - **시스템 업데이트**를 켜면 지원되는 모든 가상 컴퓨터에 누락된 OS 업데이트가 있는지를 검사합니다.
  - **기준 규칙**을 켜면 가상 컴퓨터를 공격에 취약하게 만들만한 OS 구성이 있는지를 식별하기 위해 지원되는 가상 컴퓨터를 검사합니다.

![][3]

**권장 사항** 표시:

9. **보안 센터** 블레이드로 돌아가서 **권장 사항** 타일을 클릭합니다.
10.	Azure 보안 센터에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 잠재적인 보안 취약성이 확인되면 이 위치에 권장 사항이 표시됩니다.
11.	각각의 권장 사항을 클릭하고 자세한 정보를 표시하거나 해결에 필요한 조치를 취합니다.

![][4]

**리소스 상태**를 통해 리소스의 보안 상태 및 상태 보기:

12.	**보안 센터** 블레이드로 돌아갑니다.
13.	**리소스 상태** 타일에는 **가상 컴퓨터**, **네트워킹**, **SQL**, **응용 프로그램**의 보안 상태에 대한 지표가 포함됩니다.
14.	자세한 내용을 보려면 **가상 컴퓨터**를 선택합니다.
15.	**가상 컴퓨터** 블레이드는 가상 컴퓨터의 맬웨어 방지 프로그램, 시스템 업데이트, 다시 시작, 기준 규칙을 비롯한 상태 요약을 표시합니다.
16.	**PREVENTION STEPS**(방지 단계) 아래 표시되는 항목을 선택하여 자세한 정보를 보거나 필요한 컨트롤을 구성하도록 조치를 취합니다.
17.	특정 가상 컴퓨터에 대해 추가적인 정보를 보려면 드릴다운합니다.

![][5]

**보안 경고** 표시:

19.	**보안 센터** 블레이드로 돌아가서 **보안 경고** 타일을 클릭합니다.
20.	**보안 경고** 블레이드에 경고 목록이 표시됩니다. 경고는 사용자의 보안 로그 및 네트워크 작업에 대한 Azure 보안 센터 분석을 통해 감지됩니다. 통합된 파트너 솔루션에서 보내는 경고도 포함됩니다. ![][6]

21.	경고를 클릭하여 추가적인 정보를 표시합니다.

  ![][7]

## 다음 단계
이 문서에서는 Azure 보안 센터의 보안 모니터링 및 정책 관리 구성 요소를 소개했습니다. 자세한 알아보려면 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - 보안 정책을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) – 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지 알아보기
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) – Azure 리소스의 상태를 모니터링하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – 최신 Azure 보안 뉴스 및 정보 가져오기

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0128_2016-->