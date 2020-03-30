---
title: Visual Studio 및 Azure 서비스를 통해 클라이언트 응용 프로그램을 빌드하기 위한 프런트 엔드 개발 플랫폼 선택
description: 클라이언트 응용 프로그램을 빌드하기 위해 지원되는 기본 및 플랫폼 간 언어에 대해 알아봅니다.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240852"
---
# <a name="choose-mobile-development-frameworks"></a>모바일 개발 프레임워크 선택
개발자는 플랫폼 간 접근 방식에 특정 프레임워크와 패턴을 사용하여 클라이언트 측 기술을 사용하여 모바일 응용 프로그램을 자체적으로 빌드할 수 있습니다. 개발자는 의사 결정 요인에 따라 다음을 구축할 수 있습니다.
- Objective C 및 Java와 같은 언어를 사용하여 네이티브 단일 플랫폼 응용 프로그램
- Xamarin, .NET 및 C를 사용하여 플랫폼 간 응용 프로그램 #
- 코르도바와 그 변형을 사용하여 하이브리드 응용 프로그램

## <a name="native-platforms"></a>네이티브 플랫폼
기본 응용 프로그램을 빌드하려면 플랫폼별 프로그래밍 언어, SDK, 개발 환경 및 OS 공급업체에서 제공하는 기타 도구가 필요합니다.

### <a name="ios"></a>iOS
애플이 개발하고 개발한 iOS는 애플 기기, 즉 아이폰과 아이패드에서 앱을 제작하는 데 사용된다.

- **프로그래밍 언어**: 객관적-C, 신속한
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
구글과 세계에서 가장 인기있는 OS에 의해 설계, 안드로이드는 스마트 폰과 태블릿의 범위에서 실행할 수있는 응용 프로그램을 구축하는 데 사용됩니다.

- **프로그래밍 언어**: 자바, 코틀린 
- **IDE**: 안드로이드 스튜디오와 안드로이드 개발자 도구 
- **SDK**: 안드로이드 SDK

### <a name="windows"></a>Windows
- **프로그래밍 언어**: C #
- **IDE**: 비주얼 스튜디오, 비주얼 스튜디오 코드
- **SDK**: 윈도우 SDK

#### <a name="pros"></a>장점
- 좋은 사용자 경험
- 고성능 및 네이티브 라이브러리와 인터페이스할 수 있는 기능의 반응형 애플리케이션
- 매우 안전한 애플리케이션

#### <a name="cons"></a>단점
- 응용 프로그램은 하나의 플랫폼에서만 실행됩니다.
- 응용 프로그램을 빌드하는 데 더 많은 개발자 리소스 집약적이고 비용이 많이 듭니다.
- 낮은 코드 재사용

## <a name="cross-platforms-and-hybrid-applications"></a>크로스 플랫폼 및 하이브리드 애플리케이션
플랫폼 간 응용 프로그램은 네이티브 모바일 응용 프로그램을 한 번 작성하고 코드를 공유하며 iOS, Android 및 Windows에서 실행할 수 있는 권한을 제공합니다.

### <a name="xamarin"></a>Xamarin
Microsoft가 소유한 [Xamarin은](https://visualstudio.microsoft.com/xamarin/) C#에서 강력한 크로스 플랫폼 모바일 응용 프로그램을 빌드하는 데 사용됩니다. Xamarin에는 iOS, Android 및 Windows와 같은 여러 플랫폼에서 작동하는 클래스 라이브러리 및 런타임이 있습니다. 또한 높은 성능을 제공하는 네이티브(해석되지 않은) 응용 프로그램을 컴파일합니다. Xamarin은 네이티브 플랫폼의 모든 기능을 결합하고 자체의 강력한 기능을 추가합니다.

- **프로그래밍 언어**: C #
- **IDE**: 윈도우 또는 맥에 비주얼 스튜디오

### <a name="react-native"></a>React Native
2015년 페이스북에서 출시된 React [open-source](https://github.com/facebook/react-native) [Native는](https://facebook.github.io/react-native/) iOS 및 Android용 모바일 애플리케이션을 기본으로 렌더링하는 실제 자바스크립트 프레임워크입니다. 그것은 반응, 사용자 인터페이스를 구축하기위한 페이스 북의 자바 스크립트 라이브러리를 기반으로합니다. 브라우저를 타겟팅하는 대신 모바일 플랫폼을 대상으로 합니다. React Native는 웹 구성 요소 대신 기본 구성 요소를 구성 요소로 사용합니다.
 
- **프로그래밍 언어**: 자바 스크립트
- **IDE**: 비주얼 스튜디오 코드

### <a name="unity"></a>Unity
 Unity는 게임 제작에 최적화된 엔진입니다. Windows, iOS, Android 및 Xbox와 같은 플랫폼에서 C#을 사용하여 고품질 2D 또는 3D 응용 프로그램을 만드는 데 사용할 수 있습니다.

### <a name="cordova"></a>Cordova
코르도바는 코르도바에 대한 확장과 아파치 코르도바 또는 비주얼 스튜디오 코드에 대한 시각적 스튜디오 도구를 사용하여 하이브리드 응용 프로그램을 구축 할 수 있습니다. 하이브리드 접근 방식을 사용하면 웹 사이트와 구성 요소를 공유하고 Cordova를 기반으로 하는 호스팅 웹 응용 프로그램 접근 방식을 사용하여 웹 서버 기반 응용 프로그램을 재사용할 수 있습니다.

#### <a name="pros"></a>장점
- 여러 플랫폼에 대해 하나의 코드베이스를 만들어 코드 유용성 향상
- 다양한 플랫폼에서 더 많은 잠재고객에게 제공
- 개발 시간 의 극적인 감소
- 쉽게 시작하고 업데이트할 수 있습니다.

#### <a name="cons"></a>단점
- 낮은 성능
- 유연성 부족
- 각 플랫폼에는 네이티브 응용 프로그램을 보다 창의적으로 만들 수 있는 고유한 기능 집합이 있습니다.
- UI 디자인 시간 증가
- 공구 제한
