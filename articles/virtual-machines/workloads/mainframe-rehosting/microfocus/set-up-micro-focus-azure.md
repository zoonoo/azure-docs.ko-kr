---
title: Azure에서 Micro Focus Enterprise 서버 5.0 및 Enterprise Developer 5.0 설치 | Microsoft Docs
description: 이 문서에서는 Microsoft Azure에 마이크로 포커스 엔터프라이즈 서버 5.0 및 Enterprise Developer 5.0를 설치하는 방법에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 5001f5df8ebab3e8366a2b31b1c5eb8ccc69c985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951354"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Azure에서 Micro Focus Enterprise 서버 5.0 및 Enterprise Developer 5.0 설치

이 문서에서는 Microsoft Azure에서 [Micro Focus Enterprise 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) 및 [Micro Focus Enterprise Developer 5.0](https://www.microfocus.com/documentation/enterprise-developer/ed50/)를 설정하는 방법을 보여줍니다.

Azure에서 일반적인 작업은 개발 및 테스트 환경입니다. 이 시나리오는 비용 효율적이고 쉽게 배포 및 분해되므로 일반적입니다. Micro Focus Enterprise Server는 사용할 수 있는 가장 큰 메인프레임 재호스팅 플랫폼 중 하나입니다. Windows 또는 Linux VM(가상 머신)을 사용하여 Azure에서 더 저렴한 x86 플랫폼에서 z/OS 워크로드를 실행할 수 있습니다.

이 설치 프로그램에서는 Microsoft SQL Server 2017이 이미 설치된 Azure Marketplace에서 Windows Server 2016 이미지를 실행하는 Azure VM을 사용합니다. 이 설정은 Azure Stack에도 적용됩니다.

Enterprise Server에 대한 해당 개발 환경은 Microsoft Visual Studio 2017 이상, Visual Studio Community(무료로 다운로드 가능) 또는 Eclipse에서 실행되는 Enterprise 개발자입니다. 이 문서에서는 Visual Studio 2017 이상과 함께 제공되는 Windows Server 2016 가상 머신을 사용하여 배포하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사전 요구 사항을 확인하세요.

-   Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

-   마이크로 포커스 소프트웨어 및 유효한 라이선스(또는 평가판 라이선스). 기존 마이크로 포커스 고객인 경우 마이크로 포커스 담당자에게 문의하세요. 그렇지 않은 경우 [평가판을 요청](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)합니다.

-   [Enterprise 서버 및 Enterprise 개발자](https://www.microfocus.com/documentation/enterprise-developer/ed50/)를 위한 설명서를 다운로드하세요.

    > [!Note]
    > VM에 대한 액세스를 제어하는 몇 가지 옵션이 있습니다.
    > -   모범 사례는 [Azure Bastion](https://azure.microsoft.com/services/azure-bastion/)을 설정하는 것입니다.
    > -   [사이트 간 VPN(가상 사설망)](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) 터널.
    > -   Jumpbox VM.

## <a name="install-enterprise-server"></a>Enterprise Server 설치

1.  보안 및 관리 효율성을 높이려면 이 프로젝트에 대해서만 새 리소스 그룹을 만드는 것이 좋습니다(예제: **RGMicroFocusEntServer**). Azure에서 이름의 첫 번째 부분을 사용하여 목록에서 보다 쉽게 찾을 수 있도록 리소스의 유형을 선택합니다.

2.  가상 머신을 만듭니다. Azure Marketplace에서 원하는 가상 머신 및 운영 체제를 선택합니다. 권장 설정은 다음과 같습니다.

    -   **엔터프라이즈 서버:** Windows Server 2016 및 SQL Server 2017이 설치된 **ES2 V3 VM**(2 개 vCPU와 16GB 메모리 포함)을 선택합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다. Enterprise 서버는 Azure SQL Database도 사용할 수 있습니다.

    -   **엔터프라이즈 개발자:** Windows 10 및 Visual Studio가 설치된 **B2MS VM**(2 개 vCPU와 8GB 메모리)을 선택합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다.

3.  **기본 사항** 섹션에서 사용자 이름 및 암호를 입력합니다. VM에 사용할 **구독** 및 **위치/지역을** 선택합니다. 리소스 그룹에 대한 **RGMicroFocusEntServer** 를 선택합니다.

4.  두 VM을 동일한 가상 네트워크에 배치하여 서로 통신할 수 있도록 합니다.

5.  나머지 설정은 기본값을 사용합니다. 이러한 VM의 관리자에 대해 만든 사용자 이름 및 암호를 잊지 마세요.

6.  가상 머신을 만든 경우 엔터프라이즈 서버 컴퓨터에서 인바운드 포트 **9003, 86**, HTTP **80** 및 Enterprise 서버 컴퓨터에서 원격 데스크톱 프로토콜(RDP) **3389** 를 열고, **3389** 를 개발자 컴퓨터에서 엽니다.

7.  Enterprise 서버 가상 머신에 로그인하려면 Azure Portal에서 ES2 v3 VM을 선택합니다. **개요** 섹션으로 이동하고 **연결** 을 선택하여 RDP 세션을 시작합니다. VM에 대해 만든 자격 증명을 사용하여 로그인합니다.

8.  RDP 세션에서 다음 두 파일을 로드합니다. Windows를 사용하고 있으므로 파일을 RDP 세션으로 끌어서 놓을 수 있습니다.

    -   `es\_50.exe`, Enterprise 서버 설치 파일입니다.

    -   `mflic`, 해당 라이선스 파일(Enterprise 서버는 이 파일 없이는 로드되지 않음).

9.  파일을 두 번 클릭하여 설치를 시작합니다. 첫 번째 창에서 설치 위치를 선택하고 최종 사용자 라이선스 계약에 동의합니다.

    ![스크린샷은 설치를 시작할 수 있는 Micro Focus Enterprise 서버 대화 상자를 표시합니다.](media/install-image-1.png)

    설치가 완료되면 다음과 같은 메시지가 표시됩니다.

    ![스크린샷에는 Micro Focus Enterprise 서버 대화 상자의 성공 메시지가 표시됩니다.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>업데이트 확인

설치 후에는 Microsoft c++ 재배포 가능 패키지 및 .NET Framework 같은 사전 요구 사항이 Enterprise 서버와 함께 설치되는 추가 업데이트를 확인해야 합니다.

### <a name="upload-the-license"></a>라이선스 업로드

1.  Micro Focus 라이선스 관리를 시작합니다.

2.   \> **Micro Focus 라이선스 관리자** \> **라이선스 관리** 시작을 선택하고 **설치** 탭을 클릭합니다. 업로드할 라이선스 형식 유형(라이선스 파일 또는 16 자 라이선스 코드)을 선택합니다. 예를 들어 파일의 경우 **라이선스 파일** 에서 이전에 VM에 업로드된 *`mflic` 파일로 이동한 후 **라이선스 설치** 를 선택합니다.

    ![스크린샷에는 설치 라이선스를 선택할 수 있는 Micro Focus 라이선스 관리 대화 상자가 표시됩니다.](media/install-image-3.png)

3.  Enterprise 서버가 로드되는지 확인합니다. 다음 URL `http://localhost:86/`을 사용하여 브라우저에서 Enterprise 서버 관리 사이트를 시작해 봅니다. 표시된 대로 Enterprise 서버 관리 페이지가 표시됩니다.

    ![Enterprise 서버 관리 페이지](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>개발자 컴퓨터에 Enterprise Developer 설치

1.  이전에 만든 리소스 그룹(예: **RGMicroFocusEntServer**)을 선택한 다음 개발자 이미지를 선택합니다.

2.  가상 머신에 로그인하려면 **개요** 섹션으로 이동하고 **연결** 을 선택합니다. 이 로그인은 RDP 세션을 시작합니다. VM에 대해 만든 자격 증명을 사용하여 로그인합니다.

3.  RDP 세션에서 다음 두 파일을 로드합니다(원하는 경우 끌어서 놓기).

    -   `edvs2017.exe`, Enterprise 서버 설치 파일입니다.

    -   `mflic`, 해당 라이선스 파일(Enterprise Developer는 이 파일 없이는 로드되지 않음).

4.  **edvs2017.exe** 파일을 두 번 클릭하여 설치를 시작합니다. 첫 번째 창에서 설치 위치를 선택하고 최종 사용자 라이선스 계약에 동의합니다. 원할 경우 **Rumba 9.5 설치** 를 선택하여 이 터미널 에뮬레이터를 설치합니다. 이 에뮬레이터는 필요할 수 있습니다.

    ![Visual Studio 2017 용 Micro Focus 엔터프라이즈 개발자 설치 대화 상자](media/install-image-5.png)

5.  설치가 완료되면 다음과 같은 메시지가 표시됩니다.

    ![설치 성공 메시지](media/install-image-6.png)

6.  엔터프라이즈 서버에 대해 수행한 것처럼 Micro Focus 라이선스 관리자를 시작합니다. **시작** \> **Micro Focus 라이선스 관리자** \> **라이선스 관리** 를 선택하고 **설치** 탭을 클릭합니다.

7.  업로드할 라이선스 형식 유형(라이선스 파일 또는 16 자 라이선스 코드)을 선택합니다. 예를 들어 파일의 경우 **라이선스 파일** 에서 이전에 VM에 업로드된 `mflic` 파일로 이동한 후 **라이선스 설치** 를 선택합니다.

    ![Micro Focus 라이선스 관리 대화 상자를 시작합니다](media/install-image-7.png)

Enterprise 개발자가 로드되면 Azure에서 Micro Focus 개발 및 테스트 환경의 배포가 완료되었습니다!

**다음 단계**

-   [BankDemo 애플리케이션 설정](./demo.md)

-   [Docker 컨테이너에서 Enterprise Server 실행](./run-enterprise-server-container.md)

-   [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)