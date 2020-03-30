---
title: AKS 가상 시스템 호스트의 보안 강화
description: AKS VM 호스트 OS의 보안 강화에 대해 알아보기
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594383"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>AKS 가상 시스템 호스트의 보안 강화 

Azure Kubernetes 서비스(AKS)는 SOC, ISO, PCI DSS 및 HIPAA 표준을 준수하는 보안 서비스입니다. 이 문서에서는 AKS 가상 시스템 호스트에 적용되는 보안 강화에 대해 다룹니다. AKS 보안에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 응용 프로그램 및 클러스터에 대한 보안 개념을](https://docs.microsoft.com/azure/aks/concepts-security)참조하십시오.

AKS 클러스터는 보안에 최적화된 OS를 실행하는 호스트 가상 시스템에 배포됩니다. 이 호스트 OS는 현재 추가 보안 강화 단계 집합이 적용된 우분투 16.04.LTS 이미지를 기반으로 합니다(보안 강화 세부 정보 참조).   

보안 강화 호스트 OS의 목표는 공격의 표면적을 줄이고 안전한 방식으로 컨테이너를 배포할 수 있도록 하는 것입니다. 

> [!Important]
> 보안 강화 OS는 CIS벤치마킹되지 않습니다. CIS 벤치마크와 중복되는 것은 있지만 CIS를 준수하지 않는 것이 목표입니다. 호스트 OS 강화의 목표는 Microsoft자체의 내부 호스트 보안 표준과 일치하는 보안 수준으로 수렴하는 것입니다. 

## <a name="security-hardening-features"></a>보안 강화 기능 

* AKS는 기본적으로 보안에 최적화된 호스트 OS를 제공합니다. 대체 운영 체제를 선택할 수 있는 현재 옵션은 없습니다. 

* Azure는 AKS 가상 시스템 호스트에 일일 패치(보안 패치 포함)를 적용합니다. 이러한 패치 중 일부는 재부팅이 필요하지만 다른 패치는 재부팅하지 않습니다. 필요에 따라 AKS VM 호스트 재부팅을 예약할 책임은 있습니다. AKS 패치를 자동화하는 방법에 대한 지침은 [AKS 노드 패치를](https://docs.microsoft.com/azure/aks/node-updates-kured)참조하십시오.

다음은 보안에 최적화된 호스트 OS를 생성하기 위해 AKS-Engine에서 구현되는 이미지 강화 작업의 요약입니다. 이 작업은 [이 GitHub 프로젝트에서](https://github.com/Azure/aks-engine/projects/7)구현되었습니다.  

AKS-Engine은 현재 특정 보안 표준을 홍보하거나 준수하지 않지만 CIS(인터넷 보안 센터) 감사 IS는 해당되는 경우 편의를 위해 제공됩니다. 

## <a name="whats-configured"></a>어떤 구성이 필요한가요?

| Cis  | 감사 설명| 
|---|---|
| 1.1.1.1 |cramfs 파일 시스템의 장착이 비활성화되었는지 확인|
| 1.1.1.2 |freevxfs 파일 시스템의 장착이 비활성화되었는지 확인|
| 1.1.1.3 |jffs2 파일 시스템의 장착이 비활성화되었는지 확인|
| 1.1.1.4 |HFS 파일 시스템의 장착이 비활성화되었는지 확인|
| 1.1.1.5 |HFS Plus 파일 시스템의 장착이 비활성화되었는지 확인|
|1.4.3 |단일 사용자 모드에 필요한 인증 확인 |
|1.7.1.2 |로컬 로그인 경고 배너가 올바르게 구성되었는지 확인 |
|1.7.1.3 |원격 로그인 경고 배너가 올바르게 구성되었는지 확인 |
|1.7.1.5 |/etc/문제에 대한 사용 권한이 구성되었는지 확인 |
|1.7.1.6 |/etc/issue.net에 대한 사용 권한이 구성되어 있는지 확인 |
|2.1.5 |--스트리밍 연결 유휴 시간 설정이 0으로 설정되지 않았는지 확인 |
|3.1.2 |패킷 리디렉션이 비활성화되어 있는지 확인 |
|3.2.1 |소스 라우팅 패키지가 허용되지 않는지 확인 |
|3.2.2 |ICMP 리디렉션이 허용되지 않는지 확인 |
|3.2.3 |안전한 ICMP 리디렉션이 허용되지 않는지 확인 |
|3.2.4 |의심스러운 패킷이 기록되었는지 확인 |
|3.3.1 |IPv6 라우터 보급 알림이 허용되지 않는지 확인 |
|3.5.1 |DCCP를 사용하지 않도록 설정하지 않았는지 확인 |
|3.5.2 |SCTP를 사용하지 않도록 설정하지 않았는지 확인 |
|3.5.3 |RDS를 사용하지 않도록 설정하지 않았는지 확인 |
|3.5.4 |TIPC가 비활성화되어 있는지 확인 |
|4.2.1.2 |로깅이 구성되었는지 확인 |
|5.1.2 |/etc/crontab에 대한 사용 권한이 구성되었는지 확인 |
|5.2.4 |SSH X11 포워딩이 비활성화되었는지 확인 |
|5.2.5 |SSH MaxAuthtries가 4 이하로 설정되어 있는지 확인 |
|5.2.8 |SSH 루트 로그인이 비활성화되었는지 확인 |
|5.2.10 |SSH 허가사용자 환경이 비활성화되었는지 확인 |
|5.2.11 |승인된 MAX 알고리즘만 사용가능 |
|5.2.12 |SSH 유휴 시간 시간 간격이 구성되었는지 확인 |
|5.2.13 |SSH 로그인GraceTime이 1분 이하로 설정되어 있는지 확인 |
|5.2.15 |SSH 경고 배너가 구성되었는지 확인 |
|5.3.1 |암호 생성 요구 사항이 구성되었는지 확인 |
|5.4.1.1 |암호 만료기간이 90일 이하인지 확인 |
|5.4.1.4 |비활성 암호 잠금이 30일 이하인지 확인 |
|5.4.4 |기본 사용자 umask가 027 이상 제한적인지 확인 |
|5.6 |su 명령에 대한 액세스가 제한되었는지 확인|

## <a name="additional-notes"></a>추가적인 참고 사항
 
* 공격 표면적을 더욱 줄이기 위해 OS에서 불필요한 커널 모듈 드라이버가 비활성화되었습니다. 

* 보안 강화 OS는 AKS 플랫폼 외부에서 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계  

AKS 보안에 대한 자세한 내용은 다음 문서를 참조하십시오. 

[Azure Kubernetes 서비스(AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[AKS 보안 고려 사항](https://docs.microsoft.com/azure/aks/concepts-security)

[AKS 모범 사례](https://docs.microsoft.com/azure/aks/best-practices)
