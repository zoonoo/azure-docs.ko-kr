---
title: HIPAA HITRUST 청사진 샘플 제어
description: HIPAA HITRUST 청사진 샘플의 매핑을 제어합니다. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure 정책에 매핑됩니다.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "75470748"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>HIPAA HITRUST 청사진 샘플의 제어 매핑

다음 문서에서는 Azure Blueprints HIPAA HITRUST 청사진 샘플이 HIPAA HITRUST 제어에 매핑되는 방법에 대해 자세히 설명합니다. 제어에 대한 자세한 내용은 [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

다음은 **HIPAA HITRUST** 제어에 대한 매핑입니다. 특정 컨트롤 매핑으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 많은 매핑된 컨트롤은 [Azure Policy](../../../policy/overview.md) 이니셔티브를 사용하여 구현됩니다. 전체 이니셔티브를 검토하려면 Azure Portal에서 **정책**을 열고 **정의** 페이지를 선택합니다. 그런 다음, **\[미리 보기\]: HIPAA HITRUST 제어 감사** 기본 제공 정책 이니셔티브를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../../../policy/overview.md) 정의와 연결되어 있습니다. 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../../../policy/how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수**는 정책 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 청사진 샘플에 대한 컨트롤과 Azure Policy 정의 간의 연결은 시간이 지남에 따라 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md)을 참조하세요.

## <a name="control-against-malicious-code"></a>악성 코드에 대한 제어

이 청사진을 사용하면 Azure Security Center에서 가상 머신의 누락된 엔드포인트 보호를 모니터링하고 Microsoft 맬웨어 방지 솔루션을 Windows 가상 머신에 적용하는 [Azure Policy](../../../policy/overview.md) 정의를 할당하여 악성 코드 방지를 포함하여 엔드포인트 보호를 관리할 수 있습니다.

- Windows Server용 기본 Microsoft IaaS 맬웨어 방지 확장 배포
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.
- 시스템 업데이트를 머신에 설치해야 합니다.


## <a name="management-of-removable-media"></a>이동식 미디어 관리

조직에서는 데이터 분류 수준에 따라 사용하기 전에 미디어(랩톱 포함)를 등록하고, 이러한 미디어를 사용하는 방법을 적절히 제한하며, 적절히 제거되거나 삭제될 때까지 적절한 수준의 물리적 및 논리적 보호(암호화 포함)를 적용된 정보가 포함된 미디어에 제공합니다.

- Data Lake Store 계정에서 암호화 필요
- SQL 관리형 인스턴스 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- 가상 머신에서 디스크 암호화를 적용해야 합니다.
- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.


## <a name="information-exchange-policies-and-procedures"></a>정보 교환 정책 및 절차

클라우드 서비스 공급자는 업계에서 인정된 가상화 플랫폼과 표준 가상화 형식(예: OVF(Open Virtualization Format))을 사용하여 상호 운용성을 보장하고, 사용 중인 하이퍼바이저와 고객 검토에 사용할 수 있는 모든 솔루션 관련 가상화 후크에 대한 사용자 지정 변경 내용을 문서화했습니다.

- 지정된 애플리케이션이 설치되지 않은 Windows VM을 감사하기 위한 필수 조건 적용

## <a name="control-of-operational-software"></a>운영 소프트웨어 제어 

조직에서는 서버, 워크스테이션, 랩톱 등 정보 시스템에서 권한이 없는 소프트웨어를 식별하는 한편, 권한이 없는 것으로 알려진 소프트웨어를 정보 시스템에서 실행할 수 없도록 모두 허용(allow-all), 예외별 거부(deny-by-exception) 정책을 실시하며 권한이 없는 소프트웨어 목록을 매년 한 번 이상 정기적으로 검토하고 업데이트합니다.

- \[미리 보기\] "보안 옵션 - 감사"에서 Windows VM 구성의 감사 결과 표시
- \[미리 보기\] "시스템 감사 정책 - 계정 관리"에서 Windows VM 구성의 감사 결과 표시

## <a name="change-control-procedures"></a>변경 제어 절차

모든 가상 머신 이미지의 무결성은 항상 가상 머신 이미지의 변경을 기록 및 경고하고, 비즈니스 소유자 및/또는 고객이 전자적 방법(예: 포털 또는 경고), 변경 또는 이동의 결과 및 이미지의 무결성에 대한 후속 유효성 검사를 통해 사용할 수 있도록 함으로써 보장됩니다.

- \[미리 보기\] '시스템 감사 정책 - 자세한 추적'에서 Windows VM 구성의 감사 결과 표시

## <a name="inventory-of-assets"></a>자산 인벤토리 

자산 및 서비스의 인벤토리가 유지 관리됩니다.

- Search 서비스의 진단 로그를 사용하도록 설정해야 합니다.
- \[미리 보기\] "보안 옵션 - Microsoft 네트워크 서버"에서 Windows VM 구성을 감사하도록 요구 사항 적용
- \[미리 보기\] "관리 템플릿 - 네트워크"에서 Windows VM 구성을 감사하도록 요구 사항 적용

## <a name="control-of-technical-vulnerabilities"></a>기술 취약성 제어 

모든 시스템 및 네트워크 구성 요소에 대해 강화된 구성 표준이 있습니다.

- 재해 복구가 구성되어 있지 않은 Virtual Machines 감사
- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.
- 취약성 평가 솔루션으로 취약성을 수정해야 합니다.

## <a name="segregation-in-networks"></a>네트워크 분리

조직의 보안 게이트웨이(예: 방화벽)는 보안 정책을 적용하고, 도메인 간의 트래픽을 필터링하고 무단 액세스를 차단하도록 구성되며, DMZ를 포함하여 내부 유선, 내부 무선 및 외부 네트워크 세그먼트(예: 인터넷) 간의 분리를 유지하고 각 도메인에 대한 액세스 제어 정책을 적용하는 데 사용됩니다.

- 보안 모니터링 에이전트의 자동 프로비저닝
- 가상 네트워크를 만들 때 Network Watcher 배포

## <a name="input-data-validation"></a>입력 데이터 유효성 검사

공용 웹 애플리케이션의 경우 트래픽을 제어하기 위해 애플리케이션 수준 방화벽을 구현합니다. 웹 기반이 아닌 공용 애플리케이션의 경우 조직에서는 애플리케이션 유형 특정의 네트워크 기반 방화벽을 구현했습니다. 공용 애플리케이션에 대한 트래픽이 암호화되면 디바이스에서 내부적으로 암호화하거나 분석하기 전에 트래픽의 암호를 해독할 수 있습니다.

- \[미리 보기\] "Windows 방화벽 속성"에서 Windows VM 구성의 감사 결과 표시


## <a name="network-connection-control"></a>네트워크 연결 제어

네트워크 트래픽은 조직의 액세스 제어 정책에 따라 방화벽 및 각 네트워크 액세스 지점 또는 외부 통신 서비스의 관리형 인터페이스에 대한 기타 네트워크 관련 제한 사항을 통해 제어됩니다.

- 인터넷 연결 엔드포인트를 통한 액세스를 제한해야 합니다.
- SQL 관리형 인스턴스 TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- API 앱에 대해 원격 디버깅을 해제해야 합니다.

## <a name="network-controls"></a>네트워크 제어

조직에서는 물리적 서버, 애플리케이션 또는 데이터를 가상화된 서버로 마이그레이션할 때 보안 및 암호화된 통신 채널을 사용합니다.

- VM에서 디스크 암호화를 적용해야 합니다.
- SQL Server TDE 보호기는 고유한 키를 사용하여 암호화해야 합니다.
- \[미리 보기\] "보안 옵션 - 네트워크 액세스"에서 Windows VM 구성의 감사 결과 표시
- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- \[미리 보기\] "Windows 방화벽 속성"에서 Windows VM 구성의 감사 결과 표시
- 네트워크 보안 그룹에 대한 진단 설정 배포
- 인터넷 연결 엔드포인트를 통한 액세스를 제한해야 합니다.

## <a name="sensitive-system-isolation"></a>중요한 시스템 격리

공유 시스템 리소스(예: 레지스터, 주 메모리, 보조 스토리지)는 시스템에 다시 릴리스되고, 다른 시스템/애플리케이션/사용자에게 공개되지 않도록 보호되며, 사용자는 의도적으로 또는 의도하지 않게 남아 있는 정보에 액세스할 수 없습니다.

- Virtual Machines를 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="security-of-network-services"></a>Network Services 보안

네트워크 서비스 공급자/관리자가 제공하는 합의된 서비스는 안전하게 제공되도록 공식적으로 관리 및 모니터링됩니다.

- Virtual Machines를 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.

## <a name="network-routing-control"></a>네트워크 라우팅 제어

라우팅 제어는 내부 및 외부 네트워크(예: 인터넷 및 타사 네트워크) 간에 사용되는 보안 게이트웨이(예: 방화벽)를 통해 구현됩니다.

- 가상 머신에서 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.

## <a name="information-exchange-policies-and-procedures"></a>정보 교환 정책 및 절차

조직에서는 외부 정보 시스템에서 권한 있는 개인이 조직에서 제어하는 휴대용 스토리지 미디어의 사용을 제한합니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.
- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.

## <a name="electronic-messaging"></a>전자 메시징

인스턴트 메시징 또는 파일 공유를 포함한 외부 퍼블릭 서비스를 사용하기 전에 승인을 받습니다.

- \[미리 보기\] 암호 파일 권한이 0644로 설정되지 않은 Linux VM의 감사 결과 표시

## <a name="on-line-transactions"></a>온라인 트랜잭션

조직에서는 트랜잭션과 관련된 각 당사자 간에 암호화 및 전자 서명을 사용하도록 요구합니다. 조직은 트랜잭션 세부 정보의 스토리지를 공개적으로 액세스할 수 있는 환경 외부(예: 조직의 인트라넷에 있는 스토리지 플랫폼)에 배치하여 인터넷에서 직접 액세스할 수 있는 스토리지 미디어에 보존 및 공개되지 않도록 합니다. 신뢰할 수 있는 기관이 사용되는 경우(예: 디지털 서명 및/또는 디지털 인증서를 발급하고 유지 관리하기 위해) 보안은 전체 엔드투엔드 인증서/서명 관리 프로세스 전체에 통합되어 포함됩니다.

- VM에서 디스크 암호화를 적용해야 합니다.
- \[미리 보기\] 신뢰할 수 있는 루트에 지정한 인증서가 포함되지 않은 Windows VM의 감사 결과 표시

## <a name="password-management"></a>암호 관리

암호는 모든 시스템 구성 요소에서 전송 및 저장 중에 암호화됩니다.

- \[미리 보기\] 암호 복잡성 설정이 사용되지 않는 Windows VM의 감사 결과 표시

## <a name="user-authentication-for-external-connections"></a>외부 연결에 대한 사용자 인증

다단계, Radius 또는 Kerberos(권한 있는 액세스용) 및 CHAP(전화 접속 방법에 대한 자격 증명 암호화용)와 같은 강력한 인증 방법은 조직 네트워크에 대한 모든 외부 연결에 구현됩니다.

- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- VM에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

## <a name="user-identification-and-authentication"></a>사용자 식별 및 인증

권한 있는 기능(예: 시스템 관리)을 수행한 사용자는 이러한 권한 있는 기능을 수행할 때 별도의 계정을 사용합니다. 다단계 인증 방법은 조직 정책(예: 원격 네트워크 액세스)에 따라 사용됩니다.

- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.
- VM에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.

## <a name="privilege-management"></a>권한 관리

가상화된 시스템을 호스팅하는 시스템의 관리 기능 또는 관리 콘솔에 대한 액세스는 최소 권한 원칙에 따라 담당자로 제한되고 기술 제어를 통해 지원됩니다.

- VM에서 Just-In-Time 네트워크 액세스 제어를 적용해야 합니다.
- \[미리 보기\] Kubernetes 서비스에서 RBAC(역할 기반 액세스 제어)를 사용해야 합니다.

## <a name="review-of-user-access-rights"></a>사용자 액세스 권한 검토

조직에서는 정보 자산에 대해 문서화된 권한 있는 사용자 목록을 유지 관리합니다.

- \[미리 보기\] "보안 옵션 - 계정"에서 Windows VM 구성의 감사 결과 표시

## <a name="remote-diagnostic-and-configuration-port-protection"></a>원격 진단 및 구성 포트 보호

비즈니스 기능에 특별히 필요하지 않은 컴퓨터 또는 네트워크 시스템에 설치된 포트, 서비스 및 유사한 애플리케이션은 사용하지 않도록 설정되거나 제거됩니다.

- 가상 머신에서 관리 포트를 닫아야 합니다.
- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.

## <a name="audit-logging"></a>감사 로깅

메시지의 날짜, 시간, 원본 및 대상을 포함하여 보내고 받은 메시지의 로그는 유지 관리되지만, 해당 내용은 그렇지 않습니다. 시스템이 활성화되어 있고 주요 이벤트, 데이터 액세스 성공/실패, 시스템 보안 구성 변경, 권한 있는 사용 또는 유틸리티 사용, 경보 발생, 보호 시스템(예: A/V 및 IDS) 활성화 및 비활성화, 식별/인증 메커니즘 활성화 및 비활성화, 시스템 수준 개체 만들기 및 삭제를 추적하는 동안 감사를 항상 사용할 수 있습니다.

- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.
- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.

## <a name="monitoring-system-use"></a>시스템 사용 모니터링

조직 환경 전체에 배포된 자동화 시스템은 주요 이벤트와 비정상적인 활동을 모니터링하고 시스템 로그를 분석하는 데 사용되며, 결과는 정기적으로 검토됩니다. 모니터링에는 권한 있는 작업, 권한이 부여된 액세스 또는 무단 액세스 시도가 포함됩니다(비활성화된 계정에 액세스하려는 시도, 시스템 경고 또는 오류 포함).

- Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 합니다.

## <a name="segregation-of-duties"></a>의무 분리

의무 분리는 정보 및 시스템의 무단 또는 의도하지 않은 수정의 위험을 제한하는 데 사용됩니다. 어떤 사용자도 권한 부여 또는 검색 없이 정보 시스템을 액세스하거나, 수정하거나, 사용할 수 없습니다. 관리 및 액세스 제어를 담당하는 개인에 대한 액세스는 각 사용자의 역할과 책임에 따라 최소한의 필요한 액세스로 제한되며, 이러한 개인은 이러한 제어와 관련된 감사 기능에 액세스할 수 없습니다.

- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="administrator-and-operator-logs"></a>관리자 및 운영자 로그

조직에서는 관리자 활동을 감사하기 위해 적절한 로깅을 사용하도록 설정하고, 시스템 관리자 및 운영자 로그를 정기적으로 검토합니다.

- 구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.

## <a name="identification-of-risks-related-to-external-parties"></a>외부 당사자와 관련된 위험 식별

조직과 외부 당사자 간의 원격 액세스 연결이 암호화됩니다.

- 가상 머신에서 디스크 암호화를 적용해야 합니다.

## <a name="business-continuity-and-risk-assessment"></a>비즈니스 연속성 및 위험 평가

조직에서는 중요한 비즈니스 프로세스를 식별하고, 비즈니스 연속성의 정보 보안 관리 요구 사항을 운영, 인력 관리, 자재, 운송 및 시설 측면과 관련된 다른 연속성 요구 사항과 통합합니다.

- \[미리 보기\] "보안 옵션 - 복구 콘솔"에서 Windows VM 구성의 감사 결과 표시

> [!NOTE]
> 특정 Azure Policy 정의의 가용성은 Azure Government 및 기타 국가 클라우드에 따라 다를 수 있습니다. 

## <a name="next-steps"></a>다음 단계

HIPAA HITRUST 청사진 샘플의 제어 매핑을 검토했습니다. 이번에는 다음 문서를 참조하여 이 샘플의 개요 및 배포 방법을 알아보세요.

> [!div class="next step action"]
> [HIPAA HITRUST 청사진 - 개요](./control-mapping.md)
> [HIPAA HITRUST 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
