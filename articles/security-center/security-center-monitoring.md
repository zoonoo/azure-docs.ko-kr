<properties
   pageTitle="Azure 보안 센터에서 보안 상태 모니터링 | Microsoft Azure"
   description="이 문서에서는 Azure 보안 센터의 모니터링 기능을 시작하는 방법을 설명합니다."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>

#Azure 보안 센터에서 보안 상태 모니터링
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하여 정책 준수를 모니터링하는 방법을 설명합니다.

> [AZURE.NOTE]이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

##보안 상태 모니터링이란?
상황에 대응할 수 있도록 이벤트가 발생할 때까지 이벤트를 감시하면서 기다리는 것을 모니터링이라고 생각하는 경우가 많습니다. 보안 모니터링은 리소스를 감사하여 조직의 표준 또는 모범 사례를 충족하지 않는 시스템을 식별하는 사전 예방 전략을 의미합니다.

##보안 상태 모니터링
구독의 리소스에 대한 [보안 정책](security-center-policies.md)을 활성화하면 Azure 보안 센터에서 리소스 보안을 분석하여 잠재적 취약성을 식별합니다. 네트워크 구성에 대한 정보는 즉시 이용할 수 있는 반면 보안 업데이트 상태, OS 구성 등 가상 컴퓨터 구성에 대한 정보를 이용하려면 1시간 이상이 걸립니다. 리소스 보안 상태 블레이드의 문제와 함께 리소스의 보안 상태를 볼 수 있습니다. 권장 사항 블레이드에서도 이러한 문제 목록을 볼 수 있습니다.

권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

**리소스 상태** 타일을 사용하여 리소스의 보안 상태를 모니터링할 수 있습니다. 아래와 같이 심각도가 높음 및 보통인 여러 문제가 표시됩니다.

![리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig1.png)

활성화된 보안 정책은 여기서 모니터링되는 컨트롤 유형에 영향을 줍니다. Azure 보안 센터에서 보안 업데이트가 누락된 VM, [네트워크 보안 그룹](virtual-networks-nsg.md)이 없는 서브넷 등과 같이 조치가 필요한 취약점을 발견하면 여기에 식별됩니다.

###가상 컴퓨터
**리소스 상태** 타일에서 **가상 컴퓨터**를 클릭하면 아래와 같이 Azure 보안 센터에서 모니터링하는 전체 VM 목록과 온보딩 및 방지 단계에 대한 자세한 내용이 포함된 **가상 컴퓨터** 블레이드가 열립니다.

![VM에서 누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig2.png)

이 블레이드를 열면 세 개 섹션이 표시됩니다. 각 섹션에서 개별 옵션을 선택하면 해당 문제를 해결하는 권장 단계에 대한 자세한 내용을 볼 수 있습니다.

예를 들어 **예방 단계** 아래에서 **누락된 시스템 업데이트**를 클릭하면 아래와 같이 패치가 누락된 VM 목록과 각각의 심각도가 표시되는 **누락된 시스템 업데이트** 블레이드가 열립니다.

![누락된 시스템 업데이트 방지](./media/security-center-monitoring/security-center-monitoring-fig3.png)

권장 업데이트 세부 정보를 보려면 VM 이름을 클릭합니다. 해당 VM에 대한 새 블레이드가 열리고 누락된 업데이트 목록이 표시됩니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig4.png)

> [AZURE.NOTE]여기에 표시되는 보안 권장 사항과 권장 사항 블레이에 표시되는 보안 권장 사항이 동일합니다. 권장 사항 해결에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요. 이는 VM뿐 아니라 리소스 상태 타일에서 제공되는 모든 리소스에 해당합니다.

###네트워킹
네트워킹 방지 상태는 Azure 보안 센터에서 모니터링하는 가상 네트워크를 보여줍니다. 리소스 상태 타일에서 **네트워킹**을 클릭하면 아래와 같이 자세한 내용이 포함된 **네트워킹** 블레이드가 열립니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig5.png)

가상 컴퓨터 리소스 상태 정보와 마찬가지로, 이 블레이드의 상단에는 요약된 문제 목록, 하단에는 모니터링하는 네트워크 목록이 표시됩니다.

네트워킹 상태 분석 섹션에는 [끝점의 ACL](virtual-machines-set-up-endpoints.md)이 활성화되지 않음, [네트워크 보안 그룹](virtual-networks-nsg.md)이 활성화되지 않음, 서브넷 정상 등의 방지 단계가 나열됩니다. 이러한 방지 단계를 클릭하면 자세한 정보를 확인하고 문제 해결을 위한 추가 조치를 취할 수 있습니다.

**서브넷의 NSG가 활성화되지 않음** 경고를 예로 들 수 있습니다. 이 경고를 클릭하면 **서브넷에 대해 누락된 네트워크 보안 그룹 구성** 블레이드가 열립니다. 이 블레이드에는 네트워크 보안 그룹이 활성화되지 않은 서브넷에 대한 설명과 이 서브넷에 속하는 네트워크 인터페이스 카드 목록이 표시됩니다. 보안 그룹을 적용할 서브넷을 선택하고 **NSG 구성** 옵션을 클릭합니다. 아래와 같이 **네트워크 보안 그룹 선택** 블레이드가 열립니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig6.png)

###SQL
**리소스 상태** 타일에서 **SQL**을 클릭하면 **SQL** 블레이드가 열리고 감사가 활성화되지 않음, 투명한 데이터 암호화가 활성화되지 않음, 데이터베이스의 일반 성능 상태 등의 문제와 관련된 권장 사항이 표시됩니다. 블레이드의 아래쪽 영역에는 아래와 같이 SQL Server 분석 결과 그리고 각 서버에 속한 데이터베이스가 표시됩니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig7.png)

이러한 권장 사항을 클릭하면 자세한 정보를 확인하고 문제 해결을 위한 추가 조치를 취할 수 있습니다. 아래는 SQL 서버에 대한 감사 활성화를 확장하는 예입니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig8.png)

###응용 프로그램
Azure 워크로드에서 응용 프로그램이 [리소스 관리자 VM](resource-manager-deployment-model.md)에 있고 웹 포트(TCP 포트 80 및 443)가 노출된 경우 Azure 보안 센터에서 응용 프로그램을 모니터링하여 잠재적 보안 문제를 파악하여 해결 단계를 추천할 수 있습니다. **응용 프로그램** 타일을 클릭하면 아래와 같이 **응용 프로그램** 블레이드가 열리고 방지 단계 섹션에 일련의 권장 사항이 표시됩니다. 또한 호스트/가상 IP별 응용 프로그램 분석 결과가 표시됩니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig9.png)

이전 리소스에 대한 다른 권장 사항과 마찬가지로, 권장 사항을 클릭하여 문제에 대한 자세한 정보와 해결 방법을 확인할 수 있습니다. 아래 그림의 예는 **안전하지 않은 웹 응용 프로그램**으로 식별된 응용 프로그램입니다. 안전하지 않은 것으로 간주된 응용 프로그램을 선택하면 또 다른 블레이드가 열리고 옵션이 제공됩니다. 이 예에서는 웹 응용 프로그램 방화벽을 활성화하는 블레이드가 열립니다.

![VM에서 권장 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig10.png)

## 다음 단계
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 보안 센터에서 보안 설정을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!---HONumber=AcomDC_1217_2015-->