<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capture an image" pageTitle="Capture an image of a virtual machine running Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Windows Server를 실행하는 가상 컴퓨터의 이미지를 캡처하는 방법
==============================================================

이미지 갤러리의 이미지를 사용하여 쉽게 가상 컴퓨터를 만들 수도 있고 고유의 이미지를 캡처하여 사용자 지정 가상 컴퓨터를 만드는 데 사용할 수도 있습니다. 이미지는 가상 컴퓨터를 만드는 데 템플릿으로 사용되는 가상 하드 디스크(.vhd) 파일입니다. 구성된 가상 컴퓨터에는 있는 컴퓨터 이름 및 사용자 계정 설정과 같은 특정 설정이 이미지에는 없으므로 이미지는 템플릿입니다. 동일한 방식으로 설정된 가상 컴퓨터를 여러 개 만들려면 구성된 가상 컴퓨터의 이미지를 캡처하여 해당 이미지를 템플릿으로 사용할 수 있습니다.

1.  [Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법](http://www.windowsazure.com/ko-kr/manage/windows/how-to-guides/log-on-a-windows-vm/)에 나열된 단계에 따라 가상 컴퓨터에 연결합니다.

2.  관리자로 명령 프롬프트 창을 엽니다.

3.  디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.

4.  **시스템 준비 도구** 대화 상자가 나타납니다.

    **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/en-us/library/bb457073.aspx)(영문)를 참조하십시오.

5.  **종료 옵션**에서 **종료**를 선택합니다.

6.  **확인**을 클릭합니다.

7.  Sysprep에서 가상 컴퓨터를 종료합니다. 그러면 [관리 포털](http://manage.windowsazure.com)의 컴퓨터 상태가 **중지됨**으로 변경됩니다.

8.  **가상 컴퓨터**를 클릭한 후 캡처하려는 가상 컴퓨터를 선택합니다.

9.  명령 모음에서 **캡처**를 클릭합니다.

    **가상 컴퓨터에서 이미지 캡처** 대화 상자가 나타납니다.

10. **이미지 이름**에 새 이미지의 이름을 입력합니다.

11. 사용자 지정 이미지 집합에 Windows Server 이미지를 추가하기 전에 이전 단계에서 설명한 대로 Sysprep을 실행하여 범용화해야 합니다. **가상 컴퓨터에서 Sysprep을 실행했습니다.**를 클릭하여 이를 수행했음을 나타냅니다.

12. 확인 표시를 클릭하여 이미지를 캡처합니다. 가상 컴퓨터의 이미지를 캡처하면 컴퓨터가 삭제됩니다.

    이제 **이미지**에서 새 이미지를 사용할 수 있습니다.

    **갤러리에서** 방법을 사용하여 가상 컴퓨터를 만들 경우 **이미지 선택** 페이지에서 **내 이미지**를 클릭하여 캡처한 이미지를 사용할 수 있습니다.


