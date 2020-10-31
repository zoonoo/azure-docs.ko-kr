---
title: ARM 템플릿을 사용 하 여 Azure API Management에 대 한 CI/CD
description: Azure API Management를 사용 하는 API DevOps 소개, Azure Resource Manager 템플릿을 사용 하 여 CI/CD 파이프라인에서 API 배포 관리
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 92d108304f788279a636b1dc5e1c4e6c103ede3d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088882"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하는 API Management에 대 한 CI/CD

이 문서에서는 Azure Resource Manager 템플릿을 통해 Azure API Management에서 API DevOps를 사용 하는 방법을 보여 줍니다. 전략적 가치를 갖춘 Api의 CI/CD (지속적인 통합 및 지속적인 배포) 파이프라인은 API 개발의 중요 한 측면입니다. 이를 통해 조직은 오류가 발생 하기 쉬운 수동 단계 없이 API 변경 배포를 자동화 하 고, 이전에 문제를 검색 하 고, 궁극적으로 사용자에 게 가치를 더 빠르게 제공할 수 있습니다 

이 문서에서 설명 하는 DevOps 방식을 구현 하는 세부 정보, 도구 및 코드 샘플은 GitHub의 오픈 소스 [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) 를 참조 하세요. 고객은 다양 한 엔지니어링 문화권과 기존 자동화 솔루션을 사용 하기 때문에 단일 크기의 모든 솔루션이 아닙니다.

## <a name="the-problem"></a>문제

현재 조직에는 일반적으로 여러 배포 환경 (예: 개발, 테스트 및 프로덕션)이 있으며 각 환경에 대해 별도의 API Management 인스턴스를 사용 합니다. 일부 인스턴스는 다양 한 릴리스 상황 다른 Api를 담당 하는 여러 개발 팀에서 공유 합니다.

따라서 고객은 다음과 같은 문제를 직면 하 게 됩니다.

* API Management Api 배포를 자동화 하는 방법
* 한 환경에서 다른 환경으로 구성을 마이그레이션하는 방법
* 동일한 API Management 인스턴스를 공유 하는 여러 개발 팀 간의 간섭을 방지 하는 방법

## <a name="manage-configurations-in-resource-manager-templates"></a>리소스 관리자 템플릿에서 구성 관리

다음 그림은 제안 된 접근 방식을 보여 줍니다. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="API Management를 사용 하 여 DevOps를 설명 하는 다이어그램입니다.":::

이 예제에는 *개발* 및 *프로덕션* 이라는 두 가지 배포 환경이 있습니다. 각에는 자체 API Management 인스턴스가 있습니다. 

* API 개발자는 개발 인스턴스에 액세스 하 여 Api를 개발 하 고 테스트 하는 데 사용할 수 있습니다. 
* *API 게시자* 를 호출 하는 지정 된 팀은 프로덕션 인스턴스를 관리 합니다.

이 제안 된 방법의 핵심은 [Azure Resource Manager 템플릿의](../azure-resource-manager/resource-group-authoring-templates.md)모든 API Management 구성을 유지 하는 것입니다. 조직에서는 이러한 템플릿을 Git와 같은 원본 제어 시스템에 보관 해야 합니다. 이미지에 설명 된 것 처럼 게시자 리포지토리에는 템플릿 컬렉션에 있는 프로덕션 API Management 인스턴스의 모든 구성이 포함 됩니다.

|템플릿  |설명  |
|---------|---------|
|서비스 템플릿     | 가격 책정 계층 및 사용자 지정 도메인과 같은 API Management 인스턴스의 서비스 수준 구성입니다.         |
|공유 템플릿     |  그룹, 제품 및로 거와 같은 API Management 인스턴스 전체에서 리소스를 공유 합니다.    |
|API 템플릿     |  Api 및 해당 하위 리소스 구성: 작업, 정책, 진단 설정.        |
|마스터 (주) 템플릿     |   모든 템플릿을 연결 하 고 순서 대로 배포 하 여 모든 항목을 함께 [연결](../azure-resource-manager/resource-group-linked-templates.md) 합니다. 모든 구성을 API Management 인스턴스에 배포 하려면 주 템플릿을 배포 합니다. 각 템플릿을 개별적으로 배포할 수도 있습니다.       |

API 개발자는 개발자 리포지토리로 게시자 리포지토리를 포크 하 고 해당 Api에 대 한 변경 작업을 수행 합니다. 대부분의 경우 api에 대 한 API 템플릿에 집중 하 고 공유 또는 서비스 템플릿을 변경할 필요가 없습니다.

## <a name="migrate-configurations-to-templates"></a>템플릿으로 구성 마이그레이션
API 개발자는 리소스 관리자 템플릿으로 작업 하는 경우에 직면 합니다.

* API 개발자는 [Openapi 사양](https://github.com/OAI/OpenAPI-Specification) 에서 작업 하는 경우가 많으며 리소스 관리자 스키마에 익숙하지 않을 수 있습니다. 수동으로 템플릿을 작성 하면 오류가 발생할 수 있습니다. 

   리소스 키트의 [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) 라는 도구는 개방형 api 사양 파일을 기반으로 api 템플릿 생성을 자동화 하는 데 도움이 됩니다. 또한 개발자는 API에 대 한 API Management 정책을 XML 형식으로 제공할 수 있습니다. 

* 이미 API Management를 사용 하 고 있는 고객의 경우에는 리소스 관리자 템플릿으로 기존 구성을 추출 하는 것이 좋습니다. 이러한 고객의 경우 리소스 키트의 [추출기](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) 라는 도구를 통해 API Management 인스턴스에서 구성을 추출 하 여 템플릿을 생성할 수 있습니다.  

## <a name="workflow"></a>워크플로

* Api 개발자가 API 개발 및 테스트를 완료 하 고 API 템플릿을 생성 한 후에는 변경 내용을 게시자 리포지토리에 병합 하기 위해 끌어오기 요청을 제출할 수 있습니다. 

* API 게시자는 끌어오기 요청의 유효성을 검사 하 고 변경 내용이 안전 하 고 규정을 준수 하는지 확인할 수 있습니다. 예를 들어 HTTPS만 API와 통신할 수 있는지 확인할 수 있습니다. 대부분의 유효성 검사는 CI/CD 파이프라인의 단계로 자동화할 수 있습니다.

* 변경 내용이 승인 되 고 병합 되 면 API 게시자는 일정에 따라 또는 주문형으로 프로덕션 인스턴스에 배포 하도록 선택할 수 있습니다. [GitHub 작업](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)또는 기타 도구를 사용 하 여 템플릿의 배포를 자동화할 수 있습니다.


이 방법을 사용 하면 조직에서 API Management 인스턴스에 대 한 API 변경 배포를 자동화할 수 있으며, 한 환경에서 다른 환경으로 쉽게 변경 내용을 승격 시킬 수 있습니다. Api 개발 팀 마다 다른 API 템플릿 및 파일 집합에 대해 작업을 수행 하므로 서로 다른 팀 간의 간섭을 방지할 수 있습니다.

## <a name="video"></a>비디오

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>다음 단계

- 추가 정보, 도구 및 샘플 템플릿은 오픈 소스 [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) 를 참조 하세요.