---
title: Azure Service Fabric 메시 용 Visual Studio 최적화
description: 이 문서에서는 첫 번째 디버깅 실행(F5)이 더 신속하게 수행되도록 Service Fabric Mesh 프로젝트에서 Visual Studio 성능을 최적화하는 방법을 보여줍니다.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: aa7a959128d3bcdfcce67d3abeac245975339a9f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840305"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Service Fabric Mesh 프로젝트에서 Visual Studio 성능 최적화

이 문서에서는 첫 번째 디버깅 실행(F5)이 더 신속하게 수행되도록 Service Fabric Mesh 프로젝트에서 Visual Studio 성능을 최적화하는 방법을 보여줍니다.  

## <a name="change-visual-studio-settings"></a>Visual Studio 설정 변경
 
Visual Studio의 **도구**  >  **옵션**   >  **Service Fabric 메시 도구**  >  **일반**에서 다음 설정을 조정할 수 있습니다.

- **열린 프로젝트에서 필수 Docker 이미지 풀링**에서는 프로젝트가 로드되는 동안 이미지 다운로드 프로세스를 시작하여 첫 번째 디버깅 실행(F5)을 더 신속하게 수행할 수 있습니다.  
- **열린 프로젝트에서 애플리케이션 배포**에서는 프로젝트가 열린 후에 배포 프로세스를 시작하여 첫 번째 디버깅 실행(F5)을 더 신속하게 수행할 수 있습니다.  
- **닫힌 프로젝트에서 애플리케이션 제거**에서는 프로젝트가 닫힐 때 메시 앱을 제거하여 앱에 할당된 리소스(CPU, RAM)를 회수합니다.  

Visual Studio에서 '이미지를 끌어오거나', '준비하거나', '애플리케이션을 제거하는' Service Fabric 도구 출력 창에 메시지가 표시되면 위의 설정에 대한 참조에 것입니다. 이러한 설정을 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[메시 앱 디버그 자습서](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) 읽기