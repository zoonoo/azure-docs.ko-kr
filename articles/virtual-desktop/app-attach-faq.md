---
title: Windows 가상 데스크톱 MSIX 앱 연결 FAQ-Azure
description: Windows 가상 데스크톱에 대 한 MSIX 앱 연결에 대 한 질문과 대답입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73fb9bf436c043e903977fafbb5a502e2edc5488
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518688"
---
# <a name="msix-app-attach-faq"></a>MSIX 앱 연결 FAQ

이 문서에서는 Windows 가상 데스크톱에 대 한 MSIX 앱 연결에 대해 자주 묻는 질문과 대답을 설명 합니다.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>MSIX 및 MSIX 앱 연결의 차이점은 무엇 인가요?

MSIX은 앱에 대 한 패키징 형식이 고, MSIX 앱 연결은 배포에 MSIX 개의 패키지를 제공 하는 기능입니다.

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 앱 연결에서 FSLogix를 사용 하나요?

MSIX 앱 연결에서는 FSLogix를 사용 하지 않습니다. 그러나 MSIX 앱 연결 및 FSLogix는 원활한 사용자 환경을 제공 하기 위해 함께 작동 하도록 설계 되었습니다.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Windows 가상 데스크톱 외부에서 MSIX 앱 연결을 사용할 수 있나요?

Windows 10 Enterprise에서 power MSIX 앱을 연결 하는 Api를 사용할 수 있습니다. 이러한 Api는 Windows 가상 데스크톱 외부에서 사용할 수 있습니다. 그러나 Windows 가상 데스크톱 외부에서 MSIX 앱 연결에 대 한 관리 평면이 없습니다.

## <a name="how-do-i-get-an-msix-package"></a>MSIX 패키지를 가져올 어떻게 할까요? 있나요?

소프트웨어 공급 업체는 MSIX 패키지를 제공 합니다. MSIX이 아닌 패키지를 MSIX으로 변환할 수도 있습니다. [기존 설치 관리자를 MSIX으로 이동 하는 방법](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)에 대해 자세히 알아보세요.

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX 앱 연결을 지 원하는 운영 체제는 무엇 인가요?

Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션 버전 2004 이상.

## <a name="is-msix-app-attach-currently-generally-available"></a>MSIX 앱 연결은 현재 일반 공급 되나요?

MSIX 앱 연결은 Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션 버전 2004 이상에 포함 되어 있습니다. 현재는 두 운영 체제를 모두 사용할 수 있습니다. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Windows 가상 데스크톱 외부에서 MSIX 앱 연결을 사용할 수 있나요?

MSIX 및 MSIX 앱 연결 Api는 Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션 버전 2004 이상에 포함 되어 있습니다. 현재는 MSIX 앱에 대 한 관리 소프트웨어를 Windows 가상 데스크톱 외부에서 제공 하지 않습니다.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>동일한 응용 프로그램의 두 버전을 동시에 실행할 수 있나요?

동일한 MSIX 응용 프로그램의 두 버전이 동시에 실행 되는 경우에는 각 앱에 대해 appxmanifest.xml 파일에 정의 된 MSIX 패키지 패밀리가 서로 달라 야 합니다.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>MSIX 앱 연결을 사용 하는 경우 자동 업데이트를 사용 하지 않도록 설정 해야 하나요?

예. MSIX 앱 연결은 MSIX 응용 프로그램에 대 한 자동 업데이트를 지원 하지 않습니다.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>사용 권한은 MSIX 앱 연결에서 어떻게 작동 하나요?

MSIX 앱 연결을 사용 하는 호스트 풀의 모든 Vm (가상 컴퓨터)에는 MSIX 이미지가 저장 된 파일 공유에 대 한 읽기 권한이 있어야 합니다. 또한 Azure Files 사용 하는 경우 RBAC (역할 기반 액세스 제어) 및 NTFS (새 기술 파일 시스템) 권한을 모두 부여 받아야 합니다.

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>HTTP 또는 HTTPs에 대해 MSIX 앱 연결을 사용할 수 있나요?

MSIX 앱 연결을 사용 하는 호스트 풀의 일부인 모든 Vm에는 MSIX 이미지가 저장 된 파일 공유에 대 한 읽기 권한이 있어야 합니다. Azure Files를 사용 하는 경우 RBAC와 NTFS 사용 권한을 모두 부여 해야 합니다.

## <a name="can-i-restage-the-same-msix-application"></a>동일한 MSIX 응용 프로그램을 다시 준비할 수 있나요?

예. 이미 다시 준비한 응용 프로그램을 다시 준비할 수 있으며,이로 인해 오류가 발생 하지 않습니다.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>MSIX 앱 연결에서 자체 서명 된 인증서를 지원 하나요?

HTTP 또는 HTTPs를 통한 MSIX 앱 연결을 사용 하는 것은 현재 지원 되지 않습니다.


## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 대해 자세히 알아보려면 [개요](what-is-app-attach.md) 와 [용어집](app-attach-glossary.md)을 확인 하세요. 그렇지 않으면 [앱 연결 설정](app-attach.md)을 시작 합니다.
