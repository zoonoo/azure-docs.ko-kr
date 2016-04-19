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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/11/2016"
   ms.author="yurid"/>

#Azure 보안 센터에서 보안 상태 모니터링
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하여 정책 준수를 모니터링하는 방법을 설명합니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## Azure 보안 센터란?
보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

##보안 상태 모니터링이란?
상황에 대응할 수 있도록 이벤트가 발생할 때까지 이벤트를 감시하면서 기다리는 것을 모니터링이라고 생각하는 경우가 많습니다. 보안 모니터링은 리소스를 감사하여 조직의 표준 또는 모범 사례를 충족하지 않는 시스템을 식별하는 사전 예방 전략을 의미합니다.

##보안 상태 모니터링
구독의 리소스에 대한 [보안 정책](security-center-policies.md)을 활성화한 후에 보안 센터는 리소스의 보안을 분석하여 잠재적 취약성을 식별합니다. 네트워크 구성에 대한 정보는 즉시 이용할 수 있지만 보안 업데이트 상태, OS 구성 등 가상 컴퓨터 구성에 대한 정보를 이용하려면 1시간 이상이 걸립니다. **리소스 보안 상태** 블레이드의 문제와 함께 리소스의 보안 상태를 볼 수 있습니다. **권장 사항** 블레이드에서도 이러한 문제 목록을 볼 수 있습니다.

권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

**리소스 보안 상태** 타일에서 리소스의 보안 상태를 모니터링할 수 있습니다. 아래 예제와 같이 주의가 필요한 심각도가 높고 보통인 여러 문제를 확인할 수 있습니다. 활성화된 보안 정책은 모니터링되는 컨트롤 유형에 영향을 줍니다.

![리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig1-new2.png)

보안 센터가 보안 업데이트가 누락된 VM 또는 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)이 없는 서브넷 등과 같이 조치가 필요한 취약점을 식별하면 여기에 나열됩니다.

###가상 컴퓨터 모니터링
**리소스 보안 상태** 타일에서 **가상 컴퓨터**를 클릭하면 아래와 같이 보안 센터에서 모니터링된 전체 VM 목록 뿐만 아니라 등록 및 방지 단계에 대한 자세한 내용이 포함된 **가상 컴퓨터** 블레이드가 열립니다.

![VM에서 누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig2-2-new.png)

- 등록 단계
- 가상 컴퓨터 권장 사항
- 가상 컴퓨터

각 섹션에서 개별 옵션을 선택하면 해당 문제를 해결하는 권장 단계에 관한 자세한 내용을 볼 수 있습니다. 아래 섹션에서는 이 부분에 대해 더 자세히 설명합니다.

####등록 단계
이 섹션은 데이터 수집을 위해 초기화된 VM의 총 수 및 현재 해당 상태를 보여줍니다. 모든 VM에서 데이터 수집이 초기화되면 보안 센터 보안 정책을 수신할 준비가 됩니다. 이 항목을 클릭하면 **데이터 수집 초기화** 블레이드가 열리고 아래와 같이 **설치 상태** 열에서 VM의 이름 및 데이터 수집의 현재 상태를 확인할 수 있습니다.

![초기화 상태](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####가상 컴퓨터 권장 사항
이 섹션에는 Azure 보안 센터에서 모니터링하는 각 VM에 대한 권장 사항이 있습니다. 첫 번째 열에서는 권장 사항을 나열하고 두 번째 열에서는 해당 권장 사항의 영향을 받은 VM의 총 수를 표시하며 세 번째 열에서는 아래와 같이 문제의 심각도를 표시합니다.

![VM 권장 사항](./media/security-center-monitoring/security-center-monitoring-fig4-2-new.png)

각 권장 사항에는 클릭하면 수행되는 작업 집합이 있습니다. 예를 들어 **누락된 시스템 업데이트**를 클릭하면 **누락된 시스템 업데이트** 블레이드가 열립니다. 아래와 같이 패치를 누락한 VM 및 누락된 업데이트의 심각도를 나열합니다.

![누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

**누락된 시스템 업데이트** 블레이드에는 다음 정보를 포함하는 테이블이 표시됩니다.

- **가상 컴퓨터**: 업데이트가 누락된 가상 컴퓨터의 이름입니다.
- **시스템 업데이트**: 누락된 시스템 업데이트의 수입니다.
- **마지막 검사 시간**: 보안 센터가 업데이트를 위해 VM을 마지막으로 검사한 시간입니다.
- **상태**: 권장 사항의 현재 상태입니다. 
	- **열기**: 권장 사항이 아직 해결되지 않았습니다.
	- **진행 중**: 권장 사항이 현재 해당 리소스에 적용되고 있으며, 사용자가 아무런 작업도 수행할 필요가 없습니다.
	- **해결됨**: 권장 사항이 이미 완료되었습니다.(문제가 해결되면 항목이 회색으로 표시됩니다)
- **심각도**: 특정 권장 사항의 심각도를 설명합니다. 
	- **높음**: 중요한 리소스(응용 프로그램, VM, 네트워크 보안 그룹)에 취약점이 있으며 주의가 필요합니다.
	- **보통**: 프로세스를 완료하거나 취약점을 제거하는 데 필요한 중요하지 않은 또는 추가 단계입니다.
	- **낮음**: 취약점을 해결해야 하지만 즉각적인 주의가 필요하지 않습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 보려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.)

권장 사항 세부 정보를 보려면 VM의 이름을 클릭합니다. 해당 VM에 대한 새 블레이드가 아래와 같이 업데이트 목록을 사용하여 열립니다.

![VM별 누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] 여기에 표시되는 보안 권장 사항과 권장 사항 블레이에 표시되는 보안 권장 사항이 동일합니다. 권장 사항 해결에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요. 이는 VM뿐 아니라 리소스 상태 타일에서 제공되는 모든 리소스에 해당합니다.

####가상 컴퓨터 섹션
가상 컴퓨터 섹션에는 모든 VM과 권장 사항에 대한 개요가 제공됩니다. 각 열에는 아래와 같이 권장 사항 집합이 표시됩니다.

![VM](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

각 권장 사항에 표시되는 아이콘은 주의를 기울여야 하는 VM 및 권장 사항의 형식을 식별하는 데 도움이 됩니다.

위의 예제에서 하나의 VM에는 맬웨어 방지 프로그램에 관한 중요한 권장 사항이 있습니다. VM에 대한 자세한 정보를 가져오려면 해당 VM을 클릭합니다. 이 VM을 나타내는 새 블레이드가 아래와 같이 열립니다.

![VM 보안 세부 정보](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

이 블레이드에는 VM에 대한 보안 세부 정보가 있습니다. 이 블레이드의 하단에서 권장되는 작업과 각 문제의 심각도를 볼 수 있습니다.

###가상 네트워크 모니터링
네트워킹에 대한 방지 상태 섹션은 보안 센터에서 모니터링하는 가상 네트워크를 나열합니다. **리소스 보안 상태** 타일에서 **네트워킹**을 클릭하면 아래와 같이 자세한 내용이 포함된 **네트워킹** 블레이드가 열립니다.

![네트워킹](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

이 블레이드를 열면 두 개 섹션이 표시됩니다.
- 네트워킹 권장 사항
- 네트워킹
 
각 섹션에서 권장 사항에 대한 자세한 내용의 개별 옵션을 선택할 수 있습니다. 아래 섹션에서는 이 부분에 대해 더 자세히 설명합니다.

####네트워킹 권장 사항

가상 컴퓨터 리소스 상태 정보와 마찬가지로, 이 블레이드는 맨 위에서 요약된 문제 목록을 제공하고 하단에서 모니터링된 네트워크 목록을 제공합니다.

![끝점](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

네트워킹 상태 분석 섹션은 잠재적인 보안 문제를 나열하고 권장 사항을 제공합니다. 가능한 문제는 다음을 포함할 수 있습니다.

- [끝점에서 ACL](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)을 사용할 수 없음
- [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)을 사용할 수 없음
- 정상 서브넷 및 제한되지 않은 NSG의 액세스가 나열됩니다. 
 
이들 권장 사항 중 하나를 클릭하면 새 블레이드가 열리고 아래 예에 표시된 것과 같이 권장 사항에 대한 자세한 내용이 표시됩니다.

![끝점 제한](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

이 예제에서 **공용 외부 끝점을 통한 액세스 제한** 블레이드에는 이 경고의 일부분인 NSG(네트워크 보안 그룹), NSG가 연결된 서브넷과 네트워크, 권장 사항의 현재 상태 및 문제의 심각도의 목록이 있습니다. 네트워크 보안 그룹을 클릭하면, 또 다른 블레이드가 아래와 같이 열립니다.

이 블레이드에는 네트워크 보안 그룹 정보 및 위치가 있습니다. 또한 현재 사용하는 인바운드 규칙의 목록이 있습니다. 이 블레이드의 하단에는 네트워크 보안 그룹과 연관된 VM이 표시됩니다. 인바운드 규칙을 활성화하여 현재 열려 있지만 원하지 않는 포트를 차단하거나 현재 인바운드 규칙의 원본을 변경하려면 블레이드 맨 위의 **인바운드 규칙 편집** 단추를 클릭합니다.

####네트워킹 섹션

**네트워킹** 섹션에는 아래와 같이 VM과 연결된 리소스 그룹, 서브넷 및 네트워크 인터페이스에 대한 계층 구조 보기가 있습니다.

![네트워크 트리](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

이 섹션은 [클래식 VM에서 리소스 관리자 기반 VM](../resource-manager-deployment-model.md)을 나눕니다. 그러면 Azure 서비스 관리 또는 Azure 리소스 관리 네트워킹 기능을 가상 컴퓨터에 사용할 수 있는지 여부를 빠르게 확인할 수 있습니다. 이 위치에서 네트워크 인터페이스 카드의 속성에 액세스하려면 서브넷을 확장하고 VM 이름을 클릭해야 합니다. 리소스 관리자 기반 VM에 이 작업을 수행하면 아래와 유사한 새 블레이드가 표시됩니다.

![네트워크 트리](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

이 블레이드에는 네트워크 인터페이스 카드 및 그에 대한 현재 권장 사항의 요약 정보가 표시됩니다. 선택한 VM이 클래식 VM이면 아래와 같이 다른 옵션을 포함하는 새 블레이드가 나타납니다.

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

이 블레이드에서 공용 및 개인 포트를 변경할 수 있으며 이 VM에 대한 ACL을 만들 수 있습니다.

###SQL 리소스 모니터링
**리소스 보안 상태** 타일에서 **SQL**을 클릭하면 SQL 블레이드가 감사, 활성화되지 않은 투명한 데이터 암호화와 같은 문제에 대한 권장 사항을 통해 열립니다. 또한 데이터베이스의 일반 성능 상태에 대한 권장 사항이 있습니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

이러한 권장 사항을 클릭하고 문제 해결을 위해 추가 조치에 대한 자세한 정보를 가져올 수 있습니다. 아래 예에서는 **데이터베이스 감사 활성화되지 않음** 권장 사항의 확장을 보여줍니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

**SQL 데이터베이스에 감사 활성화** 블레이드에는 다음과 같은 정보가 있습니다.

- SQL 데이터베이스의 목록
- 데이터베이스가 위치한 서버
- 이 설정이 서버에서 상속됐는지 여부 또는 이 데이터베이스에서 고유한지 여부에 대한 정보
- 현재 상태
- 문제의 심각도

권장 사항을 처리하기 위하여 데이터베이스를 클릭하면 아래와 같이 **감사 및 위협 감지** 블레이드가 열립니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

감사를 활성화하려면 **감사** 옵션에서 **켜기**를 선택하고 **저장**을 클릭합니다.

###응용 프로그램 모니터링
Azure 워크로드에 노출된 웹 포트(TCP 포트 80 및 443)를 통해 [리소스 관리자 VM](../resource-manager-deployment-model.md)에 위치한 응용 프로그램이 있는 경우 보안 센터에서 해당 응용 프로그램을 모니터링하여 잠재적 보안 문제를 파악하고 해결 단계를 수정할 수 있습니다. **응용 프로그램** 타일을 클릭하면 아래와 같이 **응용 프로그램** 블레이드가 열리고 방지 단계 섹션에 일련의 권장 사항이 표시됩니다. 또한 호스트/가상 IP 당 응용 프로그램 분석 결과가 표시됩니다.

![응용프로그램 보안 상태](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

다른 권장 사항으로 이전에 수행한 것처럼 권장 사항을 클릭하여 문제에 대한 자세한 정보와 수정하는 방법을 확인할 수 있습니다. 아래 그림의 예는 안전하지 않은 웹 응용 프로그램으로 식별된 응용 프로그램입니다. 안전하지 않은 것으로 간주되는 응용 프로그램을 선택하면 또 다른 블레이드가 열리고 다음과 같은 옵션이 제공됩니다.

![앱](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

**안전하지 않은 웹 응용 프로그램** 블레이드에는 안전하지 않다고 간주되는 응용 프로그램을 포함하는 모든 VM의 목록이 있습니다. 목록은 VM 이름, 문제의 현재 상태 및 심각도를 표시합니다. 이 웹 응용 프로그램을 클릭하면 아래와 같이 타사 WAF(웹 응용 프로그램 방화벽)를 설치할 수 있는 옵션이 있는 **웹 응용 프로그램 방화벽 추가** 블레이드가 열립니다.

![WAF 추가](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## 다음 단계
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 보안 센터에서 보안 설정을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!---HONumber=AcomDC_0413_2016-->