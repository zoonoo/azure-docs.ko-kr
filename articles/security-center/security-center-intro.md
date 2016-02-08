<properties
   pageTitle="Azure 보안 센터 소개 | Microsoft Azure"
   description="Azure 보안 센터, 주요 기능 및 작동 방법에 대해 알아봅니다."
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

# Azure 보안 센터 소개

Azure 보안 센터, 주요 기능 및 작동 방법에 대해 알아봅니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다. 예제 배포를 사용하여 서비스를 소개합니다.

## Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

##	주요 기능
Azure 보안 센터는 Azure에 기본 제공되는 쉽고 효과적인 위협 예방, 감지 및 대응 기능을 제공합니다. 주요 기능:

| | |
|----- |-----|
| 예방 | Azure 리소스의 보안 상태 모니터링 |
| | 회사의 보안 요구 사항 및 응용 프로그램 유형 또는 데이터의 민감도에 따라 Azure 구독에 대한 정책 정의 |
| | 정책 기반 보안 권장 사항을 사용하여 서비스 소유자에게 필요한 제어를 구현하는 과정 안내 |
| | Microsoft 및 파트너의 보안 서비스 및 어플라이언스를 빠르게 배포 |
| 감지 | Azure 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 보안 데이터를 수집 및 분석 |
| | Microsoft 제품과 서비스, 디지털 범죄와 인시던스 대응 센터 및 외부 피드의 글로벌 위협 인텔리전스를 이용 |
| | 기계 학습 및 행동 분석을 비롯한 고급 분석 적용 |
| 대응 | 우선 순위가 지정된 보안 문제/경고 제공 |
| | 공격 출처 및 영향을 받는 리소스에 대한 정보 제공 |
| | 현재 공격을 중지하고 미래 공격을 예방하는 데 도움이 되는 방법 제안 |

## 기능 소개 연습
Azure 보안 센터는 [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. 액세스하려면 [포털에 로그인](https://portal.azure.com)하여 **찾아보기**를 선택하고 **보안 센터** 옵션으로 스크롤하거나 이전에 포털 대시보드에 고정한 **보안 센터** 타일을 선택합니다.

![][1]

Azure 보안 센터에서 보안 정책 설정, 보안 구성 모니터링, 보안 경고 보기를 수행할 수 있습니다.

### 보안 정책

회사의 보안 요구 사항에 따라 응용 프로그램 유형 또는 각 구독의 데이터 민감도에 맞는 Azure 구독에 대한 정책을 정의합니다. 예를 들어 개발 또는 테스트에 사용되는 리소스의 요구사항은 프로덕션 응용 프로그램에 사용되는 보안 요구 사항과 다릅니다. 마찬가지로 PII 같은 규제된 데이터를 가진 응용 프로그램에 대해서는 더 높은 수준의 보안이 필요할 수 있습니다.

> [AZURE.NOTE] 보안 정책을 편집하려면 해당 구독의 소유자 또는 참여자여야 합니다.

구독 목록에 대한 **보안 정책** 타일을 클릭하고 정책 세부 정보를 볼 구독을 선택합니다.

![][2]

**데이터 수집**(위 참조) 보안 정책에 대한 데이터 수집을 사용합니다. 사용하도록 설정하면 제공되는 정보: - 보안 모니터링 및 권장 사항에 대해 모든 지원되는 가상 컴퓨터를 매일 검색 - 분석 및 위협 감지를 위한 보안 이벤트 수집

**권장 사항 표시:**(위 참조) 구독 내에서 리소스의 보안 요구를 기반으로 모니터링 및 권장할 보안 컨트롤을 선택할 수 있습니다.

### 보안 권장 사항

Azure 보안 센터는 Azure 리소스의 보안 상태를 분석하여 잠재적인 보안 취약성을 식별합니다. 권장 사항 목록은 필요한 컨트롤 구성 과정을 안내합니다. 예를 들면 다음과 같습니다.

- 맬웨어 방지 프로그램을 프로비전하면 악성 소프트웨어를 식별하여 제거하는 데 도움이 됩니다.
- 네트워크 보안 그룹 및 가상 컴퓨터에 대한 트래픽 제어 규칙 구성
- 웹 응용 프로그램의 대상을 지정한 공격에 대해 방어하는 데 도움이 되는 웹 응용 프로그램 방화벽 프로비전
- 누락된 시스템 업데이트 배포
- 권장 기준과 일치하지 않는 OS 구성 해결

권장 사항 목록에 대한 **권장 사항** 타일을 클릭합니다. 각 권장 사항을 클릭하여 추가 정보를 보거나 해결하기 위한 조치를 취합니다.

![][3]

### 리소스 상태

**리소스 상태** 타일은 환경의 전체 보안 상태를 가상 컴퓨터, 웹 응용 프로그램 및 다른 리소스 같은 리소스 유형별로 표시합니다.

**리소스 상태** 타일에서 리소스 유형을 선택하여 식별된 잠재적 보안 취약성 목록을 비롯한 더 자세한 정보(아래 예에서는 가상 컴퓨터)를 봅니다.

![][4]

### 보안 경고

Azure 보안 센터는 Azure 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 통합합니다. 위협이 감지되었을 때 보안 경고가 생성됩니다. 감지되는 사항의 예:

- 알려진 악성 IP 주소와 통신하는 손상된 가상 컴퓨터
- Windows 오류 보고를 사용하여 감지된 고급 맬웨어
- 가상 컴퓨터에 대한 무작위 공격
- 통합된 맬웨어 방지 프로그램 및 방화벽에서의 보안 경고

**보안 경고** 타일을 클릭하면 우선 순위가 지정된 경고의 목록이 표시됩니다.

![][5]

경고를 선택하면 공격 및 공격을 해결하는 방법 제안에 대한 더 자세한 정보가 표시됩니다.

![][6]

## 시작
Azure 보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. Azure 보안 센터는 Azure 구독을 사용하여 사용하도록 설정됩니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.

Azure 보안 센터는 [Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. 자세한 내용은 [포털 설명서](https://azure.microsoft.com/documentation/services/azure-portal/)를 참조하세요.

[Azure 보안 센터 시작](security-center-get-started.md) Azure 보안 센터의 보안 모니터링 및 정책 관리 구성 요소를 빠르게 안내합니다.

## 다음 단계
이 문서에서는 Azure 보안 센터, 주요 기능 및 시작하는 방법을 소개하였습니다. 자세한 알아보려면 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - 보안 정책을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) – 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지 알아보기
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) – Azure 리소스의 상태를 모니터링하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – 최신 Azure 보안 뉴스 및 정보 가져오기

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_0128_2016-->