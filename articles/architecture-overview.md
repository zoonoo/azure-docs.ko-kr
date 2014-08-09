<properties  linkid="" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="waltpo" solutions="" manager="bjsmith" editor="mattshel" />

# 아키텍처

Azure에서 일반적인 디자인 패턴을 구현하는 방법을 알아봅니다.

### Azure 기호/아이콘 집합

[Azure 기호/아이콘 집합을 다운로드][1]하여 아키텍처 다이어그램, 교육 자료, 프레젠테이션, 데이터시트, 인포그래픽, 백서 등 Azure를 설명(또는 사용)하는 기술 자료를 만듭니다. PPT, Visio 또는 PNG 형식으로 기호를 다운로드할 수 있습니다. 사용자 의견을 알기 위해 다운로드에 사용자 의견을 제공하기 위한 지침도 포함되어 있습니다.

![Azure 기호/아이콘 집합](./media/architecture-overview/AzureSymbols.png)

## 디자인 패턴

### [경쟁 소비자][2]

![경쟁 소비자](./media/architecture-overview/CompetingConsumers.png)

여러 동시 소비자가 동일한 메시징 채널에 수신된 메시지를 처리할 수 있게 해 줍니다. 이 패턴을 사용하면 시스템에서 여러 메시지를 동시에 처리하여 처리량을 최적화하고 확장성 및 가용성을 향상시키고 작업 부하를 분산시킬 수 있습니다.

### [명령 및 쿼리 책임 분리][3]

![명령 및 쿼리 책임 분리](./media/architecture-overview/CQRS.png)

별도의 인터페이스를 사용하여 데이터를 업데이트하는 작업과 데이터를 읽는 작업을 분리합니다. 이 패턴은 시스템 성능, 확장성 및 보 을 극대화하고 유연성 증대를 통해 시간에 따른 시스템 발전을 지원하며 업데이트 명령으로 인한 도메인 수준의 병합 충돌을 방지할 수 있습니다.

### [리더 선택][4]

![리더 선택](./media/architecture-overview/LeaderElection.png)

다른 인스턴스를 관리하는 책임을 맡을 리더 인스턴스를 선택하여 분산 응용 프로그램에서 공동 작업 인스턴스 모음에 의해 수행되는 작업을 조정합니다. 이 패턴은 작업 인스턴스가 서로 충돌하거나, 공유 리소스를 위해 경쟁하거나, 다른 작업 인스턴스에서 수행 중인 작업을 실수로 방해하는 경우를 방지하는 데 도움이 됩니다.

### [파이프 및 필터][5]

![파이프 및 필터](./media/architecture-overview/PipesAndFilters.png)

복잡한 처리를 수행하는 작업을 다시 사용할 수 있는 일련의 개별 요소로 분해합니다. 이 패턴은 처리를 수행하는 작업 요소를 독립적으로 배포하고 크기를 조정할 수 있게 함으로써 성능, 확장성 및 재사용 가능성을 향상시킬 수 있습니다.

### [보조 키][6]

![보조 키](./media/architecture-overview/ValetKey.png)

응용 프로그램 코드에서 데이터 전송 작업을 오프로드하기 위해 특정 리소스나 서비스에 제한된 직접 액세스 권한을 클라이언트에 제공하는 토큰 또는 키를 사용합니다. 이 패턴은 클라우드에 호스트된 저장소 시스템이나 큐를 사용하는 응용 프로그램에서 특히 유용하며, 비용을 최소화하고 확장성과 성능을 극대화할 수 있습니다.

### 추가 지침

Azure의 다른 일반적인 디자인 패턴에 대한 자세한 내용은 [클라우드 디자인 패턴][7](영문)을 참조하십시오.



[1]: http://www.microsoft.com/en-us/download/details.aspx?id=41937
[2]: http://msdn.microsoft.com/ko-kr/library/dn568101.aspx
[3]: http://msdn.microsoft.com/ko-kr/library/dn568103.aspx
[4]: http://msdn.microsoft.com/ko-kr/library/dn568104.aspx
[5]: http://msdn.microsoft.com/ko-kr/library/dn568100.aspx
[6]: http://msdn.microsoft.com/ko-kr/library/dn568102.aspx
[7]: http://msdn.microsoft.com/ko-kr/library/dn568099.aspx