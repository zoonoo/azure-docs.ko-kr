---
title: Fortanix 기밀 컴퓨팅 관리자를 사용하여 애플리케이션을 실행하는 방법
description: Fortanix 기밀 컴퓨팅 관리자를 사용하여 컨테이너화된 이미지를 변환하는 방법을 알아봅니다.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: JenCook
ms.openlocfilehash: 91a3f0a38d1182e445eba39bf31092be17f9a1fd
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111055"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Fortanix 기밀 컴퓨팅 관리자를 사용하여 애플리케이션을 실행하는 방법

[Fortanix](https://www.fortanix.com/)에서 [Fortanix 기밀 컴퓨팅 관리자](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) 및 [Fortanix 노드 에이전트](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent)를 사용하여 Azure 기밀 컴퓨팅에서 애플리케이션 실행을 시작합니다.


Fortanix는 Azure 인프라를 기반으로 빌드된 제품 및 서비스를 제공하는 타사 소프트웨어 공급업체입니다. Azure에서 유사한 기밀 컴퓨팅 서비스를 제공하는 다른 타사 공급자도 있습니다.

> [!Note]
> Microsoft는 이 문서에서 언급되는 제품을 관리하지 않습니다. Microsoft는 편의상 관련 정보를 제공하며 타사 제품을 언급하는 것이 Microsoft의 보증을 의미하지는 않습니다.

이 자습서에서는 애플리케이션 이미지를 기밀 컴퓨팅 보호된 이미지로 변환하는 방법을 보여 줍니다. 이 환경은 Azure의 DCsv2 시리즈 Intel SGX 지원 가상 머신에서 지원하는 [Fortanix](https://www.fortanix.com/) 소프트웨어를 사용합니다. 이 솔루션은 ID 확인 및 데이터 액세스 제어와 같은 중요한 보안 정책을 오케스트레이션합니다.

Fortanix에 특정한 지원을 받으려면 [Fortanix Slack 커뮤니티](https://fortanix.com/community/)에 가입하고 `#enclavemanager` 채널을 사용합니다.

## <a name="prerequisites"></a>필수 조건

1. Fortanix 기밀 컴퓨팅 관리자 계정이 없는 경우 시작하기 전에 먼저 [등록](https://ccm.fortanix.com/auth/sign-up)합니다.
1. 변환된 애플리케이션 이미지를 푸시하는 프라이빗 [Docker](https://docs.docker.com/) 레지스트리입니다.
1. Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 종량제 구독으로 업그레이드하세요.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Fortanix 기밀 컴퓨팅 관리자에 애플리케이션 추가

1. [Fortanix CCM(Fortanix 기밀 컴퓨팅 관리자)](https://ccm.fortanix.com)에 로그인합니다.
1. **계정** 페이지로 이동하고 **계정 추가** 를 선택하여 새 계정을 만듭니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="계정을 만드는 방법을 보여 주는 스크린샷.":::

1. 계정을 만든 후 **선택** 을 눌러 새로 만든 계정을 선택합니다. 이제 컴퓨팅 노드 등록 및 애플리케이션 만들기를 시작할 수 있습니다.
1. **+ 애플리케이션** 단추를 선택하여 애플리케이션을 추가합니다. 이 예제에서는 Flask Server Enclave OS 애플리케이션을 추가합니다.

1. Enclave OS 애플리케이션의 **추가** 단추를 선택합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="애플리케이션을 추가하는 방법을 보여 주는 스크린샷.":::

   > [!NOTE]
   > 이 자습서에서는 Enclave OS 애플리케이션을 추가하는 방법만을 다룹니다. EDP Rust 애플리케이션을 Fortanix 기밀 컴퓨팅 관리자로 가져오는 방법에 대한[자세한 정보](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager)를 참조하세요.

1. 이 자습서에서는 샘플 애플리케이션에 Fortanix의 docker 레지스트리를 사용합니다. 다음 정보에서 세부 정보를 입력합니다. 프라이빗 docker 레지스트리를 사용하여 출력 이미지를 유지합니다.

    - **애플리케이션 이름**: Python 애플리케이션 서버
    - **설명**: Python Flask 서버
    - **입력 이미지 이름**: fortanix/python-flask
    - **출력 이미지 이름**: fortanx-private/python-flask-sgx
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **메모리 크기**: 1GB
    - **스레드 수**: 128

    *선택 사항*: 애플리케이션을 실행합니다.
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **앱**: fortanix/python-flask

      다음 명령 실행:

      ```bash
         sudo docker run fortanix/python-flask
      ```

1. 인증서를 추가합니다. 아래 세부 정보를 사용하여 정보를 입력하고 **다음** 을 선택합니다.
    - **도메인**: myapp.domain.dom
    - **형식**: 기밀 컴퓨팅 관리자가 발급한 인증서
    - **키 경로**: /appkey.pem
    - **키 형식**: RSA
    - **인증서 경로**: /appcert.pem
    - **RSA 키 크기**: 2048비트

## <a name="create-an-image"></a>이미지 만들기

Fortanix CCM 이미지는 소프트웨어 릴리스 또는 애플리케이션의 버전입니다. 각 이미지는 하나의 MRENCLAVE(enclave 해시)와 연결되어 있습니다.

1. **이미지 추가** 페이지에서 **출력 이미지 이름** 의 **레지스트리 자격 증명** 을 입력합니다. 자격 증명은 이미지가 푸시되는 프라이빗 docker 레지스트리에 액세스하는 데 사용됩니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="이미지를 만드는 방법을 보여 주는 스크린샷.":::

1. 이미지 태그를 제공하고 **만들기** 를 선택합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-tag.png" alt-text="태그를 추가하는 방법을 보여 주는 스크린샷.":::

## <a name="domain-and-image-allowlist"></a>도메인 및 이미지 허용 목록

허용 목록에 도메인이 추가되는 애플리케이션은 Fortanix 기밀 컴퓨팅 관리자에서 TLS 인증서를 가져옵니다. 마찬가지로, 변환된 이미지에서 애플리케이션을 실행하는 경우 Fortanix 기밀 컴퓨팅 관리자에 연결하려고 시도합니다. 그러면 애플리케이션은 TLS 인증서를 요청합니다.

왼쪽의 **작업** 탭으로 전환하고 보류 중인 요청을 승인하여 도메인 및 이미지를 허용합니다.

## <a name="enroll-compute-node-agent-in-azure"></a>Azure에서 컴퓨팅 노드 에이전트 등록

### <a name="generate-and-copy-join-token"></a>조인 토큰 생성 및 복사

Fortanix 기밀 컴퓨팅 관리자에서 토큰을 만듭니다. 이 토큰은 Azure의 컴퓨팅 노드에게 자신을 인증하도록 허용합니다. Azure 가상 머신에 이 토큰을 제공해야 합니다.

1. 관리 콘솔에서 **+ 노드 등록** 단추를 선택합니다.
1. **토큰 생성** 을 선택하여 조인 토큰을 생성합니다. 토큰을 복사합니다.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Azure Marketplace에서 노드를 Fortanix 노드 에이전트로 등록

Fortanix 노드 에이전트를 만들면 가상 머신, 네트워크 인터페이스, 가상 네트워크, 네트워크 보안 그룹 및 공용 IP 주소가 Azure 리소스 그룹에 배포됩니다. Azure 구독은 가상 머신에 대해 시간당 요금이 청구됩니다. Fortanix 노드 에이전트를 만들기 전에 DCsv2 시리즈에 대한 Azure [가상 머신 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 검토하세요. 사용하지 않는 경우 Azure 리소스를 삭제합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)로 이동하여 Azure 자격 증명으로 로그인합니다.
1. 검색 창에서 **Fortanix 기밀 컴퓨팅 노드 에이전트** 를 입력합니다. **Fortanix 기밀 컴퓨팅 노드 에이전트** 라는 검색 상자에 표시되는 앱을 선택하여 제품의 홈페이지로 이동합니다.

   ![마켓플레이스 검색](media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png)
1. 필요한 경우 **지금 가져오기** 를 선택하여 정보를 입력하고 **계속** 을 선택합니다. Azure Portal로 리디렉션됩니다.
1. **만들기** 를 선택하여 Fortanix 기밀 컴퓨팅 노드 에이전트 배포 페이지를 입력합니다.
1. 이 페이지에서 가상 머신을 배포하기 위한 정보를 입력합니다. 특히 이 VM은 Fortanix 노드 에이전트 소프트웨어가 설치된 Azure의 Intel SGX 지원 가상 머신 DCsv2 시리즈입니다. 노드 에이전트는 변환된 이미지가 Azure의 Intel SGX 노드에서 안전하게 실행될 수 있도록 합니다.  **구독** 을 선택하고 가상 머신 및 관련 리소스를 배포하려는 **리소스 그룹** 을 선택합니다.

   > [!NOTE]
   > Azure의 DCsv2 시리즈 가상 머신을 배포하는 경우 제약 조건이 있습니다. 추가 코어의 할당량을 요청해야 할 수도 있습니다. 자세한 내용은 [Azure VM의 기밀 컴퓨팅 솔루션](./virtual-machine-solutions.md)을 참조하세요.

1. 사용 가능한 지역을 선택합니다.
1. **노드 이름** 에 가상 머신의 이름을 입력합니다.
1. 가상 머신에 인증하는 데 사용할 사용자 이름과 암호(또는 SSH 키)를 입력합니다.
1. 기본 OS 디스크 크기를 200으로 유지하고 VM 크기를 선택합니다(이 자습서에서는 Standard_DC4s_v2면 충분함).
1. 이전에 생성된 토큰을 **조인 토큰** 에 붙여넣습니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="리소스를 배포하는 방법을 보여 주는 스크린샷.":::

1. **검토 + 생성** 를 선택합니다. 유효성 검사를 통과했는지 확인한 다음 **만들기** 를 선택합니다. 모든 리소스가 배포되면 이제 컴퓨팅 노드가 Fortanix 기밀 컴퓨팅 관리자에 등록됩니다.

## <a name="run-the-application-image-on-the-compute-node"></a>컴퓨팅 노드에서 애플리케이션 이미지 실행

다음 명령을 실행하여 애플리케이션을 실행합니다. 노드 IP, 포트 및 변환된 이미지 이름을 특정 애플리케이션에 대한 입력으로 변경해야 합니다.

이 자습서에서 실행할 명령은 다음과 같습니다.

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

위치:

- *52.152.206.164* 는 노드 에이전트 호스트 IP입니다.
- *9092* 는 노드 에이전트가 수신 대기하는 포트입니다.
- *fortanix-private/python-flask-sgx* 는 Fortanix 기밀 컴퓨팅 관리자 웹 포털의 **이미지** 테이블의 **이미지 이름** 열 아래 이미지 탭에서 찾을 수 있는 변환된 앱입니다.

## <a name="verify-and-monitor-the-running-application"></a>실행 중인 애플리케이션 확인 및 모니터링

1. [Fortanix 기밀 컴퓨팅 관리자](https://ccm.fortanix.com/console)로 돌아갑니다.
1. 노드를 등록한 **계정** 내에서 작업 중인지 확인합니다.
1. 왼쪽 탐색 창에서 맨 위 아이콘을 선택하여 **관리 콘솔** 로 이동합니다.
1. **애플리케이션** 탭을 선택합니다.
1. 연결된 컴퓨팅 노드를 사용하여 실행 중인 애플리케이션이 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 경우 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제하셔도 됩니다. 리소스 그룹을 삭제하면 변환된 이미지와 연결된 노드의 등록을 취소합니다.

가상머신에 대한 리소스 그룹을 선택한 다음 **삭제** 를 선택합니다. 리소스 그룹의 이름을 확인하고 리소스 삭제를 마칩니다.

만든 Fortanix 기밀 컴퓨팅 관리자 계정을 삭제하려면 Fortanix 기밀 컴퓨팅 관리자의 [계정 페이지](https://ccm.fortanix.com/accounts)로 이동합니다. 삭제하려는 계정을 마우스로 가리킵니다. 오른쪽 위 모서리에서 검은 세로 점선을 선택하고 **계정 삭제** 를 선택합니다.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="계정을 삭제하는 방법을 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Fortanix 도구를 사용하여 애플리케이션 이미지를 기밀 컴퓨팅 가상 머신을 기반으로 실행되도록 변환했습니다. Azure의 기밀 컴퓨팅 가상 머신에 대한 자세한 내용은 [Virtual Machines 솔루션](virtual-machine-solutions.md)을 참조하세요.

Azure의 기밀 컴퓨팅 제품에 대한 자세한 정보를 보려면 [Azure 기밀 컴퓨팅 개요](overview.md)를 참조하세요.

Azure에서 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 및 [Scone](https://sconedocs.github.io)과 같은 다른 타사 제품을 사용하여 유사한 작업을 완료하는 방법을 알아봅니다.