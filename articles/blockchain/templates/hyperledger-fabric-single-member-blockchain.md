---
title: Hyperledger 패브릭 컨소시엄
description: Hyperledger 패브릭 컨소시엄 솔루션 템플릿을 사용하여 단일 멤버 네트워크를 배포하고 구성
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: ee8057be98d18db5963a3e5f1ba1f8bd8d76fe05
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241595"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger 패브릭 단일 멤버 네트워크

Hyperledger 패브릭 컨소시엄 솔루션 템플릿을 사용하여 단일 멤버(다중 노드) 네트워크를 배포하고 구성할 수 있습니다.

이 문서를 읽고 나면:

- 블록체인, Hyperledger 패브릭 및 복잡한 컨소시엄 네트워크 아키텍처에 대한 실무 지식을 얻을 수 있습니다.
- Azure Portal에서 단일 멤버 Hyperledger 패브릭 컨소시엄 네트워크를 배포하고 구성하는 방법을 배울 수 있습니다.

## <a name="about-blockchain"></a>블록체인 정보

블록체인 커뮤니티를 처음 접하는 경우 이 자리는 Azure에서 이 기술을 쉽고 구성 가능한 방법으로 배울 수 있는 좋은 기회입니다. 블록체인은 비트코인의 근간이 되는 기술이지만, 단순히 가상 화폐를 구현하는 것보다 훨씬 많은 가능성을 갖고 있습니다. 블록체인은 기존 데이터베이스, 분산 시스템 그리고 공격에 대한 불변성, 안정성, 감사 및 복원력을 보장하는 보안 다자간 계산을 지원하는 암호화 기술의 복합체입니다. 여러 프로토콜이 여러 메커니즘을 사용하여 이러한 특성을 제공합니다. [Hyperledger 패브릭](https://github.com/hyperledger/fabric)은 이러한 프로토콜 중 하나입니다.

## <a name="consortium-architecture-on-azure"></a>Azure의 응용 프로그램 아키텍처

이 템플릿은 단일 조직(단일 멤버) 내 사용자를 위한 프로덕션을 테스트 및 시뮬레이션할 수 있는 토폴로지를 배포합니다. 이 배포는 단일 지역의 다중 노드 네트워크로 구성되며, 조만간 여러 지역으로 확장할 예정입니다.

네트워크 세 가지 형식의 노드로 구성됩니다.

1. **멤버 노드**: 네트워크 멤버를 등록하고 관리하는 패브릭 멤버 자격을 실행하는 노드입니다. 이 노드를 클러스터링하면 확장성과 가용성을 높일 수 있지만, 이 랩에서는 단일 멤버 노드를 사용하겠습니다.
2. **정렬자 노드**: 총 명령 브로드캐스트 또는 원자성 트랜잭션 같은 배달 보증을 구현하는 통신 서비스를 실행하는 노드입니다.
3. **피어 노드**: 트랜잭션을 커밋하고 분산 원장의 상태와 복사본을 유지하는 노드입니다.

## <a name="getting-started"></a>시작

시작하려면 여러 가상 머신과 표준 저장소 계정의 배포를 지원할 수 있는 Azure 구독이 필요합니다. Azure 구독이 없는 경우 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

기본적으로 대부분의 구독은 할당량을 늘리지 않아도 소규모 배포 토폴로지를 지원합니다. 한 멤버에 가능한 최소 배포 규모는 다음과 같습니다.

- 가상 머신 5개(5코어)
- VNet 1개
- 부하 분산 장치 1개
- 공용 IP 주소 1개

구독이 있으면 [Azure Portal](https://portal.azure.com)로 이동합니다. **+**, **블록체인**, **Hyperledger 패브릭 단일 멤버 블록체인**을 차례로 선택합니다.

![Hyperledger 패브릭 단일 멤버 블록체인 마켓플레이스 템플릿](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>배포

시작하려면 **Hyperledger 패브릭 단일 멤버 블록체인**을 선택하고 **만들기**를 클릭합니다. 그러면 마법사에서 **기본 사항** 블레이드가 열립니다.

템플릿 배포에서 다중 노드 네트워크를 구성하는 과정을 안내합니다. 배포 흐름은 기본 사항, 네트워크 구성, 패브릭 구성의 세 단계로 나뉩니다.

### <a name="basics"></a>기본 사항

**기본 사항** 블레이드 아래에서 구독, 리소스 그룹 등 배포에 대한 표준 매개 변수의 값 및 기본 가상 머신 속성을 지정합니다.

![기본 사항](./media/hyperledger-fabric-single-member-blockchain/basics.png)

매개 변수 이름| 설명| 허용되는 값|기본값
---|---|---|---
**리소스 접두사**| 배포된 리소스의 이름 지정에 대한 기준으로 사용되는 문자열.|6자 이하|해당 없음
**VM 사용자 이름**| 이 멤버에게 배포된 각 가상 머신의 관리자 사용자 이름.|1-64자|azureuser
**인증 유형**| 가상 머신을 인증하는 방법.|암호 또는 SSH 공개 키|암호
**암호(인증 형식 = 암호)**|배포된 각 가상 머신의 관리자 계정 암호. 암호에는 대문자 1자, 소문자 1자, 숫자 1개 및 특수 문자 1자 중 3가지가 포함되어야 합니다.<br /><br />처음에는 모든 VM의 암호가 동일하지만, 프로비전 후 암호를 변경할 수 있습니다.|12-72자|해당 없음
**SSH 키(인증 형식 = 공개 키)**|원격 로그인에 사용하는 보안 셸 키.||해당 없음
**IP 주소별 액세스 제한**|클라이언트 엔드포인트 액세스의 제한 여부를 결정하는 설정.|예/아니요| 아니요
**IP 주소 또는 서브넷 허용(IP 주소별 액세스 제한 = 예)**|액세스 제어가 사용되면 클라이언트 엔드포인트에 액세스할 수 있는 IP 주소 또는 IP 주소 집합.||해당 없음
**구독** |배포할 구독.
**리소스 그룹** |컨소시엄 네트워크를 배포하는 리소스 그룹.||해당 없음
**위치**: |첫 번째 멤버의 네트워크 풋프린트를 배포할 Azure 지역.

### <a name="network-size-and-performance"></a>네트워크 크기 및 성능

다음으로, **네트워크 크기 및 성능** 아래에서 멤버 자격 수, 정렬자, 피어 노드 등 컨소시엄 네트워크의 크기에 대한 입력을 지정합니다. 인프라 옵션 및 가상 머신 크기를 선택합니다.

![네트워크 크기 및 성능](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

매개 변수 이름| 설명| 허용되는 값|기본값
---|---|---|---
**멤버 자격 노드 수**|멤버 자격 서비스를 실행하는 노드 수. 멤버 자격 서비스에 대한 자세한 내용은 Hyperledger [설명서](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf)의 보안 및 멤버 자격 서비스를 확인하세요.<br /><br />이 값은 현재 노드 1개로 제한되어 있지만, 그 다음 버전에서는 클러스터링을 통해 규모 확장을 지원할 계획입니다.|1| 1
**정렬자 노드 수** |트랜잭션을 블록으로 정렬(구성)하는 노드 수.--> 이 명령문은 길고 장황합니다. 정렬 서비스에 대한 자세한 내용은 Hyperledger [설명서](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html)를 참조하세요.<br /><br />이 값은 현재 노드 1개로 제한되어 있습니다. |1 |1
**피어 노드 수**| 트랜잭션을 실행하고 원장의 상태와 복사본을 유지하는 컨소시엄 멤버 소유의 노드.<br /><br />정렬 서비스에 대한 자세한 내용은 Hyperledger [설명서](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)를 참조하세요.|3| 3-9
**저장소 성능**|배포된 각 노드를 지원하는 저장소 형식. 저장소에 대한 자세한 내용은 [Microsoft Azure Storage 소개](https://docs.microsoft.com/azure/storage/common/storage-introduction) 및 [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)를 참조하세요.|Standard 또는 Premium|Standard
**가상 머신 크기** |네트워크의 모든 노드에 사용되는 가상 머신 크기|Standard A,<br />Standard D,<br />Standard D-v2,<br />Standard F 시리즈,<br />Standard DS,<br />및 Standard FS|Standard D1_v2

### <a name="fabric-specific-settings"></a>패브릭 관련 설정

마지막으로, **패브릭 설정** 아래에서 패브릭과 관련된 구성 설정을 지정합니다.

![패브릭 설정](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

매개 변수 이름| 설명| 허용되는 값|기본값
---|---|---|---
**부트스트랩 사용자 이름**| 배포된 네트워크의 멤버 서비스에 등록될 초기 인증 사용자.|9자 이하|관리자
**패브릭 CA에 대한 부트스트랩 사용자 암호**|멤버 자격 노드로 가져온 패브릭 CA 계정을 보호하기 위해 사용되는 관리자 암호.<br /><br />암호에는 대문자 1자, 소문자 1자, 숫자 1개가 포함되어야 합니다.|12자 이상|해당 없음

### <a name="deploy"></a>배포

**요약** 블레이드에서는 지정한 입력을 검토하고 기본 배포 전 유효성 검사를 실행합니다.

![요약](./media/hyperledger-fabric-single-member-blockchain/summary.png)

법률 정보 및 개인정보취급방침을 검토하고 **구입**을 클릭하여 배포합니다. 프로비전되는 VM 수에 따라 배포 시간이 몇 분부터 수십 분까지 걸릴 수 있습니다.

### <a name="accessing-nodes"></a>노드 액세스

배포가 완료되면 **개요**가 표시됩니다.

![배포](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

이 화면이 자동으로 표시되지 않으면(배포가 실행 중인 동안 관리 포털 내에서 이동하면 화면이 표시되지 않을 수 있음) 언제든지 왼쪽 탐색 모음의 [리소스 그룹] 탭에서 찾을 수 있습니다. 1단계에서 입력한 리소스 그룹 이름을 클릭하면 **개요** 페이지로 이동됩니다.

[개요]에는 솔루션 템플릿을 통해 배포된 리소스가 모두 나열됩니다. 리소스를 마음대로 탐색할 수 있으며, 템플릿을 통해 생성한 _출력 매개 변수_도 이 화면에서 액세스할 수 있습니다. 이러한 출력 매개 변수는 Hyperledger 패브릭 네트워크에 연결할 때 유용한 정보를 제공합니다.

출력 매개 변수에 액세스하려면 먼저 [리소스 그룹] 블레이드에서 **배포** 탭을 클릭합니다. 그러면 배포 기록이 표시됩니다.

![배포 기록](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

[배포 기록]에서 목록의 첫 번째 배포를 클릭하여 세부 정보를 살펴봅니다.

![배포 세부 정보](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

세부 정보 화면에는 배포 요약 정보가 표시된 후 세 가지 유용한 출력 매개 변수가 표시됩니다.

- _API 엔드포인트_는 네트워크에 응용 프로그램을 배포한 후에 사용할 수 있습니다.
- _배포 접두사_라고도 하는 _접두사_는 리소스 및 배포를 고유하게 식별합니다. 명령줄 기반 도구를 사용할 때 사용됩니다.
- _SSH-TO-FIRST-VM_은 네트워크의 첫 번째 VM에 연결하는 데 필요한 모든 올바른 매개 변수가 포함된 미리 어셈블된 ssh 명령을 제공하며, Hyperledger 패브릭의 경우 패브릭-CA 노드입니다.

제공된 관리 사용자 이름 및 암호/SSH 키를 사용하여 SSH를 통해 각 노드의 가상 머신에 원격으로 연결할 수 있습니다. 노드 VM에는 자체의 공용 IP 주소가 없으므로 부하 분산 장치를 통해 이동하여 포트 번호를 지정해야 합니다. 첫 번째 트랜잭션 노드에 액세스하는 SSH 명령은 세 번째 템플릿 출력 **SSH-TO-FIRST-VM(샘플 배포의 경우: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`)입니다. 추가 트랜잭션 노드를 가져오려면 포트 번호를 1만큼 증가합니다(예를 들어 첫 번째 트랜잭션 노드는 포트 3000, 두 번째는 3001, 세 번째는 3002에 있음).

## <a name="next-steps"></a>다음 단계

이제 Hyperledger 컨소시엄 블록체인 네트워크에 대한 응용 프로그램 및 체인코드 개발에 집중할 준비가 되었습니다.
