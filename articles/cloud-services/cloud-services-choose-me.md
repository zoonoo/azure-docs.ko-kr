---
title: "Azure 계산 옵션 - 클라우드 서비스 | Microsoft Docs"
description: "Azure 계산 호스팅 옵션 및 작동 방식에 대해 알아봅니다. 앱 서비스, 클라우드 서비스 및 가상 컴퓨터"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 26e2becf7810950fd5734fd70c1fed225149ec4d
ms.lasthandoff: 03/24/2017


---
# <a name="should-i-choose-cloud-services-or-something-else"></a>클라우드 서비스 또는 다른 항목을 선택해야 합니까?
Azure 클라우드 서비스가 적절한 선택입니까? Azure는 응용 프로그램을 실행하기 위한 여러 호스팅 모델을 제공합니다. 각각은 다양한 서비스 세트를 제공하므로 정확히 무엇을 수행하고자 하는지에 따라 선택 항목이 달라집니다.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>클라우드 서비스에 대한 설명
클라우드 서비스는 [PaaS(Platform-as-a-Service)](https://azure.microsoft.com/overview/what-is-paas/) 의 예입니다. [앱 서비스](../app-service-web/app-service-web-overview.md)와 마찬가지로 이 기술은 확장성 있고 안정적이며 운영 비용이 저렴한 응용 프로그램을 지원하도록 설계되었습니다. 앱 서비스와 마찬가지로 클라우드 서비스도 VM에서 호스트되지만 VM에 대한 제어력이 높습니다. 클라우드 서비스 VM에 원하는 소프트웨어를 설치하여 원격으로 실행할 수 있습니다.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

더욱 긴밀하게 제어할수록 사용 편의성은 낮아집니다. 추가 제어 옵션이 필요한 경우가 아니면 일반적으로는 Cloud Services에 비해 App Service의 Web Apps에서 웹 응용 프로그램을 작동하고 실행하는 것이 더 빠르고 간편합니다.

Cloud Service 역할의 두 가지 형식이 있습니다. 둘 사이의 유일한 차이점은 가상 컴퓨터에서 역할을 호스팅하는 방식입니다.

* **웹 역할**  
IIS를 통해 앱을 자동으로 배포하고 호스팅합니다.

* **작업자 역할**  
IIS를 사용하지 않고 앱을 독립 실행형으로 실행합니다.

예를 들어, 간단한 응용 프로그램은 단일 웹 역할만 사용하여 웹 사이트를 제공할 수 있습니다. 좀더 복잡한 응용 프로그램은 웹 역할을 사용하여 사용자로부터의 수신 요청을 처리한 후 해당 요청을 작업자 역할에 전달하여 처리할 수 있습니다. (이 통신에는 [서비스 버스](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)나 [Azure 큐](../storage/storage-introduction.md)를 사용할 수 있습니다.)

이전 그림에 나와 있는 것처럼 단일 응용 프로그램의 모든 VM은 동일한 클라우드 서비스에서 실행됩니다. 사용자는 응용 프로그램 VM의 자동 부하 분산 요청으로 하나의 공용 IP 주소를 통해 응용 프로그램에 액세스합니다. 플랫폼은 하드웨어 오류를 방지하는 방식으로 Cloud Services 응용 프로그램에 VM을 [확장 및 배포](cloud-services-how-to-scale.md)합니다.

응용 프로그램이 가상 컴퓨터에서 실행된다 해도 클라우드 서비스가 IaaS가 아닌 PaaS를 제공한다는 사실을 이해하는 것이 중요합니다. 다음과 같이 생각해보겠습니다. Azure Virtual Machines처럼 IaaS로 응용 프로그램이 실행될 환경을 우선 만들고 구성한 후 응용 프로그램을 이 환경에 배포합니다. Azure 가상 컴퓨터처럼 IaaS로 응용 프로그램이 실행될 환경을 우선 만들고 구성한 후 응용 프로그램을 이 환경에 배포합니다. 각 VM에 운영 체제의 새 패치 버전을 배포하는 것과 같은 작업을 수행하며 많은 작업에 대해 관리해야 합니다. 그와 반대로 PaaS에서는 환경이 이미 존재하는 것과 같아 응용 프로그램을 배포하기만 하면 됩니다. 응용 프로그램이 실행되는 플랫폼을 관리합니다(운영 체제의 새 버전 배포 포함).

## <a name="scaling-and-management"></a>확장 및 관리
클라우드 서비스로 가상 컴퓨터를 만들지 않습니다. 대신 웹 역할 인스턴스 **세 개 웹 역할 인스턴스** 및 **두 개 작업자 역할 인스턴스**같이 원하는 개수를 Azure에게 알려 주는 구성 파일을 제공하고 플랫폼에서는 이를 만듭니다.  사용자는 이들 지원 VM의 [실제 크기](cloud-services-sizes-specs.md) 를 선택하지만 명시적으로 직접 만들는지는 않습니다. 응용 프로그램이 더 큰 부하를 처리해야 하면 더 많은 VM을 요구할 수 있으며 Azure가 그러한 인스턴스를 만듭니다. 부하가 감소하면 이러한 인스턴스를 종료하여 지불을 중지할 수 있습니다.

클라우드 서비스 응용 프로그램은 사용자가 사용할 수 있도록 일반적으로 두 단계 프로세스를 통해 만들어집니다. 우선 개발자가 응용 프로그램을 플랫폼의 [준비 영역에 업로드합니다](cloud-services-how-to-create-deploy.md) . 개발자가 응용 프로그램을 사용할 준비가 되면 Azure Portal을 사용하여 준비를 프로덕션으로 바꿉니다. [준비와 프로덕션 간의 이러한](cloud-services-nodejs-stage-application.md) 전환은 가동 중지 시간 없이 수행될 수 있어서 사용자를 방해하지 않고도 실행 중인 응용 프로그램을 새 버전으로 업그레이드할 수 있습니다.

## <a name="monitoring"></a>모니터링
클라우드 서비스는 모니터링도 제공합니다. Azure Virtual Machines처럼 클라우드 서비스는 오류가 발생한 물리적 서버를 검색하여 해당 서버에서 실행 중이었던 VM을 새 컴퓨터에서 다시 시작합니다. 하지만 클라우드 서비스는 하드웨어 오류뿐만 아니라 오류가 발생한 VM과 응용 프로그램도 검색합니다. 가상 컴퓨터와 달리 클라우드 서비스에는 각 웹 역할 및 작업자 역할 내에 에이전트가 있어서 오류가 발생할 때 새 VM 및 응용 프로그램 인스턴스를 시작할 수 있습니다.

클라우드 서비스의 PaaS 특성에는 다른 것도 있습니다. 가장 중요한 것 중 하나는 웹 역할 또는 작업자 역할 인스턴스에 오류가 발생할 때 이 기술로 구축된 응용 프로그램이 제대로 실행되도록 작성되어야 한다는 점입니다. 이렇게 하려면 클라우드 서비스 응용 프로그램이 자체 VM의 파일 시스템 상태를 관리해서는 안 됩니다. Azure 가상 컴퓨터로 만들어진 VM과 달리 클라우드 서비스 VM에 실행된 쓰기는 영구적이지 않습니다. 가상 컴퓨터 데이터 디스크가 가장 좋습니다. 대신 클라우드 서비스 응용 프로그램은 SQL 데이터베이스, Blob, 테이블 또는 일부 기타 외부 저장소에 모든 상태를 명확하게 작성해야 합니다. 이 방법으로 응용 프로그램을 구축하면 보다 쉽게 확장하고 오류를 방어할 수 있으며, 둘 다 클라우드 서비스의 중요한 목표입니다.

## <a name="next-steps"></a>다음 단계
[.NET으로 클라우드 서비스 앱 만들기](cloud-services-dotnet-get-started.md)  
[Node.js로 클라우드 서비스 앱 만들기](cloud-services-nodejs-develop-deploy-app.md)  
[PHP로 클라우드 서비스 앱 만들기](../cloud-services-php-create-web-role.md)  
[Python에서 클라우드 서비스 앱 만들기](cloud-services-python-ptvs.md)


