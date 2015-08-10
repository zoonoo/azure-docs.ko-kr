<properties 
   pageTitle="Microsoft 지원 문의 | Microsoft Azure"
   description="StorSimple 장치에서 지원 요청을 만들고 지원 세션을 시작하는 방법을 알아봅니다."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/24/2015"
   ms.author="alkohli" />

# Microsoft 지원에 문의

Microsoft Azure StorSimple 솔루션에 문제가 발생하는 경우 기술 지원을 위해 서비스 요청을 할 수 있습니다. 지원 엔지니어와 함께 온라인 세션에서 StorSimple 장치에 지원 세션을 시작해야 할 수도 있습니다. 이 문서는 StorSimple 장치의 Windows PowerShell 인터페이스에서 지원 요청을 만들고 지원 세션을 시작하는 과정을 안내합니다.

## 지원 요청 만들기

지원 요청을 만들려면 다음 단계를 수행합니다.

#### 지원 요청을 만들려면

1. 지원 요청은 [관리 포털](http://manage.windowsazure.com/)을 통해 만들 수 있습니다. [관리 포털](http://manage.windowsazure.com/)에서 **계정 이름**을 클릭한 다음 **Microsoft 지원 문의**를 클릭합니다.

	![ManagementPortal을 통한 MS 지원 문의](./media/storsimple-contact-microsoft-support/IC777286.png)

1. **Microsoft 지원에 문의** 대화 상자에서


	- 드롭다운 메뉴에서 StorSimple 관리자 서비스와 관련된 대상 **구독**을 선택합니다. **지원 유형**을 **기술**로 지정합니다. 기술 지원을 사용하도록 설정하기 위해 유료 지원 계획이 필요합니다.

	1. **티켓을 만들려면** 확인 아이콘 ![확인 아이콘](./media/storsimple-contact-microsoft-support/IC740895.png)를 클릭합니다.

1. **Microsoft 지원** 창의 **제품** 드롭다운에서 **StorSimple**을 선택합니다.

	![Microsoft 지원 문의 - 상품](./media/storsimple-contact-microsoft-support/IC777288.png)

1. 화면에 나타나는 지시를 따라서 요청을 적절하게 분류하고 문제에 대해 명확하고 구체적인 설명을 제공합니다.

요청을 제출한 후에 지원 엔지니어가 요청을 계속 진행하기 위해 가능한 한 빨리 연락할 것입니다.

## StorSimple용 Windows PowerShell에서 지원 세션 시작

StorSimple 장치에서 발생할 수 있는 문제를 해결하려면 Microsoft 기술 지원 서비스 팀과 연계해야 합니다. Microsoft 기술 지원 서비스 팀은 장치에 로그온하기 위해 지원 세션을 사용해야 합니다.

지원 세션을 시작하려면 다음 단계를 수행합니다.

#### 지원 세션을 시작하려면

1. 직렬 콘솔을 사용하여 직접 또는 원격 컴퓨터에서 텔넷 세션을 통해 장치에 액세스합니다. 이렇게 하려면 [PuTTY를 사용하여 장치 직렬 콘솔에 연결](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)의 단계를 따르십시오.

1. 열린 세션에서 **Enter** 키를 눌러 명령 프롬프트를 엽니다.

1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다.

1. 프롬프트에 다음 암호를 입력합니다.

	`Password1`

1. 프롬프트에 다음 명령을 입력합니다.

	`Enable-HcsSupportAccess`

1. 암호화된 문자열을 표시합니다. 메모장과 같은 텍스트 편집기에 이 문자열을 복사합니다.

1. 이 문자열을 저장하고 Microsoft 기술 지원 서비스에 전자 메일 메시지로 보냅니다.

> [AZURE.IMPORTANT]`Disable-HcsSupportAccess`을 실행하여 지원 액세스를 비활성화할 수 있습니다. StorSimple 장치는 세션이 시작된 8시간 후에 지원 액세스를 비활성화하려고 합니다. 지원 세션을 시작한 후에 StorSimple 장치 자격 증명을 변경하는 것이 좋습니다.

<!---HONumber=July15_HO5-->