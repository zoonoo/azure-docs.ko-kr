---
title: "Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포 | Microsoft Docs"
description: "Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2a645e6ab50b7b764a56bb7a79ad5cf295881214
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Microsoft Azure에서 SAP S/4HANA 또는 BW/4HANA 배포
이 문서에서는 SAP CAL(SAP 클라우드 어플라이언스 라이브러리) 3.0을 통해 Microsoft Azure에서 S/4HANA를 배포하는 방법을 설명합니다. 프로세스 관점에서 동일한 방식으로 작동하는 BW/4HANA와 같은 다른 SAP HANA 기반 솔루션을 배포합니다. 다른 솔루션을 선택하면 됩니다.

> [!NOTE]
SAP 클라우드 어플라이언스 라이브러리에 대한 자세한 내용은 [해당 사이트의 홈페이지](https://cal.sap.com/)를 참조하세요. [SAP 클라우드 어플라이언스 라이브러리 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)에 대한 정보를 제공하는 SAP의 블로그도 있습니다.

## <a name="step-by-step-process-to-deploy-the-solution"></a>솔루션을 배포하는 단계별 프로세스

다음 스크린샷에서는 Azure에 S/4HANA를 배포하는 방법을 보여 줍니다. 이 프로세스는 BW/4HANA와 같은 다른 솔루션과 동일한 방식으로 작동합니다.

첫 번째 스크린샷에서는 Azure에서 사용할 수 있는 모든 SAP CAL HANA 기반 솔루션을 보여 줍니다. 하단의 **SAP S/4HANA 온-프레미스 버전**을 확인하세요.

![SAP 클라우드 어플라이언스 라이브러리 창 스크린샷](./media/cal-s4h/s4h-pic-1b.jpg)

먼저, 새 SAP CAL 계정을 만듭니다. **계정**에는 Azure에 대한 두 가지 옵션인 Microsoft Azure와 21Vianet에서 운영하는 Azure 옵션이 제공됩니다. 이 예제에서는 **Microsoft Azure**를 선택합니다.

![SAP 클라우드 어플라이언스 라이브러리 계정 창 스크린샷](./media/cal-s4h/s4h-pic-2.jpg)

그런 다음 Azure Portal에서 찾을 수 있는 Azure 구독 ID를 입력합니다. 그런 다음 Azure 관리 인증서를 다운로드합니다.

![SAP 클라우드 어플라이언스 라이브러리 계정 창 스크린샷](./media/cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Azure 구독 ID를 찾으려면 최신 Azure Portal이 아닌 Azure 클래식 포털을 사용해야 합니다. 이 모델에 대해 SAP CAL이 아직 도입되지 않았으며 관리 인증서를 작동하려면 여전히 클래식 포털이 필요하기 때문입니다.

다음 스크린샷은 클래식 포털을 보여 줍니다. **설정**에서 **구독** 탭을 선택하여 SAP CAL 계정 창에 입력해야 하는 구독 ID를 확인합니다.

![Azure 클래식 포털 스크린샷](./media/cal-s4h/s4h-pic4b.jpg)

**설정**에서 **관리 인증서** 탭으로 전환합니다. 관리 인증서를 업로드하여 SAP CAL에 고객 구독 내에 가상 컴퓨터를 만들 수 있는 권한을 제공합니다. (SAP CAL에서 이전에 다운로드했던 관리 인증서를 업로드하게 됩니다.)

![Azure 클래식 포털, 관리 인증서 탭 스크린샷](./media/cal-s4h/s4h-pic5.jpg)

다운로드한 인증서 파일을 선택하라는 대화 상자가 나타납니다.

![관리 인증서 업로드 대화 상자 스크린샷](./media/cal-s4h/s4h-pic8.jpg)

인증서를 업로드했으면 SAP CAl 내에서 SAP CAL과 Azure 구독 간의 연결을 테스트할 수 있습니다. 연결이 유효함을 나타내는 메시지가 나타납니다.

![SAP 클라우드 어플라이언스 라이브러리 계정 창 스크린샷](./media/cal-s4h/s4h-pic9.jpg)

다음으로 배포할 솔루션을 선택하고 인스턴스를 만듭니다.
인스턴스 이름을 입력하고 Azure 지역을 선택한 후 솔루션에 대한 마스터 암호를 정의합니다.

![SAP 클라우드 어플라이언스 라이브러리 솔루션 창 스크린샷](./media/cal-s4h/s4h-pic10.jpg)

솔루션의 크기 및 복잡성에 따라 잠시 후에(예측값은 SAP CAL에서 제공됨) 활성으로 표시되고 사용할 준비가 됩니다.

![SAP 클라우드 어플라이언스 라이브러리 인스턴스 창 스크린샷](./media/cal-s4h/s4h-pic11.jpg)

배포된 VM 종류와 같은 솔루션에 대한 세부 정보가 표시될 수 있습니다. 이 경우 다양한 크기 및 용도의 Azure VM 3개가 생성되었습니다.

![SAP 클라우드 어플라이언스 라이브러리 인스턴스 창 스크린샷](./media/cal-s4h/s4h-pic12.jpg)

Azure 클래식 포털에서 이러한 가상 컴퓨터가 SAP CAL에 지정된 것과 동일한 인스턴스 이름으로 시작하는 것을 확인할 수 있습니다.

![Azure 클래식 포털의 가상 컴퓨터 3대를 보여 주는 스크린샷](./media/cal-s4h/s4h-pic13.jpg)

이제 SAP CAL 포털에서 연결 단추를 사용하여 솔루션에 연결할 수 있습니다. 솔루션에 사용할 모든 기본 자격 증명을 설명하는 사용자 가이드에 대한 링크가 대화 상자에 포함되어 있습니다.

![인스턴스에 연결 대화 상자 스크린샷](./media/cal-s4h/s4h-pic14b.jpg)

다른 옵션은 클라이언트 Windows VM에 로그인하고 예를 들어 미리 구성된 SAP GUI를 시작하는 것입니다.

![미리 구성된 SAP GUI 스크린샷](./media/cal-s4h/s4h-pic15.jpg)

