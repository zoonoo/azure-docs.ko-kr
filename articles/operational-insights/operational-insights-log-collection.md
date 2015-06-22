<properties 
   pageTitle="로그 관리를 사용하는 방법" 
   description="Microsoft Azure Operational Insights의 로그 관리를 사용하여 모니터링된 서버에서 수집된 이벤트 로그를 볼 수 있습니다." 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights" 
   ms.date="03/20/2015"
   ms.author="banders"/>

# 로그 관리 사용 

Microsoft Azure Operational Insights에서 로그 관리를 사용하려면 먼저 인텔리전스 팩이 설치되어 있어야 합니다. 기본적으로 이 팩은 아무 것도 구성하고 수집하지 않으므로, 모든 새 작업 영역에서 기본적으로 활성화됩니다. 제거된 경우, 다시 인텔리전스 팩 갤러리에서 추가할 수 있습니다. [갤러리를 사용하여 인텔리전스 팩 추가 또는 제거 ](../operational-insights-add-intelligence-packs.md) 를 참조하세요. 

새 로그를 추가하여 이벤트를 수집하고 로그에 대해 수집하려는 이벤트 수준 또는 심각도를 선택할 수 있습니다.
로그 관리를 구성한 후 수집 정책에 적용되며 이벤트 수집을 시작합니다.
Operational Insights의 **개요** 페이지의 **로그 관리** 타일을 사용하여 모니터링된 서버에서 수집된 로그 이벤트의 일부 초기 분석에 액세스할 수 있습니다. 

![로그 관리 타일의 이미지](./media/operational-insights-log-collection/overview-log-mgt.png)

타일은 로그에 포함된 이벤트에 대한 요약을 볼 수 있는 **로그 관리** 페이지를 엽니다.
다른 데이터 유형의 경우와 같이 직접 검색할 수도 있습니다.


페이지는 다음 범주를 자세히 설명합니다.

- Windows 이벤트 로그
- IIS 로그
- 사용자가 추가한 다른 로그

![로그 관리 대시보드의 이미지](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![로그 관리 대시보드의 이미지](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## IIS 로그 파일 형식

현재 지원되는 유일한 IIS 로그 형식은 W3C입니다. 걱정하지 마세요. 가장 일반적인 형식이며 IIS 7 및 IIS 8에서 기본 형식입니다. 따라서 NCSA 또는 IIS의 네이티브 형식으로 기록하는 경우 Operational Insights는 이 로그를 전혀 선택하지 않습니다. W3C 형식에서도 기본적으로 모든 필드는 기록되지 않습니다. [로그할 W3C 필드 선택(IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx).에서 형식에 대해 더 알 수 있습니다. 


> [AZURE 팁] 검색 환경을 최적화하려면 IIS에서 **로깅**을 사용하여 .각 웹사이트에 대한 모든 로깅 필드를 선택하는 것이 좋습니다. **로그 파일 롤오버** 일정을 변경하여 새 로그를 **시간별**로 설정하여, 작은 파일들이 클라우드에 업로드되어 대역폭을 절약하는 것도 좋습니다.


## Operations Manager 또는 직접 연결하는 에이전트에서 Windows 이벤트 로그를 수집합니다.

1. **개요** 페이지에서 **로그 관리** 타일을 클릭합니다. 

2. **로그 관리** 대시보드에서 **구성** 타일을 클릭합니다.
 
3. 정보를 수집하려는 이벤트 로그의 이름을 입력합니다. 사용할 이름을 모를 경우, Windows 이벤트 로그의 속성을 **이벤트 뷰어**에서 선택하고, **FullName** 필드에서 이름을 복사하여 **다음 이벤트 로그에서 이벤트를 수집** 상자에 붙여넣습니다.

4. **+**를 클릭하여 로그를 추가합니다.

5. 로그에 대해 수집 하려는 이벤트 수준 또는 심각도를 선택합니다. **감사 성공** 및 **감사 실패** 이벤트가 이 릴리스에서 지원되지 않습니다.

6. 정보를 수집하려는 각 로그에 대해 이전 단계를 반복한 다음 **저장**을 클릭합니다.

7. 이벤트는 잠시 후에 Operational Insights에 표시되어야 데이터를 검색할 수 있습니다. 



## Operations Manager 또는 직접 연결하는 에이전트에서 IIS 로그를 수집하려면

1. **개요** 페이지에서 **로그 관리** 타일을 클릭합니다. 

2. **로그 관리** 대시보드에서 **구성** 타일을 클릭합니다.
 
3. **IIS 로그**에서 **Operations Manager에서 로그 수집**을 선택합니다.


## Azure 진단에서 IIS 로그 및/또는 Windows 이벤트를 수집합니다.
이 작업 영역에서 Operational Insights이 아닌 Azure 관리 포털에서 구성할 수 있으며, **저장소** 탭으로 이동하고 해당 저장소 계정에서 로그 수집을 활성화할 수 있습니다.


## 로그 파일 작업
 
1. **개요** 페이지에서 **로그 관리** 타일을 클릭합니다.

2. **로그 관리** 대시보드에서 이벤트 로그를 보고 작업할 로그를 선택합니다.
  
3. **검색** 페이지에서 항목에 대한 자세한 내용을 보려면 해당 항목을 클릭합니다.

4. 초기 결과에서 추가 검색을 수행한 다음 로그를 분석하고 상관관계를 지정합니다.

 
# 추가 리소스
- [Azure Operational Insights의 IIS 로그 형식 요구 사항](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- [피드백 포럼](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy) 에서 커뮤니티에서 구현하도록 요청하는 로그 유형과 기타 데이터 소스가 무엇인지 참조하세요.


<!--HONumber=52--> 