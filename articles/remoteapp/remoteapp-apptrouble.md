---
title: "Azure RemoteApp 문제 해결 - 응용 프로그램 시작 및 연결 실패 | Microsoft 문서"
description: "Azure RemoteApp의 응용 프로그램 시작 및 연결 문제에 대한 해결 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a91bea6a7a015ea4a61fd15ba34bcb1a86445b40


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Azure RemoteApp 문제 해결 - 응용 프로그램 시작 및 연결 실패
> [!IMPORTANT]
> Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

Azure RemoteApp에 호스트되는 응용 프로그램은 몇 가지 이유로 시작에 실패할 수 있습니다. 이 문서에서는 다양한 이유와 사용자가 응용 프로그램을 시작하려고 할 때 나타날 수 있는 오류 메시지에 대해 설명합니다. 또한 연결 실패에 대해서도 설명합니다. 그러나 Azure RemoteApp 클라이언트에 로그인할 때 발생하는 문제에 대해서는 이 문서에서 설명하지 않습니다.  

앱 시작 및 연결 실패로 인한 일반 오류 메시지에 대한 정보를 읽어 보세요.

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>설정 중입니다... 10분 후에 다시 시도하세요.
이 오류는 Azure RemoteApp이 사용자의 용량 요구에 맞게 확장되고 있음을 의미합니다. 백그라운드에서는 더 많은 Azure RemoteApp VM 인스턴스가 사용자의 용량 요구 사항을 처리하기 위해 만들어지고 있습니다. 일반적으로 5분 정도 걸리지만 최대 10분까지 소요될 수 있습니다. 경우에 따라 신속하게 진행되지 않아 즉시 리소스가 필요할 수 있습니다. 예를 들어 오전 9시에 많은 사용자가 동시에 Azure RemoteAppn에서 앱을 사용해야 할 수 있습니다. 이 경우 백 엔드에서 **용량 모드** 를 사용할 수 있습니다. 이렇게 하려면 Azure 지원 티켓을 열거나 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)으로 메일을 보내세요. 요청에는 반드시 구독 ID를 포함해야 합니다.  

![설정 중입니다.](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>응용 프로그램에 자동으로 다시 연결할 수 없습니다. 응용 프로그램을 다시 시작하세요.
이 오류 메시지는 Azure RemoteApp 사용 중 4시간 이상 절전 모드 상태였던 PC의 절전 상태를 해제하고, Azure RemoteApp 클라이언트에서 자동으로 다시 연결을 시도하고, 시간 제한이 초과될 때 자주 표시됩니다.  사용자에게 응용 프로그램으로 다시 돌아와서 Azure RemoteApp 클라이언트에서 열도록 지시합니다.

![응용 프로그램에 자동으로 다시 연결할 수 없습니다.](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>임시 프로필 문제
이 오류는 사용자 프로필(사용자 프로필 디스크)을 탑재하지 못하여 사용자가 임시 프로필을 받을 때 발생합니다.  관리자는 Azure Portal에서 컬렉션으로 이동한 다음 **세션** 탭으로 이동하고 사용자를 **로그오프**해야 합니다. 이렇게 하면 사용자 세션이 완전히 로그오프되어 사용자가 앱을 다시 실행할 수 있게 됩니다. 실패한 경우 Azure 지원에 문의하거나 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)으로 메일을 보내세요.

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp의 작동이 중지되었습니다.
이 오류 메시지는 Azure RemoteApp 클라이언트에 문제가 있어 다시 시작해야 함을 의미합니다. 사용자에게 닫도록 지시합니다. **프로그램 닫기**를 선택한 다음 Azure RemoteApp 클라이언트를 다시 시작합니다.  문제가 계속 발생하면 Azure 지원 티켓을 열거나 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)으로 메일을 보내세요.

![Azure RemoteApp의 작동이 중지되었습니다.](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>원격 데스크톱 연결이 이 리소스에 액세스하는 동안 오류가 발생했습니다. 연결을 다시 시도하거나 시스템 관리자에게 문의하십시오.
일반 오류 메시지입니다. Azure 지원에 문의하거나 조사할 수 있도록 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com)으로 메일을 보내세요. 

![일반 Azure RemoteApp 메시지](./media/remoteapp-apptrouble/ra-apptrouble4.png) 




<!--HONumber=Nov16_HO3-->


