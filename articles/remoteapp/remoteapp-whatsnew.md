
<properties 
    pageTitle="Azure RemoteApp의 새로운 기능"
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
    ms.date="08/12/2015" 
    ms.author="elizapo" />



# RemoteApp의 새로운 기능은 무엇인가요?

RemoteApp의 이점 중 하나는 항상 개선하기 위해 노력한다는 것입니다. 개선 시, 여기에서 해당 변경 내용을 발표합니다.

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
- 그리고 제한과 관련하여 [서비스 제한 및 기본값](remoteapp-servicelimits.md)의 정의입니다.

Azure RemoteApp에 대해 더 알아보고 싶으세요? RemoteApp 팀은 몇 주 전에 Ignite에서 시행이 중지되었습니다. [Microsoft Azure RemoteApp 관리 및 관리의 기초](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)라는 Eric의 비디오를 확인하십시오.

실제 환경에서 Azure RemoteApp를 볼 필요가 있습니까? [어디에서든 모든 장치에서 앱 실행](remoteapp-anyapp.md) 자습서를 확인하여 데이터베이스 파일의 공유를 포함하는 사용자와 액세스 공유 방법을 보여줍니다. 또한 동일하게 모든 장치에서 실행되는 [Office 365 만들기](remoteapp-tutorial-o365anywhere.md) 자습서가 있습니다.

우리와 함께해 주셔서 감사하며 자세한 업데이트로 다음 달에 돌아오겠습니다.

<!---HONumber=August15_HO7-->