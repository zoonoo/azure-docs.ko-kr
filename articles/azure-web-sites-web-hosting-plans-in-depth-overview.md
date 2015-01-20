<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure 웹 사이트 웹 호스팅 계획의 포괄 개요-Microsoft Azure 기능 가이드" description="Azure 웹 사이트에 대한 웹 호스팅 계획의 작동 방식 및 이러한 계획을 통해 관리 경험을 향상시킬 수 있는 방법에 대해 알아봅니다." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="Byron Tardif and Yochay Kiryaty" />
</br>
#Azure 웹 사이트 웹 호스팅 계획의 포괄 개요
</br>
WHP(웹 호스팅 계획)는 웹 사이트를 통해 공유할 수 있는 기능 및 용량의 집합을 나타냅니다.  웹 호스팅 계획은 각 계층에 고유한 기능 및 용량이 있는 네 개의 Azure 웹 사이트 가격 계층(예: 무료, 공유, 기본 및 표준)을 지원합니다.  동일한 구독, 리소스 그룹 및 지리적 위치에 있는 사이트는 웹 호스팅 계획을 공유할 수 있습니다. 웹 호스팅 계획을 공유하는 모든 사이트는 웹 호스팅 계획 계층에서 정의된 모든 기능 및 특징을 활용할 수 있습니다. 주어진 웹 호스팅 계획과 관련된 모든 웹 사이트는 웹 호스팅 계획에서 정의된 리소스에서 실행됩니다. 예를 들어 두 개의 "소형" 가상 컴퓨터를 사용하도록 웹 호스팅 계획이 구성된 경우 이 웹 호스팅 계획과 관련된 모든 사이트는 두 가상 컴퓨터 모두에서 실행됩니다. Azure 웹 사이트의 경우처럼 사이트가 실행되는 가상 컴퓨터는 완전히 관리되며 가용성이 뛰어납니다.
</br>
이 문서에서는 웹 호스팅 계획의 계층 및 크기와 웹 사이트를 관리하는 동안 웹 호스팅 계획이 진행되는 방식과 같은 주요 특징을 살펴봅니다. 
</br>
##웹 사이트, 웹 호스팅 계획 및 리소스 그룹
</br>
하나의 웹 사이트는 항상 하나의 웹 호스팅 계획에만 연결할 수 있습니다. 웹 호스팅 계획은 리소스 그룹과 연결됩니다. 리소스 그룹은 포함된 모든 리소스를 위한 수명 주기 경계 역할을 하는 새로운 Azure 개념입니다. 리소스 그룹을 사용하면 응용 프로그램의 모든 부분을 함께 관리할 수 있습니다. 
</br>
하나의 리소스 그룹에 여러 웹 호스팅 계획이 있을 수 있고 각 호스팅 계획에는 관련 사이트에서 사용하는 자체 특징 및 기능 집합이 있습니다.  다음 그림에서는 이러한 관계를 보여 줍니다.
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
단일 리소스 그룹에 여러 웹 호스팅 계획을 포함할 수 있으므로 리소스(주로 웹 사이트를 실행하는 가상 컴퓨터)마다 다른 사이트를 할당할 수 있습니다. 예를 들어 이러한 방식이 가능하므로 개발, 테스트 및 프로덕션 사이트 간에 리소스를 구분할 수 있으며, 프로덕션 사이트를 위해 전용 리소스 집합이 있는 하나의 웹 호스팅 계획을 할당하고, 개발 및 테스트 사이트를 위한 또 다른 웹 호스팅 계획을 할당할 수 있습니다. 
</br>
또한 단일 리소스 그룹에 여러 웹 호스팅 계획을 포함하여 여러 지역에 걸쳐 있는 응용 프로그램을 정의할 수 있습니다. 예를 들어 두 개의 지역에서 실행되는 고가용성 웹 사이트에는 각 지역별 웹 호스팅 계획, 각 웹 호스팅 계획과 연결된 웹 사이트 등 두 개의 웹 호스팅 계획이 포함됩니다. 이런 경우 모든 사이트가 하나의 응용 프로그램을 정의하는 단일 리소스 그룹에 연결됩니다.  여러 웹 호스팅 계획 및 여러 사이트와 연결된 리소스 그룹을 하나의 보기에서 보면서 쉽게 웹 사이트의 상태를 관리, 제어하고 볼 수 있습니다. 웹 사이트 리소스 및 주어진 응용 프로그램의 개별 사이트를 관리하는 것 외에도, SQL-Azure 데이터베이스와 같은 관련 Azure 리소스를 팀 프로젝트와 연결할 수 있습니다. 
</br>
##새 리소스 그룹을 만들어야 하는 경우 및 새 웹 호스팅 계획을 만들어야 하는 경우##
</br>
새 웹 사이트를 만들 때는 만들려는 웹 사이트가 새 웹 응용 프로그램을 나타내는 경우 새 리소스 그룹을 만들어야 합니다. 이 경우 새 리소스 그룹, 관련 웹 호스팅 계획 및 웹 사이트를 만드는 것이 적절한 방법입니다.  갤러리 또는 새 웹 사이트 + SQL 옵션을 사용하여 새 Azure 포털 미리 보기를 통해 새 웹 사이트를 만들면 포털은 기본적으로 새 사이트에 대한 새 리소스 그룹 및 웹 호스팅 계획을 만듭니다. 그러나 필요한 경우 이러한 기본값을 덮어쓸 수 있습니다.
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
항상 기존 리소스 그룹에 새 웹 사이트 또는 기타 리소스를 추가할 수 있습니다. 기존 리소스 그룹의 컨텍스트에서 새 웹 사이트를 만들 때 새 웹 사이트 마법사는 기본적으로 기존 리소스 및 웹 호스팅 계획을 사용합니다. 이 경우에도 필요에 따라 기본값을 덮어쓸 수 있습니다. 기존 리소스 그룹에 새 웹 사이트를 추가할 때는 기존 웹 호스팅 계획에 사이트를 추가하도록 선택하거나(새 Azure 포털 미리 보기의 기본 옵션) 사이트를 추가할 새 웹 호스팅 계획을 만들 수 있습니다.
</br>
새 호스팅 계획을 만들면 웹 사이트에 대해 새 리소스 집합을 할당할 수 있으며, 각 웹 호스팅 계획이 자체의 가상 컴퓨터 집합을 얻게 되므로 리소스 할당을 보다 강력하게 제어할 수 있습니다. 웹 호스팅 계획이 동일한 지역에 있다고 가정하는 경우 웹 호스팅 계획 간에 웹 사이트를 이동할 수 있으므로, 새 웹 호스팅 계획을 만들지 여부를 결정하는 일은 중요성이 비교적 낮습니다. 지정된 웹 사이트가 너무 많은 리소스를 소비하기 시작하거나 소수의 웹 사이트를 구분해야 하는 경우 새 웹 호스팅 계획을 만들고 웹 사이트를 이동할 수 있습니다.
</br>
다른 지역에 새 웹 사이트를 만들려고 하며 해당 지역에 기존 웹 호스팅 계획이 없으면 웹 사이트를 연결하기 위해 해당 지역에 새 웹 호스팅 계획을 만들어야 합니다. 
</br>
리소스 그룹 간에 웹 호스팅 계획이나 웹 사이트를 이동할 수 없다는 점에 유의해야 합니다. 또한 두 개의 개별 지역에 있는 두 개의 웹 호스팅 계획 간에 웹 사이트를 이동할 수도 없습니다. 
</br>
##Azure 미리 보기 포털의 기존 리소스 그룹##
</br>
Azure 웹 사이트에 이미 기존 웹 사이트가 있는 경우 모든 웹 사이트가 Azure 미리 보기 포털에 표시됩니다. 왼쪽 탐색 창에서 **찾아보기** 단추를 클릭하고 **웹 사이트**를 선택하여 모든 웹 사이트를 단순 목록으로 확인할 수 있습니다.
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
또한 왼쪽 탐색 창에서 **찾아보기** 단추를 클릭하고 **리소스 그룹**을 선택하여 사용자에 대해 생성된 모든 리소스 그룹을 확인할 수 있습니다.
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
이미 웹 사이트가 있는 각 지역에 자동으로 생성된 기본 리소스 그룹이 있다는 것도 알 수 있을 것입니다. 웹 사이트의 자동 생성 리소스 그룹 이름은 *Default-Web-<LOCATION NAME>*입니다. 여기서 위치 이름은 Azure 지역을 나타냅니다(예: *Default-Web-WestUS*). 각 리소스 그룹에 그룹의 지역에 대한 모든 기존 사이트가 있습니다. 전체 Azure 포털 또는 Azure 미리 보기 포털에서 이전에 만들었거나 앞으로 만들 각 사이트는 두 포털에서 사용할 수 있습니다. 
</br>
모든 웹 사이트는 웹 호스팅 계획과 연결되어야 하므로 각 지역에서 다음 규칙에 따라 기존 사이트에 대해 기본 웹 호스팅 계획을 만들었습니다.
</br>
* 모든 **무료** 웹 사이트는 **기본** 웹 호스팅 계획과 연결되며 해당 가격 계층이 **무료**로 설정됩니다. 
</br>
* 모든 **공유** 웹 사이트는 **기본** 웹 호스팅 계획과 연결되며 해당 가격 계층이 **공유**로 설정됩니다.
</br>
* 모든 **표준** 웹 사이트는 기본 웹 호스팅 계획과 연결되며 해당 가격 계층이 **표준**으로 설정됩니다. 
</br>
이 웹 호스팅 계획의 이름은 **DefaultServerFarm**입니다. 이 이름은 레거시 API를 지원하기 위해 선택되었습니다. 이름 **ServerFarm**은 **웹 호스팅 계획**을 나타내므로 약간 오해를 일으킬 수도 있지만 웹 호스팅 계획의 이름이며 그 자체로 엔터티는 아니라는 점을 기억해야 합니다. 
</br>
##웹 호스팅 계획 FAQ##
</br>
**질문**: 웹 호스팅 계획을 만들려면 어떻게 하나요?
</br>
**대답**: 웹 호스팅 계획은 컨테이너이며, 빈 웹 호스팅 계획을 만들 수는 없습니다. 그러나 새 웹 호스팅 계획은 명시적으로 사이트를 만드는 동안 만들어집니다.
</br>
새 **Azure 포털 미리 보기**에서 UI를 사용하여 이 작업을 수행하려면 **새로 만들기**를 클릭하고 **웹 사이트**를 선택합니다. 그러면 웹 사이트 만들기 블레이드가 열립니다. 아래 첫 번째 이미지의 왼쪽 하단에서 **새로 만들기** 아이콘을 볼 수 있으며, 두 번째 이미지에서는 **웹 사이트** 만들기 블레이드, **웹 호스팅 계획** 블레이드 및 **가격 책정 계층** 블레이드를 볼 수 있습니다.
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
이 예에서는 **contosomarketing**이라는 새 웹 사이트를 만들고 **contoso**라는 새 웹 호스팅 계획에 배치하도록 선택할 것입니다. 이 웹 호스팅 계획에서  가격 책정 계층은 **소형 표준**입니다. 웹 호스팅 계획 가격 책정 계층과 각 계층에 제공되는 기능, 가격 및 크기 옵션에 대한 자세한 내용은 (http://go.microsoft.com/?linkid=9845586)[Azure 웹 사이트 웹 호스팅 계획 사양]을 참조하세요. 
</br>
또한 기존 Azure 포털에서 웹 호스팅 계획을 만들 수도 있습니다. 이 작업은 **웹 호스팅 계획** 드롭다운에서 **새 웹 호스팅 계획 만들기**를 선택하여 **빠른 생성** 마법사의 일부로 수행됩니다.
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
이 예제에서는 **northwind**라는 새 사이트를 만들고 새 웹 호스팅 계획을 만들도록 선택할 것입니다. 이 작업의 결과로 **northwind** 웹 사이트가 포함된 **default0**이라는 새 웹 호스팅 계획이 만들어집니다. 이 작업을 통해 만들어지는 모든 웹 호스팅 계획은 이 명명 규칙을 따르며, 생성된 웹 호스팅 계획은 이름을 바꿀 수 없습니다. 또한 이 프로세스를 통해 만들어진 웹 호스팅 계획은 **무료** 가격 계층으로 만들어집니다.
</br>
**질문**: **웹 호스팅 계획**에 사이트를 할당하려면 어떻게 하나요?
</br>
**대답**: 사이트는 사이트 생성 프로세스의 일부로 웹 호스팅 계획에 할당됩니다. 새 **Azure 포털 미리 보기**에서 UI를 사용하여 이 작업을 수행하려면 **새로 만들기**를 클릭하고 **웹 사이트**를 선택합니다.
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
그런 다음 웹 사이트 만들기 블레이드에서 호스팅 계획을 선택합니다.
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
기존 Azure 포털을 사용하여 사이트를 특정 웹 호스팅 계획으로 만들 수도 있습니다. 이 작업은 **빠른 생성** 마법사의 일부로 진행됩니다. 웹 사이트 URL을 입력한 후 **웹 호스팅 계획** 드롭다운을 사용하여 사이트를 추가할 계획을 선택합니다.
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**질문**: 다른 웹 호스팅 계획으로 사이트를 이동하려면 어떻게 하나요?
</br>
**대답**: Azure 미리 보기 포털을 사용하여 다른 웹 호스팅 계획으로 사이트를 이동할 수 있습니다. 동일한 리소스 그룹에 속해 있는 동일한 지리적 지역의 웹 호스팅 계획 간에는 웹 사이트를 이동할 수 있습니다.
</br>
사이트를 다른 계획으로 이동하려면 이동하려는 사이트의 웹 사이트 블레이드로 이동합니다.  그런 다음 **웹 호스팅 계획**을 클릭합니다.
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
이렇게 하면 웹 호스팅 계획 블레이드가 열립니다. 이때 기존 웹 호스팅 계획을 선택하거나 새 계획을 만들 수 있습니다. 다른 지리적 위치 또는 리소스 그룹의 계획은 회색으로 표시되어 선택할 수 없습니다.
</br>
각 웹 호스팅 계획에는 자체 가격 책정 계층에 있습니다. **무료** 계층 웹 호스팅 계획에서 **표준** 웹 호스팅 계획으로 사이트를 이동하는 경우 웹 사이트는 표준 계층의 모든 기능과 리소스를 활용할 수 있습니다. 
</br>
</br>
**질문**: 웹 호스팅 계획의 크기를 조정하려면 어떻게 하나요?
</br>
**대답**: 웹 호스팅 계획의 크기를 조정하는 두 가지 방법이 있습니다. 한 가지는 웹 호스팅 계획 및 해당 웹 호스팅 계획과 연결된 모든 사이트를 강화하는 것입니다. 웹 호스팅 계획의 가격 책정 계층을 변경하면 해당 웹 호스팅 계획에 있는 모든 사이트에는 해당 가격 책정 계층에 따라 정의된 기능 및 리소스가 적용됩니다. 
</br>
아래 이미지에서는 **웹 호스팅 계획** 블레이드 및 **가격 책정 계층** 블레이드를 볼 수 있습니다. **웹 호스팅 계획** 블레이드에서 **가격 책정 계층** 파트를 클릭하면 웹 호스팅 계획의 가격 책정 계층을 변경할 수 있는 **가격 책정 계층** 블레이드가 확장됩니다.
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
계획의 크기를 조정하는 두 번째 방법은 웹 호스팅 계획에 있는 인스턴스의 수를 늘려 확장하는 것입니다. 아래 이미지에서는 **웹 호스팅 계획** 블레이드 및 **크기 조정** 블레이드를 볼 수 있습니다. **웹 호스팅 계획** 블레이드에서 크기 영역을 클릭하면 웹 호스팅 계획이 확장되고 계획의 인스턴스 수를 변경할 수 있습니다.
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
위 이미지의 웹 호스팅 계획은 **표준** 가격 책정 계층을 사용하도록 구성되므로 **자동 크기 조정** 옵션을 사용할 수 있습니다. 
</br>
아래 그림과 같이 전체 Azure 포털에서는 **크기 조정** 탭에서 이 작업을 수행할 수 있습니다.
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**질문**: 웹 호스팅 계획을 삭제하려면 어떻게 하나요?
</br>
**대답**: 웹 호스팅 계획을 삭제하려면 먼저 연결된 모든 웹 사이트를 삭제해야 합니다. 웹 호스팅 계획의 모든 웹 사이트를 삭제하면 웹 호스팅 계획 블레이드에서 웹 호스팅 계획을 삭제할 수 있습니다.
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
전체 Azure 포털에서 웹 호스팅 계획의 마지막 웹 사이트를 삭제하면 연결된 웹 호스팅 계획이 자동으로 삭제됩니다.
</br>
**질문**: 웹 호스팅 계획을 모니터링하려면 어떻게 하나요?
</br>
**대답**: 웹 호스팅 계획은 웹 호스팅 계획 블레이드의 모니터링 파트에서 모니터링할 수 있습니다.
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
모니터링 컨트롤을 마우스 오른쪽 단추로 클릭하고 **쿼리 편집**을 선택하여 모니터링 컨트롤을 사용자 지정할 수 있습니다.
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
공개되는 메트릭은 다음과 같습니다.
</br>
* CPU 비율
</br>
* 메모리 비율
</br>
* 디스크 큐 길이 
</br>
* HTTP 큐 길이. 
</br>
이 메트릭은 웹 호스팅 계획에 속한 인스턴스에서 발생하는 평균 사용량을 나타냅니다. 이러한 모든 메트릭은 알림 및 자동 크기 조정 규칙을 설정하는 데 사용할 수 있습니다.
</br>
##정리 및 결론##
</br>
웹 호스팅 계획은 웹 사이트를 통해 공유할 수 있는 기능 및 용량의 집합을 나타냅니다.  웹 호스팅 계획을 사용하면 융통성 있게 특정 리소스 집합, 즉 가상 컴퓨터에 특정 사이트를 할당하고 Azure 리소스 할당 및 웹 사이트 사용을 추가로 최적화할 수 있습니다.

<!--HONumber=35.2-->
