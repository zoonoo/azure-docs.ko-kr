<properties title="Planned maintenance for Azure virtual machines" pageTitle="Planned maintenance for Azure virtual machines" description="Understand what Azure planned maintenance is and how it affects your virtual machines running in Azure." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kenazk" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kenazk"></tags>

# Azure 가상 컴퓨터에 대한 계획된 유지 관리

## Azure에서 계획된 유지 관리를 수행하는 이유

Microsoft Azure는 가상 컴퓨터의 기반이 되는 호스트 인프라의 안정성, 성능 및 보안을 향상시키기 위해 전 세계적으로 주기적인 업데이트를 수행합니다. 이러한 많은 업데이트는 가상 컴퓨터 또는 클라우드 서비스에 영향을 주지 않고 수행됩니다. 그렇지만 이러한 업데이트 일부는 인프라에 필요한 업데이트를 적용하기 위해 가상 컴퓨터를 다시 부팅해야 합니다. 가상 컴퓨터는 인프라를 패치하는 동안 종료되었다가 다시 시작됩니다. 가상 컴퓨터의 가용성에 영향을 미칠 수 있는 유지 관리 종류에는 계획된 유지 관리와 계획되지 않은 유지 관리가 있습니다. 이 페이지에서는 Microsoft Azure에서 계획된 유지 관리를 수행하는 방법을 설명합니다. 계획되지 않은 유지 관리에 대한 자세한 내용은 [계획된 유지 관리 및 계획되지 않은 유지 관리 이해][계획된 유지 관리 및 계획되지 않은 유지 관리 이해]를 참조하세요.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

-   [가상 컴퓨터 구성][가상 컴퓨터 구성]
-   [다중 인스턴스 업데이트][다중 인스턴스 업데이트]
-   [단일 인스턴스 업데이트][단일 인스턴스 업데이트]
-   [메일 알림][메일 알림]

## 가상 컴퓨터 구성

가상 컴퓨터 구성에는 다중 인스턴스 및 단일 인스턴스의 두 가지 종류가 있습니다. 다중 인스턴스 가상 컴퓨터는 동일한 가상 컴퓨터를 가용성 집합에 배치하여 구성됩니다. 다중 인스턴스 구성은 중복성을 제공하고 응용 프로그램의 가용성을 보장하기 위해 권장됩니다. 가용성 집합의 모든 가상 컴퓨터는 거의 동일하며 응용 프로그램에 동일한 목적을 충족시켜야 합니다. 고가용성을 위한 가상 컴퓨터 구성에 대한 자세한 내용은 " [가상 컴퓨터의 가용성 관리][가상 컴퓨터의 가용성 관리]”를 참조하세요.

이와는 반대로 단일 인스턴스 가상 컴퓨터는 가용성 집합에 추가되지 않는 독립 실행형 가상 컴퓨터입니다. 단일 인스턴스 가상 컴퓨터 자체는 동일한 가용성 집합 하에서 배포된 둘 이상의 가상 컴퓨터를 요구하는 SLA(서비스 수준 계약)에 해당되지 않습니다. SLA에 대한 자세한 내용은 [서비스 수준 계약][서비스 수준 계약]의 "클라우드 서비스, 가상 컴퓨터 및 가상 네트워크" 섹션을 참조하세요.

## 다중 인스턴스 업데이트

계획된 유지 관리 동안 Azure 플랫폼은 먼저 다중 인스턴스 구성에 가상 컴퓨터 집합을 호스트하는 호스트 컴퓨터 집합을 업데이트하여 이러한 가상 컴퓨터가 다시 부팅되도록 합니다. 다중 인스턴스 구성의 가상 컴퓨터는 프로세스 전체에서 가용성이 유지되는 방식으로 업데이트되며, 각 컴퓨터는 집합 내의 다른 컴퓨터와 비슷한 기능을 제공하는 것으로 간주됩니다. 기본 Azure 플랫폼에서는 가용성 집합의 각 가상 컴퓨터를 UD(업데이트 도메인) 및 FD(장애 도메인)에 할당합니다. 각 UD는 동일한 시간에 다시 부팅되는 가상 컴퓨터 그룹입니다. 각 FD는 공통된 전원 및 네트워크 스위치를 공유하는 가상 컴퓨터 그룹입니다.

UD 및 FD에 대한 자세한 내용은 “[중복성을 위해 가용성 집합의 다중 가상 컴퓨터 구성][중복성을 위해 가용성 집합의 다중 가상 컴퓨터 구성]”을 참조하세요.

Microsoft Azure는 계획된 유지 관리 이벤트로 인해 두 개의 다른 UD의 가상 컴퓨터가 동시에 오프라인 상태가 되지 않을 것임을 보장합니다. 이러한 유지 관리는 각 가상 컴퓨터를 종료하고, 호스트 컴퓨터에 업데이트를 적용하고, 가상 컴퓨터를 다시 시작한 후 다음 UD로 이동함으로써 수행됩니다. 계획된 유지 관리 이벤트는 모든 UD가 업데이트되면 끝납니다. 계획된 유지 관리 중에 UD의 다시 부팅이 순차적으로 진행되지 않을 수 있지만 한 번에 하나의 UI만 다시 부팅됩니다. 지금은 다중 인스턴스 구성에서 가상 컴퓨터에 대해 계획된 유지 관리의 사전 알림이 제공되고 있지 않습니다.

가상 컴퓨터가 복원된 후에 Windows 이벤트 뷰어에 표시되는 내용의 예는 다음과 같습니다.

<!--Image reference-->

![][]

## 단일 인스턴스 업데이트

다중 인스턴스 업데이트가 완료되면 Azure는 단일 인스턴스 가상 컴퓨터를 호스트하는 호스트 컴퓨터 집합에 대해 업데이트를 수행합니다. 이 업데이트에서도 가용성 집합에서 실행되지 않는 가상 컴퓨터가 다시 부팅됩니다. 가용성 집합에서 하나의 인스턴스만 실행되고 있더라도 Azure 플랫폼은 이를 다중 인스턴스로 취급합니다. 단일 인스턴스 구성의 가상 컴퓨터는 가상 컴퓨터를 종료하고 호스트 컴퓨터에 업데이트를 적용한 후 가상 컴퓨터를 다시 시작하여 업데이트됩니다. 이러한 업데이트는 단일 유지 관리 기간에 특정 지역의 모든 가상 컴퓨터에서 실행됩니다. 이러한 계획된 유지 관리 이벤트는 이러한 유형의 가상 컴퓨터 구성에서 응용 프로그램의 가용성에 영향을 미칩니다.

### 메일 알림

단일 인스턴스 구성의 가상 컴퓨터에서만, Azure는 곧 실시될 계획된 유지 관리를 사용자에게 알리기 위해 적어도 1주일 전에 전자 메일 통신을 보냅니다. 이 전자 메일은 구독이 제공하는 기본 전자 메일 계정으로 전송됩니다. 이러한 유형의 전자 메일의 한 가지 예는 아래와 같습니다.

<!--Image reference-->

![][1]

<!--Anchors--> 
<!--Link references-->

  [계획된 유지 관리 및 계획되지 않은 유지 관리 이해]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/
  [가상 컴퓨터 구성]: #virtual-machine-configurations
  [다중 인스턴스 업데이트]: #multi-instance-update
  [단일 인스턴스 업데이트]: #single-instance-update
  [메일 알림]: #email-notification
  [가상 컴퓨터의 가용성 관리]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-manage-availability/
  [서비스 수준 계약]: ../../support/legal/sla/
  [중복성을 위해 가용성 집합의 다중 가상 컴퓨터 구성]: http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
  []: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
  [1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
