---
title: Virtual network에서 Azure 스프링 클라우드 문제 해결
description: Azure 스프링 클라우드 가상 네트워크에 대 한 문제 해결 가이드
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698232"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>가상 네트워크에서 Azure Spring Cloud 문제 해결

이 문서는 가상 네트워크에서 Azure 스프링 클라우드를 사용 하는 경우 발생할 수 있는 다양 한 문제를 해결 하는 데 도움이 됩니다.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure 스프링 클라우드 서비스 인스턴스를 만드는 데 문제가 발생 했습니다.

Azure 스프링 클라우드의 인스턴스를 만들려면 가상 네트워크에 인스턴스를 배포할 수 있는 충분 한 권한이 있어야 합니다.  스프링 클라우드 서비스 인스턴스는 [가상 네트워크에 Azure 스프링 클라우드 서비스 권한을 부여](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)해야 합니다.

Azure Portal를 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하는 경우 Azure Portal 사용 권한이 확인 됩니다.

[Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하려면 다음을 확인 합니다.

- 구독이 활성 상태입니다.
- 이 위치는 Azure 스프링 클라우드에서 지원 됩니다.
- 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
- 리소스 이름이 명명 규칙을 준수합니다. 소문자, 숫자 및 하이픈만 포함 해야 합니다. 첫 글자는 문자여야 합니다. 마지막 글자는 문자 또는 숫자여야 합니다. 값에는 2 ~ 32 문자를 포함 해야 합니다.

리소스 관리자 템플릿을 사용 하 여 Azure 스프링 클라우드 서비스 인스턴스를 설정 하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/template-syntax.md)를 참조 하세요.

### <a name="common-creation-issues"></a>일반적인 생성 문제

| 오류 메시지 | 수정 방법 |
|------|------|
| Azure 스프링 클라우드에서 만든 리소스는 정책에 의해 허용 되지 않았습니다. | 네트워크 리소스는 사용자 고유의 가상 네트워크에 Azure 스프링 클라우드를 배포할 때 생성 됩니다. 이러한 만들기를 차단 하도록 [Azure Policy](../governance/policy/overview.md) 정의 되어 있는지 확인 하세요. 리소스를 만들지 못했습니다. 오류 메시지에서 찾을 수 있습니다. |
| 제공 된 서브넷이 경로 테이블과 연결 되어 있습니다. 연결을 해제 하십시오. | 현재는 기존 경로 테이블과 연결 된 서브넷에 Azure 스프링 클라우드를 배포 하는 것을 지원 하지 않습니다 .이를 분리 한 후 다시 시도 하세요. |
| 필요한 트래픽은 나열 되지 않습니다. | [VNET에서 Azure 스프링 클라우드를 실행 하는 고객 책임](spring-cloud-vnet-customer-responsibilities.md) 을 참조 하 여 필요한 트래픽이 나열 되어 있는지 확인 하세요. |

## <a name="my-application-cant-be-registered"></a>응용 프로그램을 등록할 수 없습니다.

사용자 지정 DNS 설정을 사용 하 여 가상 네트워크를 구성 하는 경우이 문제가 발생 합니다. 이 경우 Azure 스프링 클라우드에서 사용 하는 개인 DNS 영역은 비효율적입니다. 사용자 지정 DNS 서버에 업스트림 DNS 서버로 Azure DNS IP 168.63.129.16를 추가 합니다.

## <a name="other-issues"></a>기타 문제

[일반적인 Azure 스프링 클라우드 문제를 해결](./spring-cloud-troubleshoot.md)합니다.