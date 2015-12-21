<properties 
	pageTitle="Azure 앱 서비스에서 커넥터 및 API 앱 관리 및 모니터링 | Microsoft Azure" 
	description="Azure 앱 서비스에서 커넥터 및 API 앱의 성능 보기, 마이크로 서비스 아키텍처" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="mandia"/>

# 기본 제공 API 앱 및 커넥터 관리 및 모니터

기본 제공 API 앱을 만들었습니다. 이제 어떻게 합니까?

Azure에서 모든 API 앱은 Azure상에 호스팅되는 별도 웹 사이트입니다. 결과적으로, 수행되는 요청의 개수와 데이터의 양을 커넥터를 통해 쉽게 확인할 수 있습니다. API 앱을 백업, 알림 생성, 은박 보안 작동, 사용자 및 역할을 추가할 수도 있습니다.

이 항목은 API 앱을 관리하는 다른 옵션 중 일부에 대해 설명합니다.

이러한 기본 제공 기능을 보려면 [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에서 API 앱을 엽니다. API 앱이 시작 보드에 포함된 경우 선택하여 속성을 엽니다. 또한 **찾아보기** 및 **API 앱**을 선택한 다음 API 앱을 선택할 수 있습니다.

![][browse]

## 입력한 속성을 참조 하십시오

API 앱을 열 때 사용할 수 있는 여러 기능과 작업은 다음과 같습니다.

![][settings]

다음을 수행할 수 있습니다.

- **설정**은 API 앱상의 구독 세부 정보를 포함한 특정 정보를 보여 주고, API 앱에 액세스할 수 있는 사용자를 나열합니다. 여러 기능중의 하나인 규모 기능을 사용하여 API 앱의 인스턴스 수를 증가시키거나 감소시킬 수도 있습니다.
- **시작** 및 **중지** 단추를 사용하여 API 앱을 제어합니다.
- API 앱에서 사용되는 기본 파일이 업데이트 되면 **업데이트**를 클릭하여 최신 버전을 가져올 수 있습니다. 예를 들어 Microsoft에서 수정 또는 보안 업데이트를 출시한 경우 **업데이트**를 클릭하면 이 수정 프로그램을 포함한 API 앱을 자동으로 업데이트합니다. 
- **변경 계획**을 선택하여 API 앱의 데이터 사용량에 따라 업그레이드하거나 다운그레이드합니다. 또한, 이 기능을 사용하여 데이터 사용 현황을 확인할 수 있습니다.
- SQL 커넥터와 같이 테이블을 포함한 커넥터를 만들 때 연결한 테이블 이름을 선택적으로 입력할 수 있습니다. 테이블 기반 스키마는 **스키마 다운로드**를 클릭할 때 자동으로 만들어지고 사용할 수 있게 됩니다. 다운로드한 스키마를 사용하여 변환 또는 지도를 만들수 있습니다. 

## 커넥터 또는 입력한 API 구성값 변경

빌드 커넥터를 구성하거나 만든 후에 입력 한 값을 변경할 수 있습니다. 예를들어 SQL 커넥터를 구성 하 고 SQL 서버 이름 또는 테이블 이름을 변경하려는 경우, 커넥터의 API 앱 블레이드에서 실행할 수 있습니다.

단계는 다음과 같습니다.

1. 커넥터 또는 API 앱을 엽니다. 이렇게 해서 API 앱 블레이드를 엽니다.
2. **Essentials**에서 호스트 속성 내의 하이퍼링크를 클릭합니다. 하이퍼링크 이름은*slackconnector* 또는 *microsoftsqlconnector123*과 같이 명명됩니다.

	![][apiapphost]

3. API 앱 호스트 블레이드에서 **설정**을 선택합니다. 설정 블레이드에서 **응용 프로그램 설정**을 선택합니다. **앱 설정**에 구성 값 목록이 있습니다.
	
	![][hostsettings]

4. 변경을 원하는 설정을 클릭하고 새 값을 입력한 다음 변경 내용을 **저장**합니다.


## 하이브리드 연결 관리자 설치 - 선택적

![][hcsetup]

하이브리드 연결 관리자는 SQL Server 또는 SAP와 같이 온-프레미스 시스템에 연결 기능을 제공합니다. 이 하이브리드 연결은 Azure 리소스와 온-프레미스 리소스 간의 보안을 연결하고 제어하기 위해 Azure 서비스 버스를 사용합니다.

[Azure 앱 서비스에서 하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)을 참조하세요.

> [AZURE.NOTE]방화벽 뒤의 온-프레미스 리소스에 연결하는 경우에만 하이브리드 연결 관리자가 필요합니다. 온-프레미스 시스템에 연결하지 않으려면, 하이브리드 연결 관리자 목록이 커넥터 블레이드에 나타나지 않을 수 있습니다.

## 성능 모니터
성능 메트릭은 기본 제공 기능이며 생성된 모든 API 앱에 포함됩니다. 이러한 메트릭은 Azure에 호스트된 API 앱에 한정됩니다. 샘플 메트릭:

![][monitoring]

다음을 수행할 수 있습니다.

- 200, 400, 또는 500 HTTP 상태 코드와 같은 일반적으로 알려진 HTTP 오류 코드를 포함하여 다양한 성능 메트릭을 추가하기 위해 **요청 및 오류**를 선택합니다 응답 시간, API 앱에 대한 요청 수 및 유입 및 유출되는 데이터 양을 참조하십시오. 성능 기준에 따라, 메트릭을 선택한 임계값을 초과할 경우 경고 전자 메일을 만들 수 있습니다. 
- **사용량**에서 API 앱이 사용하는 **CPU** 양을 확인하고 현재 **사용 할당량**(MB)을 검토하며 비용 계층에 기반한 최대 데이터 사용량을 볼 수 있습니다. **예상 지출**은 API 앱 실행의 잠재적인 비용을 결정하는 데 도움이 됩니다.
- 프로세스 탐색기를 열려면 **프로세스**를 선택합니다. 이것은 스레드 수 및 메모리 사용을 포함하여 사용자의 웹 인스턴스 및 해당 속성을 보여줍니다.

이 도구들을 사용하여, 비즈니스 요구 사항에 따라 앱 서비스 계획을 확장시킬지 혹은 축소시킬지 결정할 수 있습니다. 이 기능들은 필요없는 추가 도구와 함께 포털에서 기본 제공 됩니다.

## 보안 제어

API 앱 역할 기반 보안을 사용합니다. 이러한 역할은 API 앱과 다른 Azure 리소스를 포함하여 전체 Azure 환경에 적용됩니다. 역할은 다음을 포함합니다.

역할 | 설명
--- | ---
소유자 | 관리환경에 대해 전체적으로 액세스 되고 다른 사용자나 그룹에 접속 권한을 줄 수 있습니다.
참여자 | 관리 환경에 대한 모든 권한을 가집니다. 다른 사용자 또는 그룹에게 접속 권한을 줄 수 없습니다.
판독기 | 암호를 제외한 모든 리소스를 볼 수 있습니다.
사용자 액세스 관리자 | 모든 리소스를 볼 수 있고, 역할 및 지원 티켓을 생성 및 관리할수 있습니다.

[Microsoft Azure 포털에서의 역할 기반 액세스 제어](role-based-access-control-configure.md)를 참조하세요.

사용자를 쉽게 추가하고 API 앱에 특정 역할을 할당할 수 있습니다. 포털에서는 사용자의 권한과 할당된 역할을 보여줍니다.

![][access]

- **사용자**를 선택하여 사용자를 추가하고, 역할을 할당하고, 사용자를 제거합니다.
- **역할**을 선택하여 특정 역할의 모든 사용자를 확인하고, 역할에 사용자를 추가하고, 역할에서 사용자를 제거합니다. 


## 더 좋은 정보
- **API 정의**를 선택하여 특정 API 앱에 대해 자동으로 만들어진 Swagger 파일을 엽니다.
- **종속성**을 선택하여 API 앱이 요구하는 파일을 봅니다. 예를 들어, SAP 커넥터를 사용하는 경우 일부 추가 파일을 온-프레미스 하이브리드 연결 관리자에서 설치 합니다. 이러한 종속성은 API 앱 블레이드에서 보여 줍니다. 

> [AZURE.IMPORTANT]API 앱 속성을 열면 **Essentials** 아래에 새 블레이드를 여는 **호스트** 및 **게이트웨이** 링크가 있습니다.
> 
> ![][host]
> 
> 이 속성은 API 앱을 호스팅하는 웹사이트에 한정됩니다. 기본 제공 API 앱이나 커넥터를 사용할 때 대부분의 속성들이 실제로 적용되지 않기 때문에 이 속성들을 업데이트하는 것을 권장하지는 않습니다. 비주얼 스튜디오에서 API 앱을 생성하거나 Azure 구독을 배포할 때, 호스트와 게이트웨이 블레이드를 사용할 수 있습니다. [API 앱 관리](../app-service-api/app-service-api-manage-in-portal.md)는 사용자가 직접 만든 앱과 함께 이 블레이드에서 수행할 수 있는 더 많은 정보를 제공합니다.


>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드 및 약정은 필요하지 않습니다.

## 자세히 알아보기

[논리 앱 모니터링](app-service-logic-monitor-your-logic-apps.md)<br/>[ 앱 서비스의 커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)<br/>[Microsoft Azure 포털에서 역할 기반 액세스 제어](role-based-access-control-configure.md)<br/>[Azure 앱 서비스에서 하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_1210_2015-->