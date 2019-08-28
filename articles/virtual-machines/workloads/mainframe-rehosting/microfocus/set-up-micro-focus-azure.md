---
title: Azure에서 마이크로 포커스 엔터프라이즈 서버 4.0 및 Enterprise Developer 4.0 설치 | Microsoft Docs
description: Azure Vm (가상 머신)에서 마이크로 포커스 개발 및 테스트 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 Rehost.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834561"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Azure에서 마이크로 포커스 엔터프라이즈 서버 4.0 및 Enterprise Developer 4.0 설치

이 문서에서는 Azure에서 [마이크로 포커스 엔터프라이즈 서버 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) 및 [마이크로 포커스 enterprise Developer 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) 를 설정 하는 방법을 보여 줍니다.

Azure에서 일반적인 작업은 개발 및 테스트 환경입니다. 이 시나리오는 비용 효율적이 고 쉽게 배포 및 분해 되기 때문에 일반적입니다. 엔터프라이즈 서버를 사용 하는 경우 마이크로 초점은 사용 가능한 가장 큰 메인프레임 재호스팅 플랫폼 중 하나를 만들었습니다. Windows 또는 Linux Vm (가상 머신)을 사용 하 여 Azure에서 더 저렴 한 x86 플랫폼에서 z/OS 워크 로드를 실행할 수 있습니다.

이 설치 프로그램에서는 Microsoft SQL Server 2017이 이미 설치 된 Azure Marketplace에서 Windows Server 2016 이미지를 실행 하는 Azure Vm을 사용 합니다. 이 설정은 Azure Stack에도 적용 됩니다.

Enterprise Server에 대 한 해당 개발 환경은 Microsoft Visual Studio 2017 이상, Visual Studio Community (무료로 다운로드 가능) 또는 Eclipse에서 실행 되는 엔터프라이즈 개발자입니다. 이 문서에서는 Visual Studio 2017 이상과 함께 제공 되는 Windows Server 2016 가상 컴퓨터를 사용 하 여 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

시작 하기 전에 다음 필수 구성 요소를 확인 하세요.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 마이크로 포커스 소프트웨어 및 유효한 라이선스 (또는 평가판 라이선스) 기존 마이크로 포커스 고객 인 경우 마이크로 포커스 담당자에 게 문의 하세요. 그렇지 않으면 [평가판을 요청](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)합니다.

- [엔터프라이즈 서버 및 엔터프라이즈 개발자](https://www.microfocus.com/documentation/enterprise-developer/#")를 위한 설명서를 다운로드 하세요.

> [!NOTE]
> Azure Vm에 대 한 액세스를 제어할 수 있도록 사이트 간 VPN (가상 사설망) 터널 또는 jumpbox를 설정 하는 것이 가장 좋습니다.

## <a name="install-enterprise-server"></a>Enterprise Server 설치

1. 보안 및 관리 효율성을 높이려면이 프로젝트에 대해서만 새 리소스 그룹을 만드는 것이 좋습니다 (예: **RGMicroFocusEntServer**). Azure에서 이름의 첫 번째 부분을 사용 하 여 목록에서 보다 쉽게 찾을 수 있도록 리소스의 유형을 선택 합니다.

2. 가상 머신을 만듭니다. Azure Marketplace에서 원하는 가상 컴퓨터 및 운영 체제를 선택 합니다. 권장 설정은 다음과 같습니다.

    - **엔터프라이즈 서버**: Windows Server 2016 및 SQL Server 2017이 설치 된 ES2 v3 VM (2 개 vCPUs와 16gb 메모리 포함)을 선택 합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다. 엔터프라이즈 서버는 Azure SQL Database 사용할 수 있습니다.

    - **엔터프라이즈 개발자**: Windows 10 및 Visual Studio가 설치 된 B2ms VM (2 개 vCPUs와 8gb 메모리)을 선택 합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다.

3. **기본 사항** 섹션에서 사용자 이름 및 암호를 입력 합니다. Vm에 사용할 **구독** 및 **위치/지역을** 선택 합니다. 리소스 그룹에 대해 **RGMicroFocusEntServer** 를 선택 합니다.

4. 두 Vm을 동일한 가상 네트워크에 배치 하 여 서로 통신할 수 있도록 합니다.

5. 나머지 설정에 대 한 기본값을 적용 합니다. 이러한 Vm의 관리자에 대해 만든 사용자 이름 및 암호를 잊지 마세요.

6. 가상 컴퓨터를 만든 경우 엔터프라이즈 서버 컴퓨터에서 RDP에 대 한 인바운드 포트 9003, 86 및 3389 80 및 개발자 컴퓨터의 3389를 엽니다.

7. 엔터프라이즈 서버 가상 머신에 로그인 하려면 Azure Portal에서 ES2 v3 VM을 선택 합니다. **개요** 섹션으로 이동 하 고 **연결** 을 선택 하 여 RDP 세션을 시작 합니다. VM에 대해 만든 자격 증명을 사용 하 여 로그인 합니다.

8. RDP 세션에서 다음 두 파일을 로드 합니다. Windows를 사용 하 고 있으므로 파일을 RDP 세션으로 끌어서 놓을 수 있습니다.

    - .40.exe, Enterprise Server 설치 파일입니다. **\_**

    - 해당 라이선스 파일인 **mflic**-Enterprise Server는이를 제외 하 고 로드 하지 않습니다.

9. 파일을 두 번 클릭 하 여 설치를 시작 합니다. 첫 번째 창에서 설치 위치를 선택 하 고 최종 사용자 사용권 계약에 동의 합니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/01-enterprise-server.png)

     설치가 완료 되 면 다음과 같은 메시지가 표시 됩니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>업데이트 확인

설치 후에는 Microsoft C++ 재배포 가능 패키지 및 .NET Framework 같은 필수 구성 요소가 엔터프라이즈 서버와 함께 설치 되므로 추가 업데이트를 확인 해야 합니다.

### <a name="upload-the-license"></a>라이선스 업로드

1. 마이크로 포커스 라이선스 관리를 시작 합니다.

2. **마이크로 포커스 라이선스** 관리자\> **라이선스 관리** **시작** \> 을 클릭 한 다음 **설치** 탭을 클릭 합니다. 업로드할 라이선스 형식 유형 (라이선스 파일 또는 16 자 라이선스 코드)을 선택 합니다. 예를 들어 파일의 경우 **라이선스 파일**에서 이전에 VM에 업로드 된 **mflic** 파일을 찾아 **라이선스 설치**를 선택 합니다.

     ![마이크로 포커스 라이선스 관리 대화 상자](media/03-enterprise-server.png)

3. Enterprise Server가 로드 되는지 확인 합니다. 이 URL <http://localhost:86/> 을 사용 하 여 브라우저에서 엔터프라이즈 서버 관리 사이트를 시작 해 봅니다. 표시 된 대로 엔터프라이즈 서버 관리 페이지가 표시 됩니다.

     ![엔터프라이즈 서버 관리 페이지](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>개발자 컴퓨터에 엔터프라이즈 개발자 설치

1. 이전에 만든 리소스 그룹 (예: **RGMicroFocusEntServer**)을 선택한 다음 개발자 이미지를 선택 합니다.

2. 가상 컴퓨터에 로그인 하려면 **개요** 섹션으로 이동 하 고 **연결**을 선택 합니다. 이 로그인은 RDP 세션을 시작 합니다. VM에 대해 만든 자격 증명을 사용 하 여 로그인 합니다.

3. RDP 세션에서 다음 두 파일을 로드 합니다 (원하는 경우 끌어서 놓기).

    - **Edvs2017**엔터프라이즈 서버 설치 파일입니다.

    - **mflic**, 해당 라이선스 파일 (Enterprise Developer는이 없이는 로드 되지 않음).

4. **Edvs2017** 파일을 두 번 클릭 하 여 설치를 시작 합니다. 첫 번째 창에서 설치 위치를 선택 하 고 최종 사용자 사용권 계약에 동의 합니다. 원할 경우 **Rumba 9.5 설치** 를 선택 하 여이 터미널 에뮬레이터를 설치 합니다 .이 에뮬레이터는 필요할 수 있습니다.

     ![Visual Studio 2017 용 마이크로 포커스 엔터프라이즈 개발자 설치 대화 상자](media/04-enterprise-server.png)

5. 설치가 완료 되 면 다음과 같은 메시지가 표시 됩니다.

     ![설치 성공 메시지](media/05-enterprise-server.png)

6. 엔터프라이즈 서버에 대해 수행한 것 처럼 마이크로 포커스 라이선스 관리자를 시작 합니다. \> 시작\> **마이크로 포커스 라이선스 관리자** **라이선스 관리**를 선택 하 고 **설치** 탭을 클릭 합니다.

7. 업로드할 라이선스 형식 유형 (라이선스 파일 또는 16 자 라이선스 코드)을 선택 합니다. 예를 들어 파일의 경우 **라이선스 파일**에서 이전에 VM에 업로드 된 **mflic** 파일을 찾아 **라이선스 설치**를 선택 합니다.

     ![마이크로 포커스 라이선스 관리 대화 상자](media/07-enterprise-server.png)

엔터프라이즈 개발자가 로드 되 면 Azure에서 마이크로 포커스 개발 및 테스트 환경의 배포가 완료 되었습니다.

## <a name="next-steps"></a>다음 단계

- [Bank Demo 응용 프로그램 설정](./demo.md)
- [Docker 컨테이너에서 엔터프라이즈 서버 실행](./run-enterprise-server-container.md)
- [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
