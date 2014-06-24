# Azure 문제 해결

*문제 해결*은 예기치 않은 응용 프로그램 동작을 찾아 이해하고 수정하는 일반적인 작업에 대해 언급합니다. 응용 프로그램을 개발할
때 개발자는 응용 프로그램을 프로덕션 환경에 배포하기 전에 테스트, 실행 및 디버그합니다. 이는 응용 프로그램이 클라우드의
데스크톱 컴퓨터에서 실행되든 서버에서 실행되든 상관없습니다. 하지만 확장하도록 설계되었으며 넓게 분산되는 다중 인스턴스 응용
프로그램은 보다 많은 표준 도구 및 접근 방법이 필요하므로 디버깅하기 어려울 수 있습니다.

이러한 이유로 클라우드 응용 프로그램은 문제 해결을 염두에 두고 설계되어야 합니다. 응용 프로그램의 문제 해결 지원을 설계하는
방법은 우선 응용 프로그램이 실행되는 위치, 그 다음 응용 프로그램을 구축하거나 응용 프로그램이 사용하는 언어 또는 런타임에 따라
달라집니다.

Azure 가상 컴퓨터에서 실행하는 응용 프로그램을 빌드하는 경우, 고유의 서버에서 실행 중인 운영 체제에서처럼 대부분의 경우
디버깅뿐만 아니라 문제 해결 설계도 접근할 수 있습니다.

Azure에서 실행하는 응용 프로그램은 디버그하기 어려울 수 있는 폭넓게 분산되는 다중 인스턴스 응용 프로그램입니다. 이러한
종류의 응용 프로그램은 문제 해결에 보다 많은 표준 도구 및 접근 방법이 필요합니다. 이 항목은 일부 검증된 문제 해결 사례에
대해 논의하고 설명한 사례의 자세한 정보에 대한 링크가 포함되어 있습니다.

**참고**: 이 항목은 사용자가 응용 프로그램을 디자인하거나 Azure 응용 프로그램을 성공적으로 배포했으나 예기치 않은
문제가 발생한다고 가정합니다. Azure에 응용 프로그램을 배포하는 방법은 설명하지 않습니다. Azure 응용 프로그램 개발 및
배포에 대한 자세한 내용은
[https://www.windowsazure.com/en-us/develop/overview/][1](영문)를 참조하십시오.

이 항목에서는 문제가 발생할 경우 효과적으로 문제를 해결할 수 있도록 우선 응용 프로그램을 설계하는 데 도움이 되는 일부 모범
사례에 대해 설명합니다. 코드 흐름을 따를 수 있도록 사전에 응용 프로그램을 설계하지 않으면 문제가 발생할 때 문제를 찾기 아주
어려울 수 있습니다. 이러한 모범 사례는 사용된 응용 프로그램 모델이나 언어와 상관없이 Azure에서 실행되는 모든 종류의 응용
프로그램에 적합합니다.

다음 섹션에서는 응용 프로그램의 유형과 상관없이 지원 가능한 Azure 응용 프로그램을 개발하기 위한 특정 접근 방법에 대해
설명합니다. 여기서 웹 사이트, 클라우드 서비스 또는 가상 컴퓨터와 같은 응용 프로그램 종류는 상관없습니다.

각 섹션은 높은 수준의 모범 사례를 설명하고, 모범 사례를 자세히 보여 주거나 구현 방법을 설명하는 리소스에 대한 포인터가
포함되어 있습니다.
## 문서 내용

* [Azure 문제 해결의 모범 사례](#BestPractices)
* [Azure 웹 사이트](#Websites)
* [Azure 클라우드 서비스](#CloudServices)
* [Azure 가상 컴퓨터](#Vms)
* [Azure 서비스](#PlatformServices)
* [SQL 데이터베이스 문제 해결](#SQLTroubleshooting)

<h2><a  id="BestPractices" ></a>Azure 문제 해결의 모범 사례</h2>


이 섹션에서는 사용하는 호스팅 모델이나 언어와 상관없이 Azure 응용 프로그램에 적용되는 모범 사례를 설명합니다. 여기에는
이러한 사례의 보다 심층적인 논의를 위한 리소스가 포함되어 있습니다.

Azure에서 효율적으로 문제를 해결할 수 있는 기반을 만들려면 다음 세 가지 주요 영역에 집중하십시오.

* 적절한 오류 처리 - *각 구성 요소 서비스는 종속 서비스 또는 인프라의 오류를 견딜 수 있어야 합니다.*
* 추적, 로깅 및 모니터링 - *각 구성 요소 서비스에는 적절한 디버깅, 추적, 이벤트 및 오류 로깅이 있어야 합니다.*
* 가능한 경우 오류 디버그 - *프로덕션 수준을 올리기 전 및 실행 시 구성 요소 및 네트워크 수준에서도 디버그할 수 있어야
  합니다.*

이런 개념을 염두에 두고 응용 프로그램을 설계하면 응용 프로그램에서 예기치 않은 동작이 발생할 경우 그 동작을 추적하는 데 필요한
정보를 제공합니다.
### 적절하게 오류를 처리하도록 설계

응용 프로그램에서는 가능한 경우 오류 상태를 적절하게 처리해야 합니다. 이는 분산되는 Azure의 특성을 고려할 때 더욱
중요합니다. 일시적인 오류를 잘 처리하도록 설계하면 효과적으로 문제를 해결할 수 있습니다. 인터넷 응용 프로그램에서는 항상
일시적인 오류 상태가 따르므로 일시적인 오류는 클라우드 응용 프로그램에서 예상대로 동작하지 못하는 주요 영역 중 하나입니다.

일시적인 오류는 인터넷의 공유 리소스에 고유한 대기 시간 및 일시적 네트워크 연결과 관련된 오류입니다. 일부 사례:

* 예를 들어 Azure 클라우드 서비스 및 SQL 데이터베이스와 같은 공유 컴퓨터 리소스의 응답성이 순식간에 약간 커지거나 줄
  수 있습니다.
* 서비스에 영속성을 제공하기 때문에 응답이 지연됩니다. 예를 들어 SQL Azure는 영속성을 제공하기 위해 여러 개의
  데이터베이스 복사본을 일관성 있게 유지하며, 이는 응답에 큰 영향을 줍니다.
* 작업 완료 이전에 연결이 끝나는 HTTP 또는 기타 프로토콜로 인한 지연. 예를 들어 HTTP 요청이 끝점에 도달하지 않고
  제한 시간 이전에 반환되지 않을 수도 있습니다.

일시적 오류의 영향을 줄이기 위해 Azure 응용 프로그램에서는 다음을 수행해야 합니다.

* 구성 요소가 온-프레미스 환경에서보다 더 자주 실패하는 서비스에 로컬로 종속되지 않도록 느슨하게 결합합니다.
* 프로세스가 즉각적인 응답에 독립적으로 작동하도록 가능한 경우 언제나 비동기 호출을 만듭니다.
* 특정 범주의 오류를 검색하고 구성 가능한 일부 재시도 정책을 기준으로 그러한 오류에 대해 재시도 동작을 구현할 수 있는
  일시적인 오류 처리 방법을 사용합니다.

서비스에 대한 호출은 일반 오류 시나리오를 검색하고 구성 설정에 따라 호출을 다시 시도하기 위해 일시적 오류 처리 계층을
빌드하거나 사용해야 합니다. .NET 개발자의 경우 권장할 만한 라이브러리는 [Azure용 Microsoft Enterprise
Library 5.0 통합 팩][2](영문)입니다. Microsoft Enterprise Library는 엔터프라이즈 소프트웨어
개발에서 공통 기능(Cross-cutting concerns)을 지정하는 다시 사용 가능한 응용 프로그램 블록을 모아 놓은
것입니다. Azure용 Microsoft Enterprise Library 통합 팩은 Azure와 함께 사용할 수 있는
Microsoft Enterprise Library 5.0에 대한 확장입니다. 자동 크기 조정 응용 프로그램 블록, 일시적 오류
처리 응용 프로그램 블록, Blob 구성 원본, 보호된 구성 공급자 및 교육 자료가 포함되어 있습니다. 기능이 적은 단순한
.NET 라이브러리는 [클라우드 응용 프로그램 프레임워크 및 확장(CloudFx)][3](영문)입니다. CloudFx는 다양한
기능을 갖추고 안정적이며 확장 가능한 Azure 기반 솔루션 및 서비스를 구현하도록 하기 위한 프로덕션 품질 구성 요소 및 빌드
블록 집합을 제공합니다.
### 적합한 추적 및 로깅 수행

분산되고 확장 가능한 응용 프로그램은 복잡하기 때문에 한 프로세스에 대해서만 작업하는 일반적인 디버거는 응용 프로그램 실행 중에
발생하는 문제를 조사할 때 별로 유용하지 않습니다. 따라서 추적 및 로깅이 가장 중요합니다. 앱과 그 데이터를 실행하면 많은
서비스에서 공유되어 다른 많은 컴퓨터에 호스트됩니다. 규모가 큰 분산 응용 프로그램에서 연결할 서비스 인스턴스를 결정할 수 없거나
디버그할 수 없으면 어렵습니다. 추적 및 로깅을 사용하면 응용 프로그램의 상태를 좀더 이해할 수 있는 응용 프로그램 실행 및
데이터 흐름을 따라갈 수 있습니다.

성공적인 Azure 응용 프로그램은 로깅 및 추적 전략이 처음부터 응용 프로그램에 설계되어 있습니다. 이는 Microsoft에
지원을 요청하지 않고도 신속하게 문제를 찾아 복구하는 데 필요한 시간과 노력을 줄입니다.

**참고**: 추적 및 로깅을 광범위하게 기록하면 성능에 영향을 미치며, 철저하게 기록하면 더욱 더 성능에 영향을 미치게
됩니다. 따라서 응용 프로그램 설계는 필요한 경우 수정할 수 있는 구성 가능한 추적 및 로깅 정책을 포함해야 합니다. 원칙적으로
로깅 수준은 **ServiceConfiguration.cscfg** 파일에서 수정되어야 다시 배포할 필요 없이 변경할 수
있습니다.

로그 양이 많다고 해서 빠른 버그 검색 및 복구가 보장되지는 않습니다. 많은 양의 데이터는 판독하는 데 오랜 시간이 걸리고 수집할
때 응용 프로그램의 성능에 영향을 미칩니다. 수정 가능한 로깅은 로그 데이터의 크기와 저장 비용을 둘 다 제어합니다.

[Azure에서 로깅 및 추적 관리][4](영문) MSDN Magazine 문서에서 저자는 고려할 4가지 유형의 진단 출력에 대해
설명합니다.

* 디버그 출력 - 디버그 빌드에서만 가능하며 어설션 포함
* 추적 - 실행되는 동안 제어 흐름 추적
* 이벤트 로깅 - 프로그램 실행의 주요 이벤트
* 오류 로깅 - 예외 또는 위험한 상황

다른 언어, 응용 프로그램 플랫폼 및 운용 체제에서는 다른 용어로 추적하고 로깅할 수도 있습니다. Azure에서 이러한 개발
플랫폼 중 하나를 사용하고 있으면 사용 중인 언어나 플랫폼에 대해 동등한 전략 및 도구를 사용하십시오.

혼합 모드 응용 프로그램은 Azure 가상 컴퓨터, 웹 사이트 및 클라우드 서비스를 혼합하여 실행하는 응용 프로그램입니다. 이런
유형의 응용 프로그램을 빌드할 때는 넓게 분산되므로 추적 및 로깅이 더욱 중요해집니다. 이러한 혼합 모드 응용 프로그램의 문제를
해결하려면 문제를 식별하기 위해 전체 데이터 및 실행 흐름이 반드시 뒤따라야 합니다. 혼합 모드 응용 프로그램을 추적 및 로깅하는
방법은 구성 요소의 호스팅 모델에 따라 다릅니다.
### 응용 프로그램 모니터링

추적과 로깅은 더 큰 영역을 모니터링하는 데 알맞습니다. 모니터링을 통해 다음을 수행할 수 있습니다.

* Discover Azure applications
* 각 역할 인스턴스의 상태 확인
* 대기 시간 및 처리량을 포함한 성능 정보의 수집 및 모니터링
* 이벤트 수집 및 모니터링
* 추적 메시지 수집 및 모니터링
* 리소스 사용 모니터링
* 서비스 품질 기준 모니터링
* 용량 계획 수행
* 트래픽 분석 수행(사용자, 뷰, 피크 시간)
* 청구 비용 추정
* 감사 수행

모니터링은 도구나 도구 집합을 통해 수행됩니다. 사용하는 도구는 응용 프로그램이 사용하는 플랫폼 및/또는 언어에 따라 다르며,
모니터링 목적 및 요구 사항에 따라 다릅니다.

**Azure 응용 프로그램용 Microsoft System Center 모니터링 팩**

이 [모니터링 팩][5]을 통해 [Microsoft System Center Operations Manager][6]를 사용하여
Azure 응용 프로그램의 가용성 및 성능을 모니터링할 수 있습니다.

Azure 응용 프로그램의 상태를 모니터링하려면 Microsoft System Center Operations Manager
2007을 사용하는 것이 가장 좋습니다.

**Azure 플랫폼 관리 도구**

다른 도구로는 [Azure 플랫폼 관리 도구(MMC)][7]가 있으며, Azure 호스티드 서비스 및 저장소 계정을 관리할 수
있습니다. 완전한 소스 코드의 샘플로 제공된 이 도구를 통해 Azure 관리 및 진단 API를 사용하여 다양한 관리 및 구성
작업을 수행하는 방법을 알 수 있습니다.

**Cerebrata 도구**

Cerebrata는 Azure 응용 프로그램을 모니터링하고 관리할 수 있는 많은 도구를 제공합니다. 여기에는 [Azure
Diagnostics Manager][8], [Cloud Storage Studio][9] 및 [Azure Management
Cmdlets][10]가 포함됩니다.

Azure Diagnostics Manager 2는 Azure 진단을 관리하기 위한 Windows(WPF) 기반 클라이언트입니다.
이 도구를 통해 Azure에서 실행 중인 응용 프로그램이 수집한 진단 데이터를 보고 다운로드하고 관리할 수 있습니다. 이러한
제품에 대한 자세한 내용은 [http://www.cerebrata.com][11](영문)을 참조하십시오.

Cloud Storage Studio 2는 Azure 저장소를 관리하기 위한 Windows(WPF) 기반 클라이언트입니다.

Azure Management Cmdlets는 Azure 저장소, 호스티드 서비스, SQL 데이터베이스 인스턴스 및 진단을
관리하기 위한 Windows PowerShell cmdlet 집합입니다. 또한 저장소 계정을 백업하고 복원하기 위한 cmdlet도
제공합니다.

**네트워크 모니터링: AlertBot, Gomez, Keynote, Pingdom**

Compuware의 [Gomez][12] 응용 프로그램 성능 관리, [Keynote][13], [Pingdom][14] 및
[AlertBot][15]는 Azure 응용 프로그램을 원격으로 모니터링하기 위한 솔루션입니다. 이를 통해 응용 프로그램의
가용성을 모니터링하고 성능을 최적화할 수 있습니다. Pingdom과 같은 일부 서비스는 오류가 검색되면 전자 메일, SMS 또는
데스크톱 응용 프로그램을 통해 이를 알립니다. 이런 종류의 모니터링은 최종 사용자가 응용 프로그램을 성공적으로 모니터링하기 위해
해야 하는 작업을 시뮬레이션합니다.

**Apica 도구**

Apica는 Azure 웹 응용 프로그램을 "외부에서" 모니터링하는 도구를 제공합니다. 자세한 내용은
[http://www.apicasystem.com/integration-partners/][16](영문)를 참조하십시오.

**AVIcode**

AVIcode는 Microsoft에서 매입하여 이제는 Microsoft System Center의 일부입니다.
[AVIcode][17]는 광범위한 응용 프로그램 모니터링 기능 모음과 함께 .NET 응용 프로그램 성능 모니터링 기능을
제공합니다.

**성능 프로파일링**

Azure 응용 프로그램이 Windows Azure에서 실행될 때 성능 문제를 확인하기 위해 해당 응용 프로그램을
[프로파일링][18]할 수 있습니다. Visual Studio에서 Windows Azure 응용 프로그램을 게시하면 해당 응용
프로그램을 프로파일링하도록 선택할 수 있고 원하는 프로파일링 설정을 선택할 수 있습니다.

**Azure VM Assistant**

[VM Assistant][19] 도구는 원격 데스크톱을 가상 컴퓨터 인스턴스에 액세스할 때 관련된 모든 문제 해결 데이터를 한
곳에 수집하는 CodePlex 프로젝트입니다. **VM Health** 단추는 인스턴스의 현재 상태를 제공합니다.
### 가능한 경우 오류 디버깅

Azure에 응용 프로그램을 배포하기 전에 응용 프로그램을 로컬로 디버그하는 것이 좋습니다. Azure SDK에는 응용 프로그램을
배포하지 않고도 응용 프로그램을 실행하고 기초 테스트를 수행할 수 있으며 Azure 클라우드 환경을 가장하는 에뮬레이터가 포함되어
있습니다. 사용하는 디버깅 도구는 사용 중인 프로그래밍 언어와 개발 도구에 따라 다릅니다.

응용 프로그램이 배포되고 나면 Visual Studio와 같은 디버거를 사용하여 클라우드에서 디버그할 수 있습니다. 이 경우
디버그 빌드를 만들어 배포해야 합니다. 이렇게 하려면 특정 역할 인스턴스에 연결해야 합니다. 응용 프로그램에 여러 역할 및 역할
인스턴스가 있어 복잡한 경우에는 연결할 역할 인스턴스를 결정하기가 아주 어려울 수 있습니다. Visual Studio
Ultimate은 IntelliTrace를 지원하며, 이를 통해 .NET 역할이 디버그 정보를 추적할 수 있습니다. 문제가
발생하면 이 정보를 다운로드하여 Visual Studio에 로드할 수 있습니다. 각 역할 인스턴스의 IntelliTrace 로그를
보고 문제가 발생한 위치를 확인할 수 있습니다. 클라우드에서의 디버깅에 대해 단점이 있으며 필요한 경우도 있습니다. 모든
Azure 서비스에 에뮬레이터가 있는 것은 아니며(예: 서비스 버스) 지원되는 개발 도구에도 모두 에뮬레이터가 제공되지는
않습니다(예: Mac과 Linux).

응용 프로그램을 로컬로 디버그하고 나면 응용 프로그램에 빌드된 계측에 의존하여 문제가 발생하는 곳을 확인해야 합니다.

**Node.js 디버깅**

Node.JS 응용 프로그램을 디버깅하는 경우 [GitHub][20]에서 Node-Inspector 도구를 사용할 수 있습니다.
Node-Inspector는 Azure 저장소 에뮬레이터를 사용하여 개발 컴퓨터에서 로컬로 실행될 수 있습니다. 자세한 내용은
Jim Wang의 블로그 [Azure 에뮬레이터에서의 노드 디버깅][21](영문)을 참조하십시오.

Azure에서 응용 프로그램을 디버깅하고 있으면 웹 역할, 작업자 역할 또는 VM 인스턴스에 [GitHub][22]의
IISNode 전체 버전을 설치하십시오. 앞서 논의한 것처럼 이 방법은 응용 프로그램이 프로덕션에 있고 여러 인스턴스로 확장된
경우 디버깅할 역할 인스턴스나 VM을 알 수 없으므로 권장할 만한 응용 프로그램 디버깅 방법이 아닙니다.

웹 역할에 Node-Inspector를 사용하려면 웹 역할에 있는 패키지를 설치하고 평소대로 Node-Inspector 도구를
사용하십시오. Node-Inspector로 디버깅하는 방법에 대한 자세한 내용은 [Node-Inspector로
디버깅][23](영문)을 참조하십시오.

**IntelliTrace**

Microsoft Visual Studio Ultimate에는 응용 프로그램을 프로덕션에 배포하기 전에 디버깅할 수 있는
[IntelliTrace][24]가 포함되어 있습니다. IntelliTrace는 ASP.NET와 WCF 응용 프로그램을
지원합니다. Intellitrace는 프로덕션 서비스에서 사용하도록 설정되어 있으면 지원되지 않지만 Azure에 배포된 후 응용
프로그램에 대한 예외 사항을 가져오기 위해 사용할 수는 있습니다. Jim Nakashima의 블로그 게시물은
[IntelliTrace를 사용하여 Windows Azure 클라우드 서비스를 디버깅하는 방법]에 대해 설명합니다.

**Fiddler**

[Fiddler][25]는 컴퓨터와 인터넷 간의 모든 HTTP(S) 트래픽을 로깅하는 웹 디버깅 프록시입니다. Fiddler를
사용하여 트래픽을 검사하고 중단점을 설정하고 들어오거나 나가는 데이터를 "조작"할 수 있습니다. Fiddler는 Azure
저장소의 문제를 해결하는 데 특히 유용합니다.

로컬 개발 패브릭에 대해 Fiddler를 사용하려면 127.0.0.1 대신 ipv4.fiddler를 사용하십시오.

* Fiddler를 시작합니다.
* 개발 패브릭에서 서비스를 시작합니다.
* http://ipv4.fiddler:/로 이동합니다. Fiddler는 요청을 추적해야 합니다.

로컬 개발 저장소에 대해 Fiddler를 사용하려면 Fiddler를 가리키도록 서비스 구성 파일을 수정하십시오.

ServiceConfiguration.cscfg 파일을 열고 연결 문자열을 다음과 같이 변경합니다.

    Value="UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://ipv4.fiddler"

* Fiddler를 시작합니다.
* 서비스를 시작합니다. Fiddler가 모든 저장소 요청을 추적해야 합니다.
## 문제 해결 및 Azure 호스팅 모델

이 섹션에서는 다른 Azure 호스팅 모델을 사용하여 응용 프로그램을 디버깅하는 유용한 정보에 대해 설명합니다.

<h2><a  id="Websites" ></a>Azure 웹 사이트</h2>


지원 가능한 Azure 웹 사이트를 설계할 때 가능한 경우 이 문서의 앞부분에서 설명한 권장 사항을 따르십시오. 이 권장 사항에는
오류 확인 및 처리, 일시적 오류 재시도 논리 적용, 추적 및 로깅이 포함됩니다. 응용 프로그램 오류를 표시하고, 웹 사이트에
대한 진단을 구성하고, 진단 데이터를 수집한 후 문제를 확인하고 해결하기 위해 수집된 데이터를 분석하여 Azure 웹 사이트의
문제를 해결합니다.

Azure 웹 사이트는 다음 진단 옵션의 구성을 사용할 수 있습니다.

* 웹 서버 로깅
* 자세한 오류 메시지
* 실패한 요청 추적

이러한 항목에 대한 자세한 내용은 [Azure 웹 사이트 문제
해결](/en-us/develop/net/best-practices/troubleshooting-web-sites/)을
참조하십시오.

Azure 웹 사이트에 대해 웹 서버 로그를 사용하도록 설정되어 있으면 해당 웹 사이트는 [W3C 확장 로그 파일 형식][26]을
사용하여 모든 HTTP 트랜잭션을 로그 파일에 기록합니다. 그런 다음 [Log Parser][27]를 사용하여 로그 파일을 쿼리할
수 있습니다. 일부 로그 파서 쿼리 예는 [Log Parser Plus][28](영문) 및 [TechNet Log Parser
예제][29](영문)에서 사용할 수 있습니다. Office 2007/2010을 실행하는 컴퓨터에서 CHART 출력 유형을
생성하려면 [Log Parser Plus][30]의 안내에 따라 [Office 2003 Web Components][31]를
설치하십시오.

Azure 웹 사이트는 IIS 7.0 이상 버전에서 사용할 수 있는 실패한 요청 추적 기능을 똑같이 사용합니다. 하지만 IIS
실패한 요청 추적처럼 구성할 수는 없습니다. Azure 웹 사이트에 대해 실패한 요청 추적을 사용하도록 설정하면 실패한
**모든** 요청이 캡처됩니다.

<h2><a  id="Cloudservices" ></a>Azure 클라우드 서비스</h2>


Azure 클라우드 서비스의 분산 특성 때문에 이전에 설명한 대로 호출을 비동기적으로 만들고 일시적 오류의 다시 시도를 처리하여
응용 프로그램을 방어하는 것이 중요합니다.

Azure 클라우드 서비스에 사용되는 디버깅 기술은 발생한 문제의 유형에 따라 다릅니다. 특정 역할이나 역할 인스턴스와 관련된
문제(예: 시작하거나 순환하지 못한 역할)는 원격 데스크톱을 사용하여 가장 잘 확인됩니다. 이러한 경우 문제가 있는 역할 또는
역할 인스턴스를 알 수 있어 영향을 받은 역할에 연결할 수 있습니다. 문제가 발생할 경우 및 문제를 일으키는 역할 인스턴스를 모를
경우 문제를 해결하는 가장 좋은 방법은 추적과 로깅입니다. Azure 진단에서는 추적 및 로그 정보를 수집하고 관리하는 메커니즘을
제공합니다.

일부 새로운 디버깅 기능이 Azure SDK 1.7에 추가되어 예외 발생 시 좀 더 쉽게 스택 추적을 찾을 수 있고 원격 데스크톱
연결이 개선되었습니다. 스택 추적이 이제 Windows 이벤트 로그에 포함되어 문제를 정확하게 알기 더욱 쉬워졌습니다. 게다가
원격 데스크톱 연결도 개선되었습니다. 역할이 순환 중이거나 중단되었으면 원격 데스크톱을 사용하여 문제가 있는 역할에 연결하여
문제를 조사할 수 있습니다.

Azure 포털에서는 IT 전문가 및 개발자가 Azure 클라우드 서비스에서 문제를 예상하고 진단하는 데 도움이 되는 데이터를
모니터링하기 위한 액세스를 제공합니다. 기본적으로 호스트 VM이 "CPU 비율", "데이터 입력", "데이터 출력", "디스크
읽기 처리량" 및 "디스크 쓰기 처리량"과 같은 값을 수집합니다. 역할 인스턴스에 이러한 메트릭을 사용하도록 설정하는 데 필요한
구성은 없으며 고객에게 비용상 영향도 없습니다. 추가 성능 정보도 수집할 수 있습니다. 자세한 진단 정보를 수집하려면 이 정보가
Azure 저장소에 저장되고 그 결과 추가 저장 비용이 초래되므로 유효한 진단 연결 문자열이 있어야 합니다. 사용자가 자세한
모니터링을 사용할 수 있으면 포털은 기본 성능 카운터 집합을 수집하도록 원격으로 역할 인스턴스를 구성합니다.
### Azure 진단

진단 데이터를 수집하고 수집한 데이터를 모아 Azure 저장소에 저장하는 Azure 진단이라는 기능이 원래 Azure SDK
1.0에 포함되어 있습니다. ETW(Windows용 이벤트 추적) 프레임워크에 빌드된 이 소프트웨어는 Azure 확장 아키텍처에서
언급된 다음 두 가지 설계 요구 사항을 충족합니다.

* 인스턴스 이미지를 다시 설치하는 동안 손실될 수 있는 진단 데이터를 저장합니다.
* 여러 인스턴스의 진단에 중앙 리포지토리를 제공합니다.

역할에서 진단을 구성하고 나면 진단은 특정 해당 역할의 모든 인스턴스로부터 진단 데이터를 수집합니다. 진단 데이터는 디버깅 및
문제 해결, 성능 측정, 리소스 사용 모니터링, 트래픽 분석 및 용량 계획, 감사 등에 사용될 수 있습니다. 예약하거나 요청 시
지속성을 위해 Azure 저장소 계정으로 전송할 수 있습니다.

Azure 진단은 다음과 같은 4가지 중요한 방법으로 서버 패러다임을 변경합니다.

* 응용 프로그램 생성 시간에 진단을 사용할 수 있어야 합니다.
* 진단 결과를 시각화하려면 특정 도구/단계가 필요합니다.
* 영구 저장소(각 인스턴스에 기록되는 것과는 다른 Azure 저장소)에 기록하지 않으면 크래시로 인해 진단 데이터가 손실될 수
  있습니다.
* 진단 저장은 Windows Azure 저장소에 저장한 경우 매달 비용이 발생합니다.

Azure의 주요 장점 중 하나가 비용 절감이므로 비용은 특히 중요합니다. 오늘날 진단 사용 비용을 없애는 유일한 방법은 데이터를
가상 컴퓨터에 두는 것입니다. 이는 소규모 배포에서는 가능할 수 있으나 인스턴스가 많은 경우에는 실행할 수 없습니다. 다음은
재정적인 영향을 최소화하는 몇 가지 방법입니다.

* 저장소 계정이 응용 프로그램과 같은 데이터 센터에 있는지 확인합니다. 어떤 이유로 같은 데이터 센터에 있지 않은 경우에는 예약
  전송 간격을 신중하게 선택합니다. 전송 시간이 짧으면 데이터 정확도가 높아지지만 추가로 필요한 대역폭 및 처리 오버헤드를
  상쇄할 만한 효과는 없을 수 있습니다.
* 진단 데이터를 Azure 저장소에서 주기적으로 복사하고 지웁니다. 진단 데이터는 Azure 저장소를 통해 전송되지만 불필요하게
  저장소에 있지는 않습니다. 다음과 같은 여러 가지 도구로 이 작업을 수행합니다. Azure용 System Center 모니터링
  팩, Cerebrata의 Azure Diagnostics Manager, Azure PowerShell cmdlet
* 응용 프로그램을 모니터링하고 문제를 해결하는 데 필요한 진단 데이터만 선택합니다. 데이터를 너무 많이 캡처하면 비용이 상당히
  많이 증가할 뿐만 아니라 문제를 해결하기도 더 어려워집니다.
* 응용 프로그램에서 주문형 스위치를 구현하여 진단 데이터의 컬렉션 및 범위를 제어합니다.
* 모든 정보를 사용할 수 있도록 로깅 수준(자세한 정보, 정보, 경고, 오류)을 활용한 다음 배포 후 진단 구성을 활용하여
  데이터를 선택적으로 수집합니다.

<h2><a  id="Vms" ></a>Azure 가상 컴퓨터</h2>


일반적으로 Azure 가상 컴퓨터에서 실행 중인 응용 프로그램의 문제 해결에는 사용 중인 운영 체제나 플랫폼에서 사용하는 문제
해결과 같은 기술이 관련되어 있습니다. 예:

* Windows Server 2008 R2 x64(RTM 및 SP1), Windows 8 Service
  x64(Datacenter 및 Core). 주제에 관해 많은 정보가 있으며 작업을 신속하게 도와줄 수 있는 도구가 많습니다.

* 접근 방법에 대한 자세한 내용은 [방법: Windows 서비스 응용 프로그램 디버깅][32]을 참조하십시오.

* 가장 좋은 방법을 설명하는 동영상은 [Daniel Pearson의 프레젠테이션인 Windows Debugging and
  Troubleshooting][33]을 참조하십시오.

* 개방형 Suse Linux. Suse Linux의 응용 프로그램 문제 해결에 사용할 수 있는 리소스도 [Suse Linux
  설명서][34](영문)와 인터넷에 굉장히 많습니다.
* Ubuntu Linux. 역시 많은 양의 정보가 있습니다. 제품 설명서를 시작하려면
  [https://help.ubuntu.com/][35](영문)을 참조하십시오.
* CentOS Linux. 자세한 내용은 [http://centos.org/][36](영문)를 참조하십시오.

<h2><a  id="PlatformServices" ></a>Azure 서비스 문제 해결</h2>


Azure SQL 데이터베이스, Azure Active Directory 및 Azure 저장소와 같은 많은 Azure 서비스에는
응용 프로그램이 Azure에서 실행 중인지, 어떤 프로그래밍 언어 또는 라이브러리로 빌드되었는지, Microsoft 운영 체제가
아닌 다른 운영 체제에서 실행 중인지와 상관없이 사용하는 제품에 고유한 문제 해결 정보가 있습니다. 다음은 이러한 서비스 중
일부에 고유한 유용한 정보를 제공합니다.

이 문서의 설계 부분에 설명된 대로 비동기 호출, 추적 및 이벤트 로깅의 모범 사례를 구현하는 라이브러리는 많이 지원됩니다.
#### Azure 저장소 문제 해결

다음 링크에서는 문제 해결이 필요한 문제를 완화시키는 설계나 사례, 또는 추적이나 로깅 작업을 추가해야 하는 위치에 대해
논의합니다.

* 저장소 상태 및 오류 코드:
  [http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx][37]

* 저장소 분석:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx][38](아래쪽의
  세 가지 "기타 정보" 링크 참조)

* Azure 저장소 클라이언트 라이브러리의 재시도 정책 개요:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx][39]

* Azure 테이블을 최대한 활용하는 방법:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx][40]
  
  * 다음 섹션 참조: Best Practices, Tips for Programming Azure, Timeouts and
    ServerBusy - Is it normal?

* 응용 프로그램 오류로부터 Blob 보호:
  [http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx][41]

**Azure 저장소 탐색기**

Azure 저장소를 탐색하는 데는 여러 가지 방법이 있습니다. Windows Azure 저장소 팀에서 [저장소 탐색기
목록][42]을 제시했습니다. 이 탐색기 중 하나로 진단 파일 및 Azure 저장소 분석 파일을 볼 수 있습니다.
Cloudberry Lab의 [Azure Blob 저장소용 탐색기][43](영문)는 **저장소 설정**을 클릭하여 응용
프로그램에서 바로 저장소 분석을 사용할 수 있는 사용자 인터페이스를 제공합니다.

<h2><a  id="Misc" ></a>Azure 서비스 버스 문제 해결</h2>


이 섹션에서는 Azure 서비스 버스를 안정적이고 합리적으로 사용하여 일반적인 문제 발생을 최소화하도록 응용 프로그램을 개발하는
방법을 개략적으로 설명합니다. 또한 일반적인 서비스 버스 오류를 확인하고 해결하는 방법에 관한 세부 정보도 제공합니다.
### 일반 지침

서비스 버스는 릴레이되고 조정된 메시징 기능을 지원하는 인터넷 범위의 [엔터프라이즈 서비스 버스][44]입니다. 서비스 버스는 두
가지 유형의 메시징에 대해 시스템 수준에서 할당량과 임계값을 구현합니다. 응용 프로그램이 이 할당량을 초과하면 응용 프로그램이
제한되거나 요청 또는 메시지가 거부됩니다. 서비스 버스 할당량 및 이 할당량이 초과할 때 나타나는 동작에 대한 자세한 내용은
[Azure 서비스 버스 할당량][45]을 참조하십시오. 일부 할당량(예: 큐 또는 토픽의 크기)은 엔터티를 만들 때 사용자가
정의합니다.

서비스 버스 메시징 엔터티의 데이터 및 데이터 처리 방법을 보려면 [서비스 버스 탐색기][46] 또는 Azure Tools for
Visual Studio(버전 1.7 이상)의 서버 탐색기를 사용하여 큐, 토픽, 구독 및 규칙을 만들고 삭제하고 테스트할 수
있습니다. 이는 실행 중이지만 예상한 방법으로 데이터를 처리하지 않는 응용 프로그램의 문제를 해결하는 훌륭한 방법입니다. 이러한
도구에는 큐, 토픽 및 릴레이 엔터티를 테스트하고, 보낸 사람과 받는 사람의 각 작업이 수행한 작업을 추적하고, 진행 중인 테스트
실행의 진행률 및 성능을 모니터링하고, 오류 메시지를 포함하여 결과의 자세한 로그를 생성할 수 있는 기능이 포함되어 있습니다.
### 서비스 버스 릴레이

서비스 버스 "릴레이" 서비스는 클라우드에서 실행되며, SOAP, WS-\*, REST를 포함한 웹 서비스 표준 및 여러 가지
다른 전송 프로토콜을 지원합니다. 들어오는 세션 및 WCF 서비스로 보낸 요청을 수신하는 데 서비스 버스 릴레이를 대리자로 사용할
수 있습니다. 릴레이된 메시징 패턴에서는 온-프레미스 또는 클라우드 기반 서비스가 아웃바운드 포트를 통해 릴레이 서비스에 연결하고
특정 랑데부 주소와 연결된 통신에 사용할 양방향 소켓을 만듭니다. 클라이언트는 서비스가 상주하고 있는 위치를 알 필요가 없으며,
온-프레미스 서비스는 방화벽에 열려 있는 인바운드 포트가 필요 없습니다. 하지만 사용자의 네트워크 구성에 따라 방화벽이나 프록시
서버를 통해 서비스 버스 릴레이에 연결할 때 문제가 발생할 수도 있습니다. [방화벽 내 서비스 버스 호스팅][47]에서는 이러한
연결 문제를 해결하는 방법에 대해 설명합니다.
### 서비스 버스 큐 및 토픽

서비스 버스 큐 및 토픽은 조정된 메시징 기능을 제공합니다. 이 기능은 받는 사람이 메시지를 수신할 준비가 될 때까지 안정적으로
보관되는 서비스 버스 큐나 토픽에 메시지를 푸시합니다. 메시지 보낸 사람과 받는 사람이 동시에 온라인 상태일 필요가 없습니다. 이
메시징 인프라는 소비하는 쪽에서 메시지를 수신할 준비가 될 때까지 메시지를 안정적으로 저장합니다. 메시징 API는 응용 프로그램에
영향을 줄 수 있는 다양한 오류가 발생할 수 있습니다. 이러한 오류는 다음 범주로 대략 그룹화할 수 있습니다.

* 사용자 오류 - 예: 인수가 잘못되었다는 코드. 권장 작업: 코드를 수정한 후 계속합니다.
* 설치/구성 오류 - 예: 없거나 삭제된 동작과 연결된 큐 또는 토픽. 권장 작업: 구성을 검토하고 필요한 경우 변경합니다.
* 일시적인 오류 - 예: 서비스가 요청을 현재 처리할 수 없다는 응답. 권장 작업: 작업을 다시 시도하거나 사용자에게 알립니다.
  자세한 내용은 [일시적인 통신 오류를 처리][48]를 참조하십시오.
* 기타 오류 - 예: 시간 초과 오류 또는 메시지 잠금이 손실되었다는 오류. 권장 작업: 일반적으로는 정리 또는 중단을 수행하기
  위해 이러한 예외를 처리하지 않습니다. 이러한 예외는 추적용으로 사용될 수 있습니다.

[메시징 예외][49]는 .NET용 서비스 버스 클라이언트 라이브러리 사용자에게 발생할 수 있는 예외의 개요와 각 예외 종류를
처리하는 방법에 관한 권장 작업을 함께 제공합니다. .NET용 클라이언트 라이브러리는 다른 언어용 서비스 버스 라이브러리 구조와
잘 맞춰져 있으므로 .NET 언어로 프로그래밍하지 않았을지라도 이 지침이 유용할 수 있습니다. 예를 들어 일시적인 오류와 같은
일부 경우 동작을 다시 시도할 수 있습니다. 이 문서의 앞 부분에 나온 일시적인 오류 처리 지침에 따라 일시적인 오류를 효율적으로
처리할 수 있습니다. 또한 .NET 응용 프로그램에서 일시적 서비스 버스 오류를 처리하는 방법을 설명하는 샘플 코드, 자세한 내용
및 모범 사례는 Azure 개발자 지침 문서인 [Azure 서비스 버스 브로커 메시징 API를 활용하는 최상의 방법][50]에서
[일시적인 통신 오류를 처리][48] 섹션을 참조하십시오.

조정된 메시징을 사용하는 응용 프로그램을 개발할 때 중점을 두어야 할 또 다른 부분은 메시지에서 예외를 정확하고 효율적으로 처리할
수 있는 신뢰할 만한 메시지 수신 논리를 구현해야 한다는 점입니다. [Azure 서비스 버스 브로커 메시징 API를 활용하는
최상의 방법][50] 문서의 "신뢰할 수 있는 메시지 수신 루프 구현" 섹션에서는 메시지가 처음에 성공적으로 배달되지 않은 경우
여러 번 메시지를 배달할 수 있는 **PeekLock** 수신 모드를 사용하기 위한 여러 기술에 대해 설명합니다. 이 문서에서는
응용 프로그램이 중복된 메시지를 처리하지 않도록 하는 모범 사례를 권장합니다. 또한 잠금 시간 제한으로 인해 발생할 수 있는
문제를 방지하고, 메시지를 신속하게 처리하여 **PeekLock** 모드에서 전체 성능을 개선하는 데도 도움이 됩니다. 이 문서는
.NET용 서비스 버스 클라이언트 라이브러리를 사용하는 샘플 코드도 제공합니다.
### 추가적인 문제 해결 리소스

일반적인 서비스 버스 오류 및 오류를 조사하여 해결하는 방법에 관한 자세한 내용은 [서비스 버스 문제 해결][51]을
참조하십시오.
## Azure Active Directory ACS(액세스 제어 서비스)

문제 해결

* 오류 코드:
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/gg185949.aspx][52]
* ACS 서비스 제한:
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/gg185909.aspx][53]

<h2><a  id="SQLTroubleshooting" ></a>Azure SQL 데이터베이스 문제 해결</h2>


Azure SQL 데이터베이스와 상호 작용할 때 Azure SQL 데이터베이스 응용 프로그램의 분산 특성을 주의해서 다루어야
합니다. 이 섹션에서는 주의해야 할 여러 영역에 대해 논의합니다. 이는 전체 목록은 아닙니다. 지원 가능한 Azure SQL
데이터베이스 코드 작성의 핵심은 반환 코드를 검사하고 실패를 처리하는 확실한 재시도 코드를 가지고 있는 것입니다.

응용 프로그램이 로그인 실패를 적절하게 처리해야 합니다. SQL 데이터베이스 인스턴스를 위해서는 SQL 인증을 사용해야 합니다.
성공적으로 로그인할 수 없는 경우 사용자 자격 증명이 유효하지 않거나 요청한 데이터베이스를 사용할 수 없는 것입니다.

서비스에 액세스할 수 없도록 응용 프로그램이 처리해야 합니다. 서버가 이미 프로비전되고 Azure SQL 데이터베이스 서비스를
사용할 수 있는 경우([Azure 상태][54] 페이지를 사용하여 확인할 수 있음) 현장 설치의 구성 문제가 원인일 수 있습니다.
예를 들어 이름을 확인할 수 없거나(tracert와 같은 도구로 테스트할 수 있음), SQL 데이터베이스에서 사용하는 방화벽 차단
포트 1433이 있거나, 올바르게 구성되지 않은 프록시 서버를 사용하고 있을 수 있습니다. SQL Server와 동일한 기술을
사용하여 이러한 문제를 해결합니다. 자세한 내용은 [SQL 데이터베이스 연결 문제 해결 가이드][55] 및 [SQL 데이터베이스
문제 해결][56]을 참조하십시오.

응용 프로그램이 일반 네트워크 오류를 처리해야 합니다. 다음과 같은 경우 Azure SQL 데이터베이스에서 사용자 연결을 끊을 수
있으므로 일반 네트워크 오류가 발생할 수 있습니다.

* 연결이 장시간 유휴 상태인 경우
* 연결이 리소스를 너무 많이 소비하거나 장시간 동안 트랜잭션을 점유하고 있는 경우
* 서버 사용량이 너무 많은 경우

Azure SQL 데이터베이스에서 성능을 향상시키려면 SQL Server와 동일한 기술을 사용합니다. 자세한 내용은 다음 항목을
참조하십시오.

* SQL Server 온라인 설명서의 [쿼리 문제 해결][57]
* [SQL 데이터베이스 쿼리 문제 해결 및 최적화][58]
* [SQL 데이터베이스 성능 고려 사항 및 문제 해결][59]
* [I/O 성능 향상][60]
* [SQL 데이터베이스용 System Center 모니터링 팩][61]

Azure SQL 데이터베이스는 SQL Server 오류 메시지의 하위 집합을 사용합니다. SQL Server 오류에 대한 자세한
내용은 SQL Server 온라인 설명서의 [오류 및 이벤트 참조(데이터베이스 엔진)][62]를 참조하십시오.

로그인 이름이나 암호를 복구해야 하는 경우 서버와 데이터베이스에 적절한 액세스 권한을 부여할 수 있는 서비스 관리자에게
문의하십시오. 서비스 관리자는 Azure 관리 포털을 사용하여 자신의 암호를 다시 설정할 수도 있습니다.

SQL 데이터베이스 쿼리는 잘못된 쿼리, 네트워크 문제 등의 다양한 이유로 실패할 수 있습니다. 일부 오류는 일시적이어서 문제가
저절로 해결되기도 합니다. 이를 고려하여 잠시 후에 쿼리를 다시 시도하는 것이 좋습니다. 여러 번 다시 시도한 후에도 쿼리가
실패하면 오류를 보고할 수 있습니다. 물론 모든 오류가 일시적인 오류인 것은 아닙니다. SQL 오류 102, "잘못된 구문"은
같은 쿼리를 제출한 횟수와 상관없이 해결되지 않습니다. 간단히 말해 강력한 재시도 논리 구현은 신중히 생각해야 합니다.
TDS(Tabular Data Stream) 오류 토큰은 가능한 경우 사용자 연결을 끊기 전에 전송됩니다. 응용 프로그램 환경을
향상시키려면 SQL 데이터베이스 응용 프로그램에서 재시도 논리를 구현하여 이러한 오류를 catch하는 것이 좋습니다. 오류가
발생하면 다시 연결한 다음 실패한 명령이나 쿼리를 다시 실행합니다. 자세한 내용은 다음 링크를 참조하십시오.

* [SQL 데이터베이스에서 일시적인 오류에 대해 논리 다시 시도][63]
* [SQL 데이터베이스 재시도 논리 샘플(영문)][64]
* [일시적인 오류 처리 응용 프로그램 블록(영문)][65]
### Azure SQL 백업 및 복원 전략

Azure SQL 데이터베이스는 사용할 수 있는 환경 및 도구로 인해 자신의 백업 및 복원 전략이 필요합니다. 위험은 여러 가지
방법으로 Microsoft 데이터 센터에 있는 데이터베이스의 영향을 받아 왔습니다. 더 나은 도구가 제공되어 작업을 더욱 쉽게
만든다고 할지라도 오늘날 사용할 수 있는 도구는 다른 위험 요인을 해결합니다. Red-gate는 최근 SQL 데이터베이스 백업 및
복원용 무료 도구를 게시했으며 다음에서 찾을 수 있습니다.
[http://www.red-gate.com/products/dba/sql-azure-backup/][66]

SQL 데이터 동기화를 사용하면 코드를 작성할 필요 없이 데이터 동기화 웹 사이트 내에서 양방향 동기화를 쉽게 만들고 예약할 수
있습니다. 자세한 내용은 다음에서 찾을 수 있습니다.
[http://msdn.microsoft.com/ko-kr/library/windowsazure/hh456371.aspx][67]

SQL 데이터베이스 백업 및 복원 전략에 대한 자세한 내용은 다음 문서를 참조하십시오.

* 다음 항목에서는 SQL 데이터베이스 백업 및 복원 전략 개요를 제공합니다.
  [http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx][68]
* 다음 항목에서는 데이터베이스를 같은 서버의 다른 데이터베이스에 백업하는 방법에 대해 설명합니다.
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/ff951631.aspx][69]
* 다음 항목에서는 기존 SQL 데이터베이스 인스턴스를 주어진 저장소 계정의 Blob으로 내보내는 방법에 대해 설명합니다.
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/hh335292.aspx][70]
* 다음 항목에서는 Blob에 저장된 bacpac 파일에서 기존 SQL 데이터베이스 인스턴스를 가져오는 방법에 대해 설명합니다.
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/hh335291.aspx][71]
* 다음 항목에서는 SQL 데이터베이스에서 제공하는 비즈니스 연속성 기능에 대해 설명합니다.
  [http://msdn.microsoft.com/ko-kr/library/windowsazure/hh852669.aspx][72]

<h2><a  id="Cache" ></a>Azure 캐싱</h2>


Azure 캐싱은 두 가지 형태인 Azure 공유 캐싱과 역할 기반 Azure 캐싱(미리 보기)으로 제공됩니다. 공유 캐싱은 캐싱
서비스를 제공하는 다중 테넌트 Azure 서비스입니다. Azure 캐싱(미리 보기)은 역할 인스턴스를 호스트하는 가상 컴퓨터의
메모리 일부를 사용하여 캐싱을 역할에 호스트합니다. Azure 캐싱 문제를 해결하려면 오류 코드를 확인하거나 예외를 catch하여
캐시 동작을 관찰하십시오. 역할 기반 캐싱(미리 보기)을 사용하는 경우에는 성능 카운터도 사용할 수 있습니다. 캐싱 문제는 보통
다음 범주 중 하나에 해당합니다.

* 할당량 관련 오류 - 할당량 초과함(공유 캐싱)
* 제한 - 추가로 캐시된 항목을 지원하는 데 필요한 실제 메모리가 부족한 경우에 발생함
* 제거 - 응용 프로그램 성능을 줄이는 방법으로 항목을 강제적으로 제거하여 새 항목을 위한 공간 확보
* 만료- 만료 시간이 너무 짧거나 길게 설정되어 있음

할당량 관련 오류에 대한 자세한 내용은 [할당량 이해][73]를 참조하십시오.



[1]: https://www.windowsazure.com/en-us/develop/overview/
[2]: http://msdn.microsoft.com/en-us/library/hh680918%28v=pandp.50%29.aspx
[3]: http://nuget.org/packages/Microsoft.Experience.CloudFx
[4]: http://msdn.microsoft.com/en-us/magazine/ff714589.aspx
[5]: http://www.microsoft.com/download/en/details.aspx?id=11324
[6]: http://www.microsoft.com/en-us/server-cloud/system-center/operations-manager.aspx
[7]: http://wapmmc.codeplex.com/
[8]: http://cerebrata.com/Products/AzureDiagnosticsManager/
[9]: http://cerebrata.com/Products/CloudStorageStudio/
[10]: http://cerebrata.com/Products/AzureManagementCmdlets/
[11]: http://www.cerebrata.com
[12]: http://www.compuware.com/application-performance-management/
[13]: http://www.keynote.com/solutions/
[14]: http://pingdom.com/
[15]: http://www.alertbot.com/products/website-monitoring/default.aspx
[16]: http://www.apicasystem.com/integration-partners/
[17]: http://www.microsoft.com/en-us/server-cloud/system-center/avicode.aspx
[18]: http://msdn.microsoft.com/en-us/library/windowsazure/hh369930.aspx
[19]: http://azurevmassist.codeplex.com/
[20]: https://github.com/dannycoates/node-inspector
[21]: http://weblogs.asp.net/jimwang/archive/2012/04/17/debugging-node-node-inspector-in-the-azure-emulator.aspx
[22]: https://github.com/windowsazure/iisnode/downloads
[23]: http://howtonode.org/debugging-with-node-inspector
[24]: http://msdn.microsoft.com/en-us/library/dd264915.aspx
[25]: http://www.fiddler2.com/fiddler2/
[26]: http://go.microsoft.com/fwlink/?LinkID=90561
[27]: http://go.microsoft.com/fwlink/?LinkId=246619
[28]: http://logparserplus.com/Examples
[29]: http://technet.microsoft.com/en-us/library/ee692659.aspx
[30]: http://logparserplus.com/article/2
[31]: http://www.microsoft.com/downloads/en/details.aspx?familyid=7287252C-402E-4F72-97A5-E0FD290D4B76&displaylang=enBlockquote
[32]: http://msdn.microsoft.com/en-us/library/7a50syb3%28v=vs.90%29.aspx
[33]: http://technet.microsoft.com/en-us/edge/Video/hh867800
[34]: https://www.suse.com/documentation/
[35]: https://help.ubuntu.com/
[36]: http://centos.org/
[37]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179382.aspx
[38]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-analytics.aspx
[39]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/03/overview-of-retry-policies-in-the-windows-azure-storage-client-library.aspx
[40]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
[41]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/30/protecting-your-blobs-against-application-errors.aspx
[42]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/04/17/windows-azure-storage-explorers.aspx
[43]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
[44]: http://en.wikipedia.org/wiki/Enterprise_service_bus
[45]: http://msdn.microsoft.com/en-us/library/windowsazure/ee732538.aspx
[46]: http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a
[47]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706729.aspx
[48]: http://msdn.microsoft.com/en-us/library/hh851746(VS.103).aspx
[49]: http://msdn.microsoft.com/en-us/library/hh418082.aspx
[50]: http://msdn.microsoft.com/en-us/library/windowsazure/hh545245.aspx
[51]: http://msdn.microsoft.com/en-us/library/windowsazure/ee706702.aspx
[52]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185949.aspx
[53]: http://msdn.microsoft.com/en-us/library/windowsazure/gg185909.aspx
[54]: http://go.microsoft.com/fwlink/p/?LinkId=168847
[55]: http://social.technet.microsoft.com/wiki/contents/articles/sql-azure-connectivity-troubleshooting-guide.aspx
[56]: http://msdn.microsoft.com/en-us/library/ee730906.aspx
[57]: http://msdn.microsoft.com/en-us/library/ms186351(SQL.100).aspx
[58]: http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-sql-azure.aspx
[59]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2011/DBI314
[60]: http://blogs.msdn.com/b/sqlazure/archive/2010/07/27/10043069.aspx?PageIndex=2#comments
[61]: http://www.microsoft.com/en-us/download/details.aspx?id=10631
[62]: http://go.microsoft.com/fwlink/p/?LinkId=166622
[63]: http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-sql-azure.aspx
[64]: http://code.msdn.microsoft.com/windowsazure/SQL-Azure-Retry-Logic-2d0a8401
[65]: http://msdn.microsoft.com/en-us/library/hh680934(PandP.50).aspx
[66]: http://www.red-gate.com/products/dba/sql-azure-backup/
[67]: http://msdn.microsoft.com/en-us/library/windowsazure/hh456371.aspx
[68]: http://social.technet.microsoft.com/wiki/contents/articles/1792.sql-azure-backup-and-restore-strategy.aspx
[69]: http://msdn.microsoft.com/en-us/library/windowsazure/ff951631.aspx
[70]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335292.aspx
[71]: http://msdn.microsoft.com/en-us/library/windowsazure/hh335291.aspx
[72]: http://msdn.microsoft.com/en-us/library/windowsazure/hh852669.aspx
[73]: http://msdn.microsoft.com/en-us/library/gg185683.aspx
