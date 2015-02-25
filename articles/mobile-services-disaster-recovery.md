<properties pageTitle="재해가 발생한 경우 모바일 서비스 복구 - Azure 모바일 서비스" description="재해가 발생한 경우 모바일 서비스를 복구하는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="" authors="brettsam" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/11/2014" ms.author="brettsam"/>

# 재해가 발생한 경우 모바일 서비스 복구

Azure 모바일 서비스를 사용하여 앱을 배포하는 경우 서버 오류, 네트워크 장애, 데이터 손실, 광범위한 설비 손실과 같이 가용성 문제가 발생하는 경우 기본 제공 기능을 사용하여 비즈니스 연속성을 보장할 수 있습니다. Azure 모바일 서비스를 사용하여 앱을 배포하면 전통적인 온-프레미스 솔루션을 배포하는 경우 디자인, 구현, 관리에 사용하는 다수의 내결함성 및 인프라 기능을 활용할 수 있습니다. Azure는 매우 적은 비용으로 상당한 잠재적 오류를 완화합니다.

<h2><a name="prepare"></a>가능한 재해에 대비</h2>

가용성 문제가 발생하는 경우 더 손쉽게 복구하기 위해 미리 대비할 수 있습니다. 

+ **Azure 모바일 서비스 SQL 데이터베이스에서 데이터 백업**
	<br/>모바일 서비스 응용 프로그램 데이터는 Azure SQL 데이터베이스에 저장됩니다. [SQL 데이터베이스 비즈니스 연속성 지침]의 설명대로 데이터를 백업하는 것이 좋습니다.
+ **모바일 서비스 스크립트 백업**
	<br/>[Team Foundation Service] 또는 [GitHub]와 같은 소스 제어 시스템에 모바일 서비스 스크립트를 저장하고 모바일 서비스 자체의 복사본에만 의존하지는 않는 것이 좋습니다. 모바일 서비스 [소스 제어 기능]을 사용하거나 [Azure 명령줄 도구를 사용]하여 Azure 포털을 통해 스크립트를 다운로드할 수 있습니다. 포털에서 "preview"라는 레이블이 있는 기능은 특히 주의하세요. 해당 스크립트의 경우 복구가 보장되지 않으므로 고유한 소스 제어 원본에서 스크립트를 복구해야 할 수 있습니다.
+ **보조 모바일 서비스 예약**
	<br/>모바일 서비스의 가용성 문제가 발생하는 경우 대체 Azure 지역에 모바일 서비스를 다시 배포해야 할 수 있습니다. 용량을 사용할 수 있도록 하려면(예: 흔하지 않은 상황이지만 전체 지역이 손실된 경우) 대체 지역에 보조 모바일 서비스를 만들고 기본 서비스의 모드와 동일하거나 그보다 높은 모드로 설정하는 것이 좋습니다. 즉, 기본 서비스가 공유 모드인 경우 보조 서비스를 공유 또는 예약으로 만들 수 있습니다. 하지만 기본 서비스가 예약인 경우에는 보조 서비스도 예약이어야 합니다.


<h2><a name="watch"></a>문제 신호 감시</h2>

다음 상황은 복구 작업이 필요한 문제를 나타냅니다.

+ 모바일 서비스에 연결된 앱이 장시간 동안 모바일 서비스와 통신할 수 없습니다.
+ [Azure 포털]에서 모바일 서비스 상태가 **비정상**으로 표시됩니다.
+ Azure 포털에서 모바일 서비스의 모든 탭의 위쪽에 **비정상** 배너가 나타나고 관리 작업에서 오류 메시지가 발생합니다.
+ [Azure 서비스 대시보드]가 가용성 문제를 나타냅니다.

<h2><a name="recover"></a>재해 복구</h2>

문제가 발생하는 경우 서비스 대시보드를 사용하여 지침 및 업데이트를 얻습니다.
 
서비스 대시보드에서 메시지가 나타나는 경우 다음 단계를 실행하여 대체 Azure 지역에서 모바일 서비스를 실행 상태로 복원합니다. 보조 서비스를 미리 만들어 둔 경우 해당 용량을 사용하여 기본 서비스를 복원합니다. 복구 이후에 기본 서비스의 URL 및 응용 프로그램 키가 변경되지 않기 때문에 이를 참조하는 앱을 업데이트할 필요가 없습니다. 

중단 후에 모바일 서비스를 복구하려면

1. Azure 포털에서 서비스의 상태가 **비정상**으로 보고되는지 확인합니다.

2. 이미 보조 모바일 서비스를 예약해 둔 경우에는 이 단계를 건너뛰어도 됩니다.

   아직 보조 모바일 서비스를 예약해 두지 않았다면 이번에는 다른 Azure 지역에서 보조 모바일 서비스를 만듭니다. 해당 코드를 기본 서비스의 모드와 같거나 그보다 높게 설정합니다. 즉, 기본 서비스가 공유 모드인 경우 보조 서비스를 공유 또는 예약으로 만들 수 있습니다. 하지만 기본 서비스가 예약인 경우에는 보조 서비스도 예약이어야 합니다.

3. [명령줄 도구를 사용하여 모바일 서비스 자동화]의 설명대로 구독에서 작동하도록 Azure 명령줄 도구를 구성합니다.

4. 이제 보조 서비스를 사용하여 기본 서비스를 복구할 수 있습니다.

    > [AZURE.IMPORTANT] 이 단계에서 명령을 실행하면 보조 서비스가 삭제되어 해당 용량을 기본 서비스 복구에 사용할 수 있게 됩니다. 스크립트 및 설정을 유지하려는 경우에는 명령을 실행하기 전에 백업하는 것이 좋습니다.
    
   준비가 되면 다음 명령을 실행합니다.

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] 명령이 완료된 후 포털에서 변경 내용이 표시될 때까지 몇 분 정도 걸릴 수 있습니다.

5. 스크립트를 소스 제어에 있는 원본과 비교하여 모든 스크립트가 올바르게 복구되었는지 확인합니다. 대부분의 경우 스크립트는 데이터 손실 없이 자동으로 복구됩니다. 하지만 차이가 발견하는 경우에는 해당 스크립트를 수동으로 복구할 수 있습니다.

6. 복구된 서비스가 Azure SQL 데이터베이스와 통신하고 있는지 확인합니다. 복구 명령은 모바일 서비스를 복구하지만 원본 데이터베이스에 대한 연결은 유지합니다. 기본 Azure 지역의 문제가 데이터베이스에도 영향을 미치는 경우에는 복구된 서비스가 올바르게 실행되지 않을 수 있습니다. Azure 서비스 대시보드를 사용하여 특정 지역의 데이터베이스 상태를 검사할 수 있습니다. 원래 데이터베이스가 작동하지 않는 경우 다음과 같이 복구할 수 있습니다.
	+ [SQL 데이터베이스 비즈니스 연속성 지침]의 설명대로 Azure SQL 데이터베이스를 방금 모바일 서비스를 복구한 Azure 지역으로 복구합니다.
	+ Azure 포털에서 모바일 서비스의 **"구성"** 탭에서 "데이터베이스 변경"을 선택한 후 새로 복구된 데이터베이스를 선택합니다.

이제 모바일 서비스가 새 Azure 지역으로 복구되어 원래 URL을 사용하여 저장소 앱의 트래픽을 허용하는 상태가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[SQL 데이터베이스 비즈니스 연속성 지침]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[소스 제어 기능]: http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/store-scripts-in-source-control/
[Azure 명령줄 도구 사용]: http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/command-line-administration/
[Azure 포털]: http://manage.windowsazure.com/
[Azure 서비스 대시보드]: http://www.windowsazure.com/ko-kr/support/service-dashboard/
[명령줄 도구를 사용하여 모바일 서비스 자동화]: http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/command-line-administration/


<!--HONumber=42-->
