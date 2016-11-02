<properties
   pageTitle="Azure 보안 센터에서 데이터 수집 활성화 | Microsoft Azure"
   description=" Azure 보안 센터에서 데이터 수집을 활성화하는 방법을 알아봅니다. "
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# Azure 보안 센터에서 데이터 수집 활성화

위협을 방지, 감지 및 대응하는 고객을 위해 Azure 보안 센터에서는 구성 정보, 메타데이터, 이벤트 로그 등을 포함한 Azure 가상 컴퓨터에 대한 데이터를 수집하고 처리합니다. 보안 센터에 처음 액세스할 경우 구독의 모든 가상 컴퓨터에서 데이터 수집을 활성화합니다. 데이터 수집를 사용하는 것이 좋지만 보안 센터 정책에서 데이터 수집을 해제하여 옵트아웃할 수 있습니다([데이터 수집 비활성화](#disabling-data-collection)를 참조). 데이터 수집을 해제하면 보안 센터는 해당 구독에 대한 보안 정책에서 데이터 수집을 켜도록 권장합니다.

> [AZURE.NOTE] 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.

## 권장 사항 구현

1. **보안 센터** 블레이드에서 **권장 사항 타일**을 선택합니다. 이렇게 하면 **권장 사항** 블레이드가 열립니다. ![보안 센터 블레이드][1]

2. **권장 사항** 블레이드에서 **구독에 대한 데이터 수집 활성화**를 선택합니다. 그러면 **데이터 수집 켜기** 블레이드가 열립니다. ![권장 사항 블레이드][2]

3. **데이터 수집 켜기** 블레이드에서 구독을 선택합니다. 해당 구독에 대한 **보안 정책** 블레이드가 열립니다.

4. **보안 정책** 블레이드에서 자동으로 로그를 수집하도록 **데이터 컬렉션**에서 **켜기**를 선택합니다. 데이터 컬렉션을 켜면 구독에 현재 포함된 VM과 지원되는 새 VM에 모니터링 확장이 프로비전됩니다. ![보안 정책 블레이드][3]

5.	**저장**을 선택합니다.

6.	**지역당 저장소 계정 선택**을 선택합니다. 가상 컴퓨터를 실행 중인 각 영역에 대해 가상 컴퓨터에서 수집한 데이터가 저장되는 저장소 계정을 선택합니다. 각 지역에 대한 저장소 계정을 선택하지 않으면 사용자를 위해 계정이 자동으로 생성됩니다. 이 예제에서는 **newstoracct**를 선택합니다. 구독에 대한 보안 정책에 돌아가서 다른 저장소 계정을 선택하여 나중에 저장소 계정을 변경할 수 있습니다. ![저장소 계정 선택][4]

7.	**확인**을 선택합니다.

> [AZURE.NOTE] 먼저 구독 수준에서 데이터 수집을 켜고 저장소 계정을 선택하는 것이 좋습니다. Azure 구독 수준 및 리소스 그룹 수준에서 보안 정책을 설정할 수 있지만 데이터 수집 및 저장소 계정 구성은 구독 수준에서만 발생합니다.

## 데이터 수집을 활성화한 후에

데이터 수집은 Azure 모니터링 에이전트 및 Azure 보안 모니터링 확장을 통해 사용하도록 설정됩니다. Azure 보안 모니터링 확장은 다양한 보안 관련 구성을 검색하고 이를 [Windows용 이벤트 추적](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx)(ETW)으로 보냅니다. 또한 운영 체제는 이벤트 로그 항목을 만듭니다. Azure 모니터링 에이전트는 이벤트 로그 항목을 읽으며 ETW는 이를 추적하고 분석을 위해 저장소 계정에 복사합니다. 또한 모니터링 에이전트는 저장소 계정에 크래시 덤프 파일을 복사합니다. 이는 보안 정책에 구성된 저장소 계정입니다.

## 데이터 수집 비활성화

언제든지 데이터 컬렉션을 비활성화할 수 있으며 이는 보안 센터에서 이전에 설치한 모니터링 에이전트를 제거합니다. 데이터 수집을 해제할 구독을 선택해야 합니다.

> [AZURE.NOTE] Azure 구독 수준 및 리소스 그룹 수준에서 보안 정책을 설정할 수 있지만 데이터 수집을 해제하려면 구독을 선택해야 합니다.

1.	**보안 센터** 블레이드로 돌아가서 **정책** 타일을 선택합니다. 그러면 **보안 정책–구독 또는 리소스 그룹당 정책 정의** 블레이드가 열립니다. ![정책 타일 선택][5]

2.	**보안 정책–구독 또는 리소스 그룹당 정책 정의** 블레이드에서 데이터 수집을 사용하지 않으려는 구독을 선택합니다. ![데이터 수집을 비활성화할 구독 선택][6]

3.	해당 구독에 대한 **보안 정책** 블레이드가 열립니다. 데이터 컬렉션에서 **끄기**를 선택합니다.

4.	상단 리본에서 **저장**을 선택합니다.

5.	위쪽 리본에서 **에이전트 삭제**를 선택하여 기존 가상 컴퓨터에서 에이전트를 삭제합니다.

## 참고 항목

이 문서에서는 보안 센터 권장 사항 "데이터 수집 활성화"를 구현하는 방법을 보여 주었습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
- [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure 보안 센터 FAQ](security-center-faq.md)-–서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/)--최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png

<!---HONumber=AcomDC_0803_2016-->