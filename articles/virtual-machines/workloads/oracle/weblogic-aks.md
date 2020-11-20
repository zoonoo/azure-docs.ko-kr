---
title: Azure Kubernetes Service에서 Oracle WebLogic 서버를 실행 하기 위한 솔루션은 무엇 인가요?
description: Azure Kubernetes 서비스에서 Oracle WebLogic 서버를 실행 하는 방법에 대해 알아봅니다.
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: a57d5a00fad9a17e01b96ebdf395fb7a4b857935
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968692"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Oracle WebLogic 서버를 실행 하기 위한 솔루션은 무엇 인가요?

이 페이지에서는 AKS (Azure Kubernetes Service)에서 Oracle WebLogic 서버 (WLS)를 실행 하기 위한 솔루션에 대해 설명 합니다. 이러한 솔루션은 Oracle 및 Microsoft에서 공동으로 개발 및 지원 됩니다.

Azure Virtual Machines에서 WebLogic 서버를 실행 하는 것도 가능 합니다. 이에 대 한 해결 방법은 [Microsoft 문서](/azure/virtual-machines/workloads/oracle/oracle-weblogic)에 설명 되어 있습니다.

WebLogic 서버는 전 세계에서 중요 업무용 엔터프라이즈 Java 응용 프로그램 중 일부를 실행 하는 최고의 Java 응용 프로그램 서버입니다. WebLogic 서버는 Oracle 소프트웨어 제품군의 미들웨어 기반을 형성 합니다. Oracle 및 Microsoft는 Azure에서 워크 로드를 최고의 클라우드 플랫폼으로 실행 하기 위한 선택 및 유연성을 가진 WebLogic 서버 고객의 역량을 강화 하기 위해 최선을 다하고 있습니다.

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AKS 인증 및 지원
WebLogic 서버는 AKS에서 제대로 실행 되도록 Oracle 및 Microsoft에 의해 인증 됩니다. AKS 솔루션의 WebLogic 서버는 Docker 및 Kubernetes 인프라에서 컨테이너 화 된 및 오케스트레이션 Java EE 응용 프로그램을 쉽게 실행할 수 있도록 하기 위한 것입니다. 솔루션은 안정성, 확장성, 관리 효율성 및 엔터프라이즈 지원에 중점을 두었습니다.

Weblogic 서버 클러스터는 WebLogic Kubernetes 연산자를 통해 Kubernetes에서 실행 되도록 완전히 활성화 됩니다 (여기에서 ' Operator ' 라고 함). 연산자는 표준 Kubernetes 연산자 패턴을 따릅니다. 달리 수동 작업을 자동화 하 고 운영 안정성 기능을 추가 하 여 Kubernetes에서 WebLogic 도메인 및 배포의 관리 및 운영을 간소화 합니다. 이 연산자는 Oracle WebLogic 서버 12c, Oracle Fusion 미들웨어 인프라 12c 이상을 지원 합니다. WebLogic 서버 12.2.1.3 및 12.2.1.4에 대 한 공식 Docker 이미지를 연산자와 테스트 했습니다. 연산자에 대 한 자세한 내용은 [Oracle의 공식 설명서](https://oracle.github.io/weblogic-kubernetes-operator/)를 참조 하세요.

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS의 WLS에 대 한 지침, 스크립트 및 샘플
AKS의 WebLogic 서버 인증 외에도 Oracle과 Microsoft는 AKS에서 WebLogic 서버를 실행 하기 위한 자세한 지침, 스크립트 및 샘플을 제공 합니다. 이 지침은 [운영자 설명서](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)의 Azure Kubernetes Service 샘플 섹션에 통합 되어 있습니다. 이 지침은 최대한 쉽게 AKS 배포에 대 한 프로덕션 WebLogic 서버를 만들기 위한 것입니다. 이 지침에서는 Oracle에서 제공 하는 공식 WebLogic 서버 Docker 이미지를 사용 합니다. 장애 조치 (Failover)는 Kubernetes 영구 볼륨 클레임을 통해 액세스 되는 Azure Files을 통해 이루어집니다. Azure 부하 분산 장치는 ' LoadBalancer ' 유형의 Kubernetes 서비스를 사용 하 여 프로 비전 할 때 지원 됩니다. 이 지침을 통해 높은 수준의 구성과 사용자 지정을 수행할 수 있습니다.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="샘플 스크립트를 사용 하 여 AKS에 WebLogic 서버를 배포할 수 있습니다.":::

이 지침에서는 현재 Docker 이미지 외부에 도메인을 배포 하 고 Oracle의 표준 Docker 이미지를 사용 하는 것으로 가정 합니다. Docker 이미지 내에서 도메인을 사용 하 여 사용자 지정 이미지를 사용 하도록 설정 하기 위한 지침을 추가 합니다. 추가 사용 편의성 및 Azure 서비스 통합은 나중에 Azure Virtual Machines 솔루션에서 Oracle WebLogic 서버 미러링의 Marketplace 제공을 통해 가능 합니다.

_이러한 솔루션은_ 사용자의 라이선스를 가져옵니다. Oracle을 사용 하 여 이미 적절 한 라이선스를 보유 하 고 있으며 Azure에서 제품을 실행할 수 있는 적절 한 라이선스를가지고 있다고 가정 합니다.

_엔지니어링 팀이 이러한 솔루션을 개발 하는 마이그레이션 시나리오에 대해 자세히 알아보려면 [이 짧은 설문 조사](https://aka.ms/wls-on-azure-survey) 를 작성 하 고 연락처 정보를 포함_ 합니다. 프로그램 관리자, 설계자 및 엔지니어가 곧 연락 하 여 가까운 공동 작업을 시작 합니다. 솔루션이 활성 초기 개발 중인 동안에는 마이그레이션 시나리오에 대 한 공동 작업 기회를 무료로 사용할 수 있습니다.

## <a name="deployment-architectures"></a>배포 아키텍처

Azure Kubernetes 서비스에서 Oracle WebLogic 서버를 실행 하기 위한 솔루션은 비교적 편리 하 게 다양 한 프로덕션 지원 배포 아키텍처를 사용 하도록 설정 합니다.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="AKS에서 복합 WebLogic 서버 배포가 사용 됩니다.":::

솔루션에서 고객에 게 제공 하는 기능 외에도 배포를 더욱 유연 하 게 사용자 지정할 수 있습니다. 응용 프로그램을 배포할 때 고객이 더 많은 Azure 리소스를 배포와 통합 하 게 됩니다. 고객은 솔루션 개선을 위해 설문 조사에 피드백을 제공 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure Kubernetes 서비스에서 Oracle WebLogic 서버 실행을 탐색 합니다.

> [!div class="nextstepaction"]
> [AKS에서 WLS 실행을 위한 지침, 스크립트 및 샘플](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes 연산자](https://oracle.github.io/weblogic-kubernetes-operator/)
