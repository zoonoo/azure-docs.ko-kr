---
title: 새로운 기능은 무엇입니까? 릴리스 정보-Azure Blockchain 서비스
description: 최신 릴리스 정보, 버전, 알려진 문제 및 예정 된 변경 사항과 같은 Azure Blockchain 서비스의 새로운 기능에 대해 알아봅니다.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 80ece6cb6bb81b7ce168da997603e17d1238171b
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921889"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Azure Blockchain 서비스의 새로운 기능

> 이 URL을 복사 하 고 붙여 넣는 방법으로 업데이트에 대 한이 페이지를 다시 방문 하는 시기에 대 한 알림 받기: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` rss 피드 판독기 [ ![ rss 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us).

Azure Blockchain 서비스는 지속적으로 향상 된 기능을 받습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 새로운 기능
- 버전 업그레이드
- 알려진 문제

---

## <a name="june-2020"></a>2020년 6월

### <a name="version-upgrades"></a>버전 업그레이드

- 2.6.0으로 쿼럼 버전을 업그레이드 합니다. 버전 2.6.0를 사용 하 여 서명 된 개인 트랜잭션을 보낼 수 있습니다. 개인 트랜잭션 전송에 대 한 자세한 내용은 [쿼럼 API 설명서](https://docs.goquorum.com/en/latest/Getting%20Started/api/)를 참조 하세요.
- Tessera 버전을 0.10.5로 업그레이드 합니다.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>계약 크기 및 트랜잭션 크기가 128 KB로 증가

형식: 구성 변경

계약 크기 (MaxCodeSize)가 128 KB로 증가 하 여 더 큰 크기의 스마트 계약을 배포할 수 있습니다. 또한 트랜잭션 크기 (txnSizeLimit)가 128 KB로 증가 했습니다. 구성 변경 내용은 6 월 19 2020 이후 Azure blockchain 서비스에서 만든 새 컨소시엄에 적용 됩니다.

### <a name="trietimeout-value-reduced"></a>TrieTimeout 값 감소

형식: 구성 변경

메모리 내 상태가 디스크에 더 자주 기록 되도록 TrieTimeout 값이 줄었습니다. 값이 낮을수록 노드가 충돌 하는 드문 경우에 노드의 복구 속도를 높일 수가 있습니다.

## <a name="may-2020"></a>2020년 5월

### <a name="version-upgrades"></a>버전 업그레이드

- Ubuntu 버전을 18.04로 업그레이드
- 2.5.0로 쿼럼 버전 업그레이드
- Tessera 버전 업그레이드 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure Blockchain 서비스가 rawPrivate 트랜잭션 보내기를 지원 합니다.

형식: 기능

고객은 노드의 계정 외부에서 개인 트랜잭션에 서명할 수 있습니다.

### <a name="two-phase-member-provisioning"></a>2 단계 멤버 프로 비전

형식: 향상

두 단계는 기존 컨소시엄에서 구성원이 만들어지는 시나리오를 최적화 하는 데 도움이 됩니다. 멤버 인프라는 첫 번째 단계에서 프로 비전 됩니다. 두 번째 단계에서는 멤버가 blockchain과 동기화 됩니다. 2 단계 프로 비전은 시간 초과로 인 한 멤버 생성 오류를 방지 하는 데 도움이 됩니다.

## <a name="known-issues"></a>알려진 문제

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>eth estimateGas 함수는 쿼럼 v 2.6.0에서 예외를 throw 합니다.

쿼럼 v 2.6.0에서 추가 *값* 매개 변수를 제공 하지 않고 *eth* 함수를 호출 하면 *메서드 처리기* 가 예외를 손상 시킵니다. 쿼럼 팀에 알림이 표시 되었으며 7 월 2020 일에 수정이 예상 됩니다. 수정을 사용할 수 있을 때까지 다음 해결 방법을 사용할 수 있습니다.

- 성능에 영향을 줄 수 있으므로 *eth. estimateGas* 를 사용 하지 마십시오. Eth. estimateGas 성능 문제에 대 한 자세한 내용은 [estimateGas 함수를 호출 하면 성능이 저하](#calling-ethestimategas-function-reduces-performance)됩니다. 각 트랜잭션에 가스 값을 포함 합니다. 대부분의 라이브러리는 가스 값이 제공 되지 않은 경우 eth를 호출 하 여 쿼럼 v 2.6.0의 작동이 중단 되도록 합니다.
- *Eth*를 호출 해야 하는 경우에는 쿼럼 팀에서 추가 매개 변수 *값* 을 *0* 으로 전달 하 여 해결 방법을 제안 합니다.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>4 개 미만의 유효성 검사기 노드가 있는 경우 마이닝 중지

프로덕션 네트워크에는 네 개 이상의 유효성 검사기 노드가 있어야 합니다. 쿼럼은 IBFT 크래시 내결함성을 충족 하는 데 필요한 최소 4 개의 유효성 검사기 노드를 권장 합니다 (33+ 1). 4 개의 유효성 검사기 노드를 가져오려면 Azure Blockchain 서비스 *표준* 계층 노드가 두 개 이상 있어야 합니다. 표준 노드는 두 개의 유효성 검사기 노드로 프로 비전 됩니다.  

Azure Blockchain 서비스의 Blockchain 네트워크에 4 개의 유효성 검사기 노드가 없는 경우 네트워크에서 마이닝을 중지할 수 있습니다. 처리 된 블록에 대 한 경고를 설정 하 여 마이닝이 중지 된 것을 감지할 수 있습니다. 정상 네트워크에서 처리 된 블록은 5 분 마다 노드당 60 블록으로 처리 됩니다.

완화를 위해 Azure Blockchain 서비스 팀은 노드를 다시 시작 해야 합니다. 고객은 노드를 다시 시작 하기 위해 지원 요청을 열어야 합니다. Azure Blockchain 서비스 팀은 마이닝 문제를 자동으로 검색 하 고 수정 하는 작업을 수행 합니다.

프로덕션 등급 배포에 *표준* 계층을 사용합니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager에는 표준 계층 노드가 필요 합니다.

블록 체인 Data Manager를 사용 하는 경우 *표준* 계층을 사용 합니다. *기본* 계층에는 4gb의 메모리만 있습니다. 따라서 Blockchain Data Manager 및 해당 서비스에서 실행 되는 다른 서비스에 필요한 사용으로 확장할 수 없습니다.

개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>대량 잠금 해제 계정 호출로 인해 지역에서 충돌이 발생 합니다.

트랜잭션을 제출 하는 동안 많은 볼륨의 잠금 해제 계정 호출이 트랜잭션 노드에서 발생 하는 경우에는 오류가 발생할 수 있습니다. 따라서 수집 트랜잭션을 중지 해야 합니다. 그렇지 않으면 보류 중인 트랜잭션 큐가 늘어납니다.

Geth는 1 분 이내에 자동으로 다시 시작 됩니다. 노드에 따라 동기화를 수행 하는 데 시간이 오래 걸릴 수 있습니다. Azure Blockchain 서비스 팀은 동기화 시간을 단축 하는 보관 기능을 사용 하도록 설정 합니다.

Geth 충돌을 식별 하기 위해 응용 프로그램 로그의 Blockchain 메시지에서 오류 메시지에 대 한 로그를 확인할 수 있습니다. 보류 중인 트랜잭션이 증가 하는 동안 처리 된 블록이 감소 하는지 확인할 수도 있습니다.

이 문제를 완화 하려면 계정 잠금을 해제 하는 명령을 사용 하 여 서명 되지 않은 트랜잭션을 보내는 대신 서명 된 트랜잭션을 보냅니다. 이미 외부적으로 서명 된 트랜잭션의 경우 계정 잠금을 해제할 필요가 없습니다.

서명 되지 않은 트랜잭션을 보내려면 잠금 해제 명령에서 time 매개 변수로 0을 보내 무한 시간에 대 한 계정 잠금을 해제 합니다. 모든 트랜잭션이 전송 된 후에 다시 계정을 잠글 수 있습니다.  

다음은 Azure Blockchain 서비스에서 사용 하는 geth 매개 변수입니다. 이러한 매개 변수는 조정할 수 없습니다.

- 이스탄불 차단 기간: 5 초
- 바닥 가스 제한: 7억
- Ceil 가스 제한: 8억

### <a name="large-volume-of-private-transactions-reduces-performance"></a>대용량의 개인 트랜잭션이 있으면 성능이 저하 됩니다.

Azure Blockchain 서비스 기본 계층 및 사설 트랜잭션을 사용 하는 경우 Tessera가 충돌할 수 있습니다.

블록 체인 응용 프로그램 로그를 검토 하 고 메시지를 검색 하 여 Tessera 충돌을 검색할 수 있습니다 `Tessera crashed. Restarting Tessera...` .

충돌이 발생 하면 Azure Blockchain 서비스는 Tessera를 다시 시작 합니다. 다시 시작은 약 1 분이 걸립니다.

많은 양의 개인 트랜잭션을 전송 하는 경우 *표준* 계층을 사용 합니다. 개발, 테스트 및 개념 증명에 *기본* 계층을 사용합니다. 멤버를 만든 후 기본과 표준 간의 가격 책정 계층 변경은 지원되지 않습니다.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Eth estimateGas 함수를 호출 하면 성능이 저하 됩니다.

*Eth estimateGas 함수를* 여러 번 호출 하면 초당 트랜잭션이 크게 줄어듭니다. 각 트랜잭션 전송에 *eth. estimateGas* 함수를 사용 하지 마십시오. *Eth. estimateGas* 함수는 메모리를 많이 사용 합니다.

가능 하면 트랜잭션 제출에 대해 보수적인 가스 값을 사용 하 고 *eth. estimateGas*의 사용을 최소화 합니다.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>스마트 계약의 바인딩되지 않은 루프로 인 한 성능 저하

성능을 저하 시킬 수 있으므로 스마트 계약에서 바인딩되지 않은 루프를 사용 하지 마십시오. 자세한 내용은 다음 자료를 참조하세요.

- [바인딩되지 않은 루프 방지](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [스마트 계약 보안 모범 사례](https://github.com/ConsenSys/smart-contract-best-practices)
- [쿼럼에서 제공 하는 스마트 계약 지침](http://docs.goquorum.com/en/latest/Security/Framework/Decentralized%20Application/Smart%20Contracts%20Security/)
- [농담에서 제공 하는 가스 한도 및 루프에 대 한 지침](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)
