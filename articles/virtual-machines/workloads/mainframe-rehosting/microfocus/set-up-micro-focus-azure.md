---
title: Azure에 마이크로 포커스 엔터프라이즈 서버 4.0 및 엔터프라이즈 개발자 4.0 설치 | 마이크로 소프트 문서
description: Azure 가상 시스템(VM)에서 마이크로 포커스 개발 및 테스트 환경을 사용하여 IBM z/OS 메인프레임 워크로드를 다시 호스팅합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411186"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Azure에 마이크로 포커스 엔터프라이즈 서버 4.0 및 엔터프라이즈 개발자 4.0 설치

이 문서에서는 Azure에서 [마이크로 포커스 엔터프라이즈 서버 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) 및 마이크로 포커스 엔터프라이즈 개발자 [4.0을](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) 설정하는 방법을 보여 주며 있습니다.

Azure의 일반적인 워크로드는 개발 및 테스트 환경입니다. 이 시나리오는 매우 비용 효율적이고 배포및 분해가 용이하기 때문에 일반적입니다. 엔터프라이즈 서버를 통해 Micro Focus는 사용 가능한 가장 큰 메인프레임 리호스팅 플랫폼 중 하나를 만들었습니다. Windows 또는 Linux 가상 시스템(VM)을 사용하여 Azure에서 저렴한 x86 플랫폼에서 z/OS 워크로드를 실행할 수 있습니다.

> [!NOTE]
> 출시 예정: Azure VM에서 [마이크로 포커스 엔터프라이즈 서버 5.0을](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) 설정하는 방법에 대한 지침입니다.

이 설정은 이미 설치된 Microsoft SQL Server 2017을 사용하여 Azure 마켓플레이스에서 Windows Server 2016 이미지를 실행하는 Azure VM을 사용합니다. 이 설정은 Azure 스택에도 적용됩니다.

엔터프라이즈 서버에 대한 해당 개발 환경은 Microsoft Visual Studio 2017 이상, Visual Studio 커뮤니티(무료 다운로드) 또는 이클립스에서 실행되는 엔터프라이즈 개발자입니다. 이 문서에서는 Visual Studio 2017 이상 에 설치된 Windows Server 2016 가상 컴퓨터를 사용하여 배포하는 방법을 보여 주며 이 문서에서는 이를 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 필수 구성 조건을 확인하십시오.

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 마이크로 포커스 소프트웨어 및 유효한 라이센스 (또는 평가판 라이센스). 기존 마이크로 포커스 고객인 경우 Micro Focus 담당자에게 문의하십시오. 그렇지 않으면 [평가판을 요청합니다.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

- 엔터프라이즈 서버 [및 엔터프라이즈 개발자에](https://www.microfocus.com/documentation/enterprise-developer/#")대한 설명서를 가져옵니다.

> [!NOTE]
> 가장 좋은 방법은 Azure VM에 대한 액세스를 제어할 수 있도록 사이트 간 VPN(가상 사설망) 터널 또는 점프박스를 설정하는 것입니다.

## <a name="install-enterprise-server"></a>Enterprise Server 설치

1. 더 나은 보안 및 관리 용이성을 위해 이 프로젝트에 대한 새 리소스 그룹(예: **RGMicroFocusEntServer)을**만드는 것이 좋습니다. Azure에서 이름의 첫 번째 부분을 사용하여 목록에서 쉽게 찾을 수 있도록 리소스 유형을 선택합니다.

2. 가상 머신을 만듭니다. Azure 마켓플레이스에서 원하는 가상 컴퓨터 및 운영 체제를 선택합니다. 권장 설정은 다음과 같습니다.

    - **엔터프라이즈 서버**: Windows Server 2016 및 SQL Server 2017이 설치된 ES2 v3 VM(vCPU 2개 및 16GB 메모리 포함)을 선택합니다. 이 이미지는 Azure 마켓플레이스에서 사용할 수 있습니다. 엔터프라이즈 서버는 Azure SQL 데이터베이스도 사용할 수 있습니다.

    - **엔터프라이즈 개발자**: Windows 10 및 Visual Studio가 설치된 B2ms VM(vCPU 2개 및 8GB 메모리)을 선택합니다. 이 이미지는 Azure 마켓플레이스에서 사용할 수 있습니다.

3. **기본** 섹션에서 사용자 이름과 암호를 입력합니다. VM에 사용할 **구독** 및 **위치/지역을** 선택합니다. 리소스 그룹에 대한 **RGMicroFocusEntServer를** 선택합니다.

4. 두 VM을 동일한 가상 네트워크에 배치하여 서로 통신할 수 있습니다.

5. 나머지 설정에 대한 기본값을 수락합니다. 이러한 VM 관리자를 위해 만든 사용자 이름과 암호를 기억하십시오.

6. 가상 시스템이 만들어지면 인바운드 포트 9003, 86 및 80을 HTTP용 및 엔터프라이즈 서버 컴퓨터의 RDP용 3389및 개발자 컴퓨터에서 3389로 엽니다.

7. Azure 포털에서 엔터프라이즈 서버 가상 시스템에 로그인하려면 ES2 v3 VM을 선택합니다. **개요** 섹션으로 이동하여 **연결을** 선택하여 RDP 세션을 시작합니다. VM에 대해 만든 자격 증명을 사용하여 로그인합니다.

8. RDP 세션에서 다음 두 파일을 로드합니다. Windows를 사용하므로 파일을 RDP 세션으로 끌어서 놓을 수 있습니다.

    - **es\_40.exe**, 엔터프라이즈 서버 설치 파일.

    - **mflic**- 해당 라이센스 파일-엔터프라이즈 서버 없이 로드 되지 않습니다.

9. 설치를 시작하려면 파일을 두 번 클릭합니다. 첫 번째 창에서 설치 위치를 선택하고 최종 사용자 사용권 계약에 동의합니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/01-enterprise-server.png)

     설치가 완료되면 다음 메시지가 나타납니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>업데이트 확인

설치 후 Microsoft C++ 재배포 가능 및 .NET Framework와 같은 여러 필수 구성 조건이 엔터프라이즈 서버와 함께 설치되므로 추가 업데이트를 확인해야 합니다.

### <a name="upload-the-license"></a>라이센스 업로드

1. 마이크로 포커스 라이센스 관리를 시작합니다.

2. **마이크로 포커스 라이센스 관리자** \> **라이센스 관리** **시작을** \> 클릭한 다음 **설치** 탭을 클릭합니다. 예를 들어, **라이센스 파일에서**파일의 경우 VM에 이전에 업로드한 **mflic** 파일을 찾아보고 **라이센스 설치를 선택합니다.**

     ![마이크로 포커스 라이센스 관리 대화 상자](media/03-enterprise-server.png)

3. 엔터프라이즈 서버가 로드되는지 확인합니다. 이 URL을 <http://localhost:86/> 사용하여 브라우저에서 엔터프라이즈 서버 관리 사이트를 시작해 보십시오. 엔터프라이즈 서버 관리 페이지가 표시된 대로 표시됩니다.

     ![엔터프라이즈 서버 관리 페이지](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>개발자 컴퓨터에 엔터프라이즈 개발자 설치

1. 이전에 만든 리소스 그룹(예: **RGMicroFocusEntServer)을**선택한 다음 개발자 이미지를 선택합니다.

2. 가상 시스템에 로그인하려면 **개요** 섹션으로 이동하여 **연결**을 선택합니다. 이 로그인은 RDP 세션을 시작합니다. VM에 대해 만든 자격 증명을 사용하여 로그인합니다.

3. RDP 세션에서 다음 두 파일을 로드합니다(원하는 경우 끌어서 놓기).

    - **edvs2017.exe,** 엔터프라이즈 서버 설치 파일.

    - **mflic**, 해당 라이센스 파일 (엔터프라이즈 개발자없이로드되지 않습니다).

4. 설치를 시작 하려면 **edvs2017.exe** 파일을 두 번 클릭 합니다. 첫 번째 창에서 설치 위치를 선택하고 최종 사용자 사용권 계약에 동의합니다. 당신이 원하는 경우, 당신이 아마 필요합니다이 터미널 에뮬레이터를 **설치하려면 룸바 9.5 설치를** 선택합니다.

     ![비주얼 스튜디오 2017 설치 대화 상자에 대 한 마이크로 포커스 엔터프라이즈 개발자](media/04-enterprise-server.png)

5. 설정이 완료되면 다음 메시지가 나타납니다.

     ![성공적인 메시지 설정](media/05-enterprise-server.png)

6. 엔터프라이즈 서버에서와 마찬가지로 마이크로 포커스 라이센스 관리자를 시작합니다. **마이크로 포커스 라이센스 관리자** \> **라이센스 관리** **시작을** \> 선택하고 **설치** 탭을 클릭합니다.

7. 업로드할 라이센스 형식(라이선스 파일 또는 16자 라이센스 코드)을 선택합니다. 예를 들어, **라이센스 파일에서**파일의 경우 VM에 이전에 업로드한 **mflic** 파일을 찾아보고 **라이센스 설치를 선택합니다.**

     ![마이크로 포커스 라이센스 관리 대화 상자](media/07-enterprise-server.png)

엔터프라이즈 개발자가 로드하면 Azure에서 마이크로 포커스 개발 및 테스트 환경의 배포가 완료됩니다!

## <a name="next-steps"></a>다음 단계

- [은행 데모 응용 프로그램 설정](./demo.md)
- [Docker 컨테이너에서 엔터프라이즈 서버 실행](./run-enterprise-server-container.md)
- [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
