
<properties
    pageTitle="Azure RemoteApp의 새로운 기능 | Microsoft Azure"
    description="Azure RemoteApp에 작성된 변경 및 개선 사항을 알아봅니다."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/02/2016"
    ms.author="elizapo" />



# Azure RemoteApp의 새로운 기능

Azure RemoteApp의 이점 중 하나는 항상 개선하기 위해 노력한다는 것입니다. 개선 시, 여기에서 해당 변경 내용을 발표합니다.

## 향후 업데이트
Azure RemoteApp 팀이 RDS 블로그에 매월 업데이트를 게시한다는 것을 아십니까? Azure RemoteApp의 변경 내용뿐 아니라 RDS 사용 방법에 대한 기타 정보를 확인할 수 있습니다. 자세한 내용은 [원격 데스크톱 서비스 블로그](https://blogs.msdn.microsoft.com/rds/)를 참조하세요. 예를 들어 2주 전에는 [Azure RemoteApp 및 Azure AD를 사용하여 그대로 워크로드 이동](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/)에 대한 항목을 게시했습니다.
 
## 2015년 9월
- Microsoft Office 365 템플릿과 갤러리 이미지에 추가된 Infopath입니다. Infopath를 공유하려면 최신 이미지로 컬렉션을 업데이트해야 합니다.
- 클라이언트 업데이트:
	- 사용자가 특히 연결 문제와 관련된 피드백을 공유할 수 있도록 Windows 클라이언트를 업데이트했습니다.
	- 오류 메시지를 해결하고 자격 증명이 예상보다 빨리 만료되는 문제를 해결하기 위해 iOS 클라이언트를 업데이트했습니다.
- Office 2016 지원을 테스트하고 있습니다. 완료되면 업데이트된 이미지를 찾아보세요.
- [클라우드와 하이브리드 컬렉션 간의 차이점](remoteapp-collections.md)에 대한 새 문서가 게시되었습니다. 이 문서로 앱에 가장 적합한 컬렉션 유형(클라우드 전용, 클라우드 + VNET 또는 하이브리드)을 선택할 수 있습니다.
- Azure RemoteApp을 사용하여 QuickBooks를 공유하고 싶지만 작업 단계를 모르시나요? 수행 방법을 정확히 알려주는 [Eric의 새 문서](remoteapp-quickbooks.md)를 확인하세요.

## 2015년 8월
8월에 발생한 큰 변화 중 주목해야 할 부분은 다음과 같습니다.

- 이제 클라우드 컬렉션으로 Azure VNET을 사용할 수 있습니다. 새로운 단계에 대한 [클라우드 만들기 지침](remoteapp-create-cloud-deployment.md)을 확인하세요.
- Windows RemoteApp 클라이언트의 경우 앱을 **시작** 메뉴에 추가할 수 있게 되었습니다. 앱이 응용 프로그램 목록에 표시되고 해당 앱을 Windows의 **시작 **메뉴에 고정할 수 있습니다.
- 새 이미지를 Azure VM 갤러리(Microsoft Office 365 ProPlus가 있는 Windows 서버 원격 데스크톱 세션 호스트)에 추가했습니다.
- 모달 창을 사용하는 앱이 중단되지 않도록 Mac 클라이언트를 수정했습니다.
- Azure RemoteApp으로 [Office 365 ProPlus 구독](remoteapp-officesubscription.md)을 사용하는 방법에 대해 문서화했습니다.
- Azure RemoteApp 컬렉션의 [앱 및 데이터 보안](remoteapp-secure.md) 방법을 자세히 설명했습니다.

## 2015년 7월

7월에는 8월에 예정된 변경을 준비하므로 현재 다룰 내용은 많지 않고 대부분 문서 업데이트입니다. 다음은 최근 변경 내용입니다.

- 포털에 **지원** 탭이 추가되어 포럼과 같은 지원 리소스에 더욱 쉽게 액세스할 수 있습니다.
- 하이브리드 컬렉션에 대한 문제 해결 정보가 수정되었습니다. VNET 구성을 위한 정확한 포트 식별 방법 등과 같은 문제 해결 팁에 관한 [최신의 가장 강력한](remoteapp-hybridtrouble.md) 기능을 확인하세요.
- [사용자 데이터](remoteapp-upd.md)를 만들고 Azure RemoteApp에 저장하는 방법이 문서화되었습니다.
- [앱 잠금](remoteapp-secure.md) 방법이 문서화되었습니다.
- [Azure RemoteApp cmdlet](https://msdn.microsoft.com/library/mt428031.aspx)이 게시되었습니다.
- 마지막으로 일부 Azure RemoteApp 사용자와 용어에 대한 대화를 시작했습니다. 다양한 컬렉션 옵션을 참조하는 방법이 어떻게 변경되었는지 확인합니다.

## 2015년 6월

많은 것들이 변경됩니다. 6월에 팀이 매우 바빠집니다.

- 재설계된 Azure RemoteApp [방문 페이지](https://www.remoteapp.windowsazure.com/)를 확인하십시오.
- 구독의 일부로서 사용 가능한 모든 이미지에 업데이트된 소프트웨어입니다.
- 컬렉션을 만들기 전에 강제 터널링 지원 및 IP 서브넷 크기를 검사를 포함하여 하이브리드 컬렉션에 기능이 향상됩니다.
- *와일드 카드 웹캠이 작동하지 않는다는 사실을 확인했습니다. 대신, 인스턴스 ID 또는 GUID를 지정해야 합니다. 리디렉션 정보를 반영하도록 업데이트할 예정입니다.
- 그래서 Azure 갤러리에서 템플릿 이미지를 만들 때 사용자 지정 바이러스 백신 소프트웨어를 이미지에 추가할 수 있도록 합니다.

7월에 더 많은 변경을 롤아웃하므로 곧 다시 다른 업데이트를 예정하고 있습니다.

## 2015년 5월

이 항목을 처음 만든 이후로 다양한 추가 기능(및 개월)이 생겨났으므로 이 목록은 약간 다를 수 있으며 기간은 3월 초에서5월까지입니다. 이러한 새 기능을 확인해 보십시오.

- 모든 것이 자동화되어 Azure RemoteApp에 [Azure PowerShell 모듈의 cmdlet](remoteapp-tutorial-arawithpowershell.md)가 있습니다.
- [Azure 가상 컴퓨터에서 Azure RemoteApp 이미지를 만듭니다](remoteapp-image-on-azurevm.md) 사용자 지정 이미지를 Azure에 훨씬 더 빨리 업로드합니다.
- RemoteApp VNET 대신 Azure VNET을 사용하여 Azure에 회사 네트워크 리소스를 연결합니다. Azure VNET (그 1 단계)를 만드는 방법을 안내하도록 [하이브리드 컬렉션 지침](remoteapp-create-hybrid-deployment.md)을 업데이트했습니다.
- VNET과 관련하여 VNET 크기 제한 및 제한 사항에 대한 [새 지침](remoteapp-vnetsizing.md)을 확인하십시오.
- 그리고 제한과 관련하여 [서비스 제한 및 기본값](../azure-subscription-service-limits.md)의 정의입니다.

Azure RemoteApp에 대해 더 알아보고 싶으세요? RemoteApp 팀은 몇 주 전에 Ignite에서 시행이 중지되었습니다. [Microsoft Azure RemoteApp 관리 및 관리의 기초](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)라는 Eric의 비디오를 확인하십시오.

실제 환경에서 Azure RemoteApp를 볼 필요가 있습니까? [어디에서든 모든 장치에서 앱 실행](remoteapp-anyapp.md) 자습서를 확인하여 데이터베이스 파일의 공유를 포함하는 사용자와 액세스 공유 방법을 보여줍니다. 또한 동일하게 모든 장치에서 실행되는 [Office 365 만들기](remoteapp-tutorial-o365anywhere.md) 자습서가 있습니다.

우리와 함께해 주셔서 감사하며 자세한 업데이트로 다음 달에 돌아오겠습니다.


### 의견 보내기
이 기사에 대한 등급을 매기고 아래에 의견을 다는 것은 물론 문서를 직접 변경할 수 있다는 사실을 알고 계셨나요? 누락된 부분이 있나요? 잘못된 부분이 있나요? 혼동을 줄 수 있는 부분이 있나요? 위로 스크롤하여 **GitHub에서 편집**을 클릭하면 변경할 수 있습니다. 당사에서 변경 사항을 검토하고 승인하면 변경 및 개선 사항을 바로 여기서 확인할 수 있습니다.

<!---HONumber=AcomDC_0511_2016-->