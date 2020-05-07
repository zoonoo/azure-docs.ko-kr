---
title: Azure Spring Cloud의 앱 상태 이해
description: Azure 스프링 클라우드의 앱 상태 범주에 대해 알아봅니다.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569005"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Azure Spring Cloud의 앱 상태 이해

Azure 스프링 클라우드 UI는 실행 중인 응용 프로그램의 상태에 대 한 정보를 제공 합니다.  응용 프로그램 유형의 일반적인 상태를 표시 하는 구독에서 각 리소스 그룹에 대 한 **앱** 옵션이 있습니다.  각 응용 프로그램 유형에 대해 **응용 프로그램 인스턴스**를 표시 합니다.

## <a name="apps-status"></a>앱 상태
응용 프로그램 유형의 일반적인 상태를 보려면 리소스 그룹의 왼쪽 탐색 창에서 **앱** 을 선택 합니다. 결과는 배포 된 앱의 상태를 표시 합니다.

* **프로 비전 상태** 는 배포의 프로 비전 상태를 표시 합니다.
* **실행 중인 인스턴스** 수/응용 프로그램 인스턴스의 수를 표시 합니다. 응용 프로그램을 중지 해야 하는 경우이 열에 *중지 됨*이 표시 됩니다.
* **등록 된 인스턴스** eureka에 등록 된 앱 인스턴스 수/원하는 앱 인스턴스 수를 표시 합니다. 응용 프로그램을 중지 해야 하는 경우이 열에 *중지 됨*이 표시 됩니다.


 ![앱 상태](media/spring-cloud-concept-app-status/apps-ui-status.png)

**배포 상태는 다음 값 중 하나로 보고 됩니다.**

| 열거형 | 정의 |
|:--:|:----------------:|
| 실행 중 | 배포가 실행 중 이어야 합니다. |
| 중지됨 | 배포를 중지 해야 합니다. |

**프로 비전 상태는 CLI 에서만 액세스할 수 있습니다.  이 값은 다음 값 중 하나로 보고 됩니다.**

| 열거형 | 정의 |
|:--:|:----------------:|
| 만드는 중 | 리소스를 만드는 중입니다. |
| 업데이트 | 리소스를 업데이트 하는 중입니다. |
| 성공 | 리소스를 제공 하 고 이진 파일을 배포 했습니다. |
| Failed | *성공* 목표를 달성 하지 못했습니다. |
| 삭제 중 | 리소스를 삭제 하 고 있습니다. 이렇게 하면 작업이 수행 되지 않으며 리소스를이 상태에서 사용할 수 없습니다. |

## <a name="app-instances-status"></a>앱 인스턴스 상태

배포 된 앱의 특정 인스턴스의 상태를 보려면 **앱** UI에서 앱의 **이름을** 클릭 합니다. 결과는 다음과 같이 표시 됩니다.
* **상태**: 인스턴스가 실행 중인지 또는 해당 상태 인지 여부
* **Discoverystatus**: Eureka 서버에서 앱 인스턴스의 등록 된 상태입니다.

 ![앱 인스턴스 상태](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**인스턴스 상태는 다음 값 중 하나로 보고 됩니다.**

| 열거형 | 정의 |
|:--:|:----------------:|
| 시작 중 | 지정 된 인스턴스에 이진 파일이 성공적으로 배포 되었습니다. Jar 파일을 제대로 실행할 수 없기 때문에 jar 파일을 부팅 하는 인스턴스가 실패할 수 있습니다. |
| 실행 중 | 인스턴스가 작동 합니다. |
| Failed | 여러 번 다시 시도한 후에 앱 인스턴스가 사용자의 이진을 시작 하지 못했습니다. |
| 종결 | 앱 인스턴스를 종료 하는 중입니다. |

**인스턴스의 검색 상태는 다음 값 중 하나로 보고 됩니다.**

| 열거형 | 정의 |
|:--:|:----------------:|
| UP | 앱 인스턴스가 eureka에 등록 되어 있고 트래픽을 받을 준비가 되었습니다. |
| OUT_OF_SERVICE | 앱 인스턴스가 Eureka에 등록 되어 트래픽을 수신할 수 있습니다. 그러나 의도적으로 트래픽을 종료 합니다. |
| 아래로 | 앱 인스턴스가 Eureka에 등록 되어 있지 않거나 등록 되어 있지만 트래픽을 수신할 수 없습니다. |


## <a name="see-also"></a>참고 항목
* [Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비](spring-cloud-tutorial-prepare-app-deployment.md)