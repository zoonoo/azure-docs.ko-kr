---
title: Visual Studio App Center 및 Azure 서비스를 사용한 모바일 앱 개발의 다양 한 단계
description: 모바일 응용 프로그램 개발과 관련 된 다양 한 단계 및 App Center와 같은 Microsoft 서비스를 통해 고급 모바일 응용 프로그램을 빌드하는 방법을 알아봅니다.
author: elamalani
ms.assetid: 12bbb070-9b3c-1234-5678-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 6349f90eeacaedb03db4d6e91902fea69a8d0dae
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818011"
---
# <a name="different-stages-in-mobile-application-development"></a>모바일 응용 프로그램 개발의 다양 한 단계
모바일 응용 프로그램을 빌드하는 데는 여러 단계가 포함 되며 개발자는 Microsoft 서비스, 도구 및 기술을 활용 하 여 고급 모바일 응용 프로그램을 쉽게 빌드할 수 있습니다.

## <a name="app-development"></a>앱 개발
개발자는 목표-C, Java, Xamarin을 사용 하는 플랫폼 간 앱, Cordova를 사용 하 여 네이티브, Unity 및 하이브리드에 반응 하는 등의 언어로 네이티브 단일 플랫폼 클라이언트 응용 프로그램을 빌드할 수 있습니다. 클라이언트 응용 프로그램을 구성 하기 위해 Pc 및 Mac에서 Visual Studio, Mac용 Visual Studio 또는 Visual Studio Code와 같은 Ide 및 코드 편집기를 사용할 수 있습니다.

## <a name="plan-and-design-with-back-end-services"></a>백 엔드 서비스를 사용 하 여 계획 및 디자인
 Microsoft는 모바일 응용 프로그램을 위한 안전 하 고 확장 가능 하며 안정적인 백 엔드를 설정 하기 위한 다양 한 서비스를 제공 합니다. 개발자는 자체 서버를 유지 관리할 필요 없이 서버 리스 Api 및 트리거를 만들 수 있습니다. 이러한 서비스는 푸시 알림, 오프 라인 데이터 동기화, 인증 및 데이터와 같은 서비스를 활용할 수 있으며,이를 통해 다양 한 앱 생성 시나리오를 지 원하는 서비스로 포괄적이 고 광범위 한 모바일 백 엔드를 구성할 수 있습니다. 백 엔드 서비스를 구축 하려면 많은 요구 사항이 있습니다.
   - 장치에 데이터를 저장 하는 것을 제한 합니다.
   - 여러 장치에서 데이터를 동기화 합니다.
   - 네트워크 연결 없이 응용 프로그램이 실행 되도록 합니다.
   - 사용자에 게 알림을 보냅니다.
   - 배터리 소비를 줄입니다.
   - 확장성, 안정성 및 보안 백 엔드를 구축 합니다.
   - 자동 서버 유지 보수.

## <a name="devops-and-continuous-monitoring"></a>DevOps 및 연속 모니터링
모바일 및 클라우드 중심 응용 프로그램을 만드는 개발자는 CI (지속적인 통합), CD (지속적인 업데이트) 및 연속 모니터링 서비스를 활용 하 여 수천 개의 플랫폼에서 UI 자동화 테스트를 실행 하는 모든 플랫폼에 대 한 네이티브 빌드를 생성할 수 있습니다. 베타 테스트 채널을 통해 릴리스를 자동화 하거나 앱 스토어에 직접 배포 하는 물리적 장치. 또한 응용 프로그램에 대 한 사용 현황 분석, 충돌 및 예외 정보를 수집할 수 있습니다.

## <a name="additional-services"></a>서비스
빌드 중인 응용 프로그램의 유형 및 특성에 따라 많은 Azure 서비스가 사용 될 수 있습니다.
  - 빠른 쿼리를 사용 하 여 모든 유형의 내부 데이터를 검색 하는 Azure Cognitive Search입니다.
  - Azure Cognitive Services 응용 프로그램에서 인공 지능을 사용 합니다.
  - 응용 프로그램에서 실시간 통신을 가능 하 게 하는 Azure SignalR.
