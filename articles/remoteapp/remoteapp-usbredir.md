<properties 
    pageTitle="Azure RemoteApp에서 USB 장치를 리디렉션하려면 어떻게 합니까? | Microsoft Azure" 
    description="Azure RemoteApp에서 USB 장치에 대해 리디렉션을 사용하는 방법을 알아봅니다." 
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
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# Azure RemoteApp에서 USB 장치를 리디렉션하려면 어떻게 합니까?

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

장치 리디렉션을 통해 사용자가 자신의 컴퓨터나 Azure RemoteApp의 앱과 태블릿에 연결된 USB 장치를 사용할 수 있습니다. 예를 들어 Azure RemoteApp을 통해 Skype를 공유한 경우 사용자가 자신의 장치 카메라를 사용할 수 있어야 합니다.

계속 진행하기 전에 [Azure RemoteApp에서 리디렉션 사용](remoteapp-redirection.md)의 USB 리디렉션 정보를 읽으세요. 그러나 권장 nusbdevicestoredirect:s:*는 USB 웹 카메라에 대해 작동하지 않으며 일부 USB 프린터 또는 USB 다기능 장치에 대해 작동하지 않을 수 있습니다. 설계 특성상 그리고 보안상의 이유로 Azure RemoteApp 관리자가 장치 클래스 GUID 또는 장치 인스턴스 ID에 의해 리디렉션을 사용하도록 설정해야 사용자가 해당 장치를 사용할 수 있습니다.

이 문서에서는 웹 카메라 리디렉션에 대해 이야기하고 있지만 USB 프린터 및 **nusbdevicestoredirect:s:*** 명령으로 리디렉션하지 않는 기타 USB 다기능 장치를 리디렉션할 때에도 유사한 방법을 사용할 수 있습니다.

## USB 장치에 대한 리디렉션 옵션
Azure RemoteApp은 원격 데스크톱 서비스에 대해 사용할 수 있는 것과 매우 유사한 메커니즘을 USB 장치 리디렉션에 사용합니다. 기반 기술을 통해 지정된 장치에 알맞은 리디렉션 방법을 선택하고**usbdevicestoredirect:s:** 명령을 사용하여 고급 및 RemoteFX USB 장치 리디렉션을 모두 최대한 이용할 수 있습니다. 이 명령에 4개의 요소가 있습니다.

| 처리 순서 | 매개 변수 | 설명 |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1 | * | 고급 리디렉션에 의해 선택되지 않은 모든 장치를 선택합니다. 참고: 설계상 USB 웹 카메라에 대해서는 *가 작동하지 않습니다. |
| | {장치 클래스 GUID} | 지정된 장치 설정 클래스와 일치하는 모든 장치를 선택합니다. |
| | USB\\InstanceID | 지정된 인스턴스 ID에 대해 지정된 USB 장치를 선택합니다. |
| 2 | -USB\\인스턴스 ID | 지정된 장치에 대한 리디렉션 설정을 제거합니다. |

## 장치 클래스 GUID를 사용하여 USB 장치 리디렉션
두 가지 방법으로 리디렉션에 사용할 수 있는 장치 클래스 GUID를 찾을 수 있습니다.

첫 번째 옵션은 [공급업체에 사용할 수 있도록 시스템에서 정의한 장치 설정 클래스](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx)를 사용하는 것입니다. 로컬 컴퓨터에 연결된 장치와 가장 가깝게 일치하는 클래스를 선택합니다. 디지털 카메라의 경우 이 클래스는 이미징 장치 클래스 또는 비디오 캡처 장치 클래스일 수 있습니다. 로컬 컴퓨터에 연결된 USB 장치(이 경우 웹 카메라)에서 작동하는 정확한 클래스 GUID를 찾으려면 장치 클래스를 몇 번 실험해 보아야 합니다.

더 나은 방법 또는 두 번째 옵션은 다음 단계를 수행하여 특정 장치 클래스 GUID를 찾는 것입니다.

1. 장치 관리자를 열고 리디렉션할 장치를 찾아서 마우스 오른쪽 단추로 클릭한 다음 속성을 엽니다. ![장치 관리자 열기](./media/remoteapp-usbredir/ra-devicemanager.png)
2. **세부 정보** 탭에서 속성 **클래스 Guid**를 선택합니다. 이때 나타나는 값이 해당 유형의 장치에 대한 클래스 GUID입니다. ![카메라 속성](./media/remoteapp-usbredir/ra-classguid.png)
3. 클래스 Guid 값을 사용하여 일치하는 장치를 리디렉션합니다.

예:

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

동일한 Cmdlet에서 여러 장치 리디렉션을 결합할 수 있습니다. 예: 로컬 저장소와 USB 웹 카메라를 리디렉션하려면 Cmdlet은 다음과 같습니다.

		Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

클래스 GUID에 의해 장치 리디렉션을 설정하면 지정된 컬렉션에서 해당 클래스 GUID와 일치하는 모든 장치가 리디렉션됩니다. 예를 들어 로컬 네트워크에 동일한 USB 웹 카메라를 가진 여러 대의 컴퓨터가 있는 경우 단일 Cmdlet을 실행하여 모든 웹 카메라를 리디렉션할 수 있습니다.

## 장치 인스턴스 GUID를 사용하여 USB 장치 리디렉션

더 정밀한 제어를 원하여 장치별로 리디렉션을 제어하려면 **USB\\InstanceID** 리디렉션 매개 변수를 사용할 수 있습니다.

이 방법의 가장 어려운 부분은 USB 장치 인스턴스 ID를 찾는 것입니다. 컴퓨터 및 특정 USB 장치에 액세스해야 합니다. 그러고 나서 다음 단계를 수행합니다.

1. [Remote Desktop 세션에서 내 장치와 리소스를 어떻게 사용할 수 있습니까?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)에서 설명한 대로 원격 데스크톱 세션에서 장치 리디렉션을 사용하도록 설정합니다.
2. 원격 데스크톱 연결을 열고 **옵션 표시**를 클릭합니다.
3. **다른 이름으로 저장**을 클릭하여 현재 연결 설정을 RDP 파일에 저장합니다. ![설정을 RDP 파일로 저장](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. 파일 이름과 위치, 예를 들어 “MyConnection.rdp” 및 “This PC\\Documents”를 선택하고 파일을 저장합니다.
5. 텍스트 편집기를 사용하여 MyConnection.rdp 파일을 열고 리디렉션할 장치의 인스턴스 ID를 찾습니다.

이제 다음 Cmdlet에서 인스턴스 ID를 사용합니다.

	Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB<Device InstanceID value>"



### 의견 보내기 
이 기사에 대한 등급을 매기고 아래에 의견을 다는 것은 물론 문서를 직접 변경할 수 있다는 사실을 알고 계셨나요? 누락된 부분이 있나요? 잘못된 부분이 있나요? 혼동을 줄 수 있는 부분이 있나요? 위로 스크롤하여 **GitHub에서 편집**을 클릭하면 변경할 수 있습니다. 당사에서 변경 사항을 검토하고 승인하면 변경 및 개선 사항을 바로 여기서 확인할 수 있습니다.

<!---HONumber=AcomDC_0817_2016-->