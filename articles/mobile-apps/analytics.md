---
title: Visual Studio App Center 및 Azure 서비스를 사용 하 여 모바일 응용 프로그램 사용 및 사용자 동작 이해
description: 사용자가 모바일 응용 프로그램을 사용 하는 방법을 이해 하 여 현명한 비즈니스 의사 결정을 내리는 데 도움이 되는 App Center와 같은 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 4dc3cea4497c55dda0d8da2ca29201615dadff19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241086"
---
# <a name="analyze-and-understand-mobile-application-use"></a>모바일 응용 프로그램 사용 분석 및 이해
사용자가 응용 프로그램을 어떻게 사용 하는지 어떻게 잘 이해 하 고 있나요? 응용 프로그램에 사용 되는 활성 사용자 수와 시간이 지남에 따라 사용이 어떻게 변경 되나요? 어떤 기능을 사용 하 고 있으며 가장 많이 사용 되는 기능은 무엇 인가요? 이러한 사용자의 기반 응용 프로그램의 최신 버전을 사용 하는 사용자는 몇 개입니까? 이러한 모든 질문은 앱을 성공적인 비즈니스로 전환 하는 것을 이해 하는 데 중요 합니다. 이러한 종류의 사용 현황 분석 질문에 답변 하려면 앱에서 사용 데이터를 수집 해야 합니다.

데이터를 더 자세히 살펴보면 응용 프로그램을 개선 하 고 사용자의 만족을 유지 하는 방법을 더 많이 찾을 수 있습니다. 데이터를 사용 하 여 실행 가능한 정보를 찾고 사용자의 만족도를 유지 하는 것이 중요 합니다.

## <a name="importance-of-analytics"></a>분석의 중요성
- 사용자를 이해 하 고 응용 프로그램과 상호 작용 하는 방법을 이해 하 고 응용 프로그램을 세밀 하 게 조정 하기 위해 응용 프로그램을 다시 제공 하 고 비즈니스를 성장 하는 뛰어난 환경을 제공 합니다.
- 사용 메트릭을 추적 하 여 응용 프로그램을 출시 하는 방법에 대 한 의사 결정을 내리고 고객에 게 더 나은 서비스를 제공 합니다.
- 응용 프로그램 성능을 측정 합니다.
- 응용 프로그램에서 사용할 값과 성능에 대해 알아봅니다.
- 변동 및 보존에 영향을 주는 문제에 대 한 데이터 기반 통찰력을 얻습니다.

다음 서비스를 사용 하 여 모바일 응용 프로그램 분석을 사용 하도록 설정 합니다.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 분석](/appcenter/analytics/) 을 사용 하면 중요 한 사항에 집중 하 여 대상 그룹을 확장할 수 있습니다. 사용자 세션, 인기 장치, OS 버전 및 동작 분석에 대 한 심층 보고 및 통찰력을 제공 합니다. 광범위 한 응용 프로그램 분석으로 모든 항목을 추적 하는 사용자 지정 이벤트를 쉽게 만듭니다.

   **주요 기능**
   - 사용 패턴, 사용자 채택 및 기타 참여 메트릭을 무료로 추적 하세요.
   - 사용자 지정 이벤트를 통해 추세, 사용자 동작 및 참여를 식별 합니다.
   - 단일 대시보드의 응용 프로그램 사용량 (일별, 주별, 월별), 세션, 장치 속성 및 사용자 인구 통계에 대 한 기본 메트릭 및 상세 정보를 가져옵니다.
   - 무제한 보존을 위해 모든 App Center 분석 데이터를 Azure로 계속 내보냅니다. App Center Analytics는 Azure Blob storage 및 Azure 애플리케이션 Insights로 내보내기를 지원 합니다.
   - 코 호트 (예: 보존, 깔때기형 분석 및)에 대 한 보다 심층적인 통찰력을 위해 Azure 애플리케이션 Insights와 통합 합니다.
   - 1 줄 SDK 통합을 사용 하 여 몇 분 내에 시작 하세요.
   - IOS, Android, macOS, tvOS, Xamarin, 기본, Unity 및 Cordova에 대 한 플랫폼 지원을 얻습니다.

   **참조**
   - [App Center에 등록](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center 분석 시작](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure Monitor
Azure Monitor는 원격 분석을 수집 하 고 분석 하 여 성능을 최대화 하 고 모바일 응용 프로그램을 모니터링 하는 도구를 제공 하는 [Application Insights](/azure/azure-monitor/app/app-insights-overview)포함 합니다. App Center Analytics를 사용 하 여 Application Insights으로 내보내기를 설정 하면 Application Insights을 활용할 수 있습니다. Application Insights은 App Center에서 제공 하는 분석 도구를 벗어나 응용 프로그램에서 사용자 지정 이벤트 원격 분석을 쿼리, 분할, 필터링 및 분석할 수 있습니다.

**주요 기능**
   - 사용자 지정 이벤트 원격 분석을 쿼리합니다.
   - 강력한 분할 기능을 사용 하 여 이벤트 원격 분석을 필터링 합니다.
   - 응용 프로그램의 변환, 보존 및 탐색 패턴을 분석 합니다. 다음을 사용할 수 있습니다.
     - Funnels는 변환 속도를 분석 하 고 모니터링 합니다.
     - 보존을 통해 응용 프로그램에서 시간에 따라 사용자를 얼마나 잘 유지 하는지 분석할 수 있습니다.
     - 시각화 및 텍스트를 공유할 수 있는 보고서에 결합 하는 통합 문서.
     - 코 호트는 다른 분석 도구에서 쉽게 참조할 수 있도록 특정 사용자 또는 이벤트 그룹의 이름을 지정 하 고 저장 합니다.

**참조**
- [Azure Portal](https://portal.azure.com/)
- [App Center 및 Application Insights를 사용 하 여 모바일 응용 프로그램 분석](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Application Insights에서 App Center 분석 사용](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) 은 세계 최고 수준의 클라우드로 연결 된 게임을 만드는 데 필요한 게임 서비스, 실시간 분석 및 LiveOps를 갖춘 완전 한 백 엔드 플랫폼을 제공 합니다. 이러한 서비스는 게임 개발자를 위해 시작할 장벽을 줄입니다. 게임을 통해 확장 되는 크고 작은 스튜디오 비용 효율적인 개발 솔루션을 제공 합니다. 이 서비스를 통해 플레이어를 스튜디오 하 고, 유지 하 고, 수익 창출 수 있습니다. PlayFab을 통해 개발자는 지능형 클라우드를 사용 하 여 게임을 빌드 및 운영 하 고 게임 데이터를 분석 하며 전반적인 게임 환경을 개선할 수 있습니다.

**주요 기능**
   - 실시간 대시보드를 모니터링 합니다.
   - 상위 메트릭을 통해 게임의 성능을 평가 합니다.
   - 자동 생성 된 보고서를 통해 게임의 일별 및 월별 성능의 요약을 검토 합니다. 게임 관리자에서 보고서를 보고 매일 다운로드 하거나 받은 편지 함으로 배달 되도록 할 수 있습니다.
   - A/B 테스트를 사용 하 여 실험을 실행 하 고 특정 변수에 대 한 최적의 설정을 결정 합니다.
   - 플레이어에 대해 조각화를 사용 하 여 자동화 된 플레이어 그룹을 정의 합니다.
    
**참조**
- [PlayFab 포털](https://developer.playfab.com/en-US/sign-up)
- [분석](/gaming/playfab/#pivot=documentation&panel=analytics)
- [빠른 시작](/gaming/playfab/#pivot=documentation&panel=quickstarts) 
