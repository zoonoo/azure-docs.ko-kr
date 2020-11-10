---
title: 빠른 시작- RHEL(Red Hat Enterprise Linux)에서 Azure VM 및 Virtual Machine Scale Set에 JBoss Enterprise Application(EAP) 배포
description: Red Hat JBoss EAP를 사용하여 엔터프라이즈 Java 애플리케이션을 Azure RHEL VM 및 Virtual Machine Scale Set에 배포하는 방법입니다.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135116"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에서 JBoss EAP를 사용하여 엔터프라이즈 Java 애플리케이션을 Azure에 배포

이러한 빠른 시작 템플릿에서는 [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)을 사용하여 [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform)를 Azure Virtual Machines(VM) 및 Virtual Machine Scale Sets에 배포하는 방법을 보여 줍니다. 배포의 유효성을 검사하는 Java 앱 샘플이 배포에 포함되어 있습니다. JBoss EAP는 오픈 소스 애플리케이션 서버 플랫폼이며, Java 애플리케이션에 대한 엔터프라이즈급 보안, 확장성 및 성능을 제공합니다. RHEL은 오픈 소스 OS(운영 체제) 플랫폼입니다. 이를 통해 모든 환경에서 기존 앱을 크기 조정하고 새 기술을 롤아웃할 수 있습니다. JBoss EAP 및 RHEL에는 모든 환경에서 엔터프라이즈 Java 애플리케이션을 빌드, 실행, 배포 및 관리하는 데 필요한 모든 항목이 포함되어 있습니다. 온-프레미스, 가상 환경 및 프라이빗, 퍼블릭 또는 하이브리드 클라우드를 위한 훌륭한 오픈 소스 솔루션입니다.

## <a name="prerequisite"></a>필수 요소 

* 활성 구독이 있는 Azure 계정. Azure 구독을 얻으려면 [Visual Studio 구독자에 대한 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)을 활성화하거나 [체험 계정](https://azure.microsoft.com/pricing/free-trial)을 만듭니다.

* JBoss EAP 설치- JBoss EAP에 대한 RHSM(Red Hat Subscription Management) 자격이 있는 Red Hat 계정이 있어야 합니다. 이 자격을 통해 Red Hat에서 테스트되고 인증된 JBoss EAP 버전을 다운로드할 수 있습니다.  EAP 자격이 없는 경우 시작하기 전에 [JBoss EAP 평가 구독](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)을 받으세요. 새 Red Hat 구독을 만들려면 [Red Hat 고객 포털](https://access.redhat.com/)로 이동하여 계정을 설정합니다.
F
* [Azure 명령줄 인터페이스](https://docs.microsoft.com/cli/azure/overview)

* RHEL 옵션 - PAYG(종량제) 또는 BYOS(Bring-Your-Own-Subscription) 중에서 선택합니다. BYOS를 사용하는 경우 빠른 시작 템플릿을 배포하기 전에 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image를 활성화해야 합니다.

## <a name="java-ee--jakarata-ee-application-migration"></a>Java EE/Jakarata EE 애플리케이션 마이그레이션

### <a name="migrate-to-jboss-eap"></a>JBoss EAP로 마이그레이션
JBoss EAP 7.2 및 7.3은 Java EE(Java Enterprise Edition) 8 및 Java EE 8 사양의 인증된 구현입니다. JBoss EAP는 HA(고가용성) 클러스터링, 메시징 및 분산 캐싱과 같은 기능에 대한 미리 구성된 옵션을 제공합니다. 또한 이를 통해 사용자가 JBoss EAP에서 제공하는 다양한 API 및 서비스를 사용하여 애플리케이션을 작성, 배포 및 실행할 수 있습니다.  JBoss EAP에 대한 자세한 내용은 [JBoss EAP 7.2 소개](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) 또는 [JBoss EAP 7.3 소개](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)를 참조하세요.

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP의 애플리케이션

* 웹 서비스 애플리케이션 - 웹 서비스는 서로 다른 소프트웨어 애플리케이션 간에 상호 운용할 수 있는 표준 수단을 제공합니다. 각 애플리케이션은 서로 다른 플랫폼과 프레임워크에서 실행될 수 있습니다. 이러한 웹 서비스는 내부 및 다른 유형의 하위 시스템 통신을 용이하게 합니다. 자세한 내용은 [EAP 7.2에서 웹 서비스 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) 또는 [EAP 7.3에서 웹 서비스 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)을 참조하세요.

* EJB(Enterprise Java Bean) 애플리케이션 - EJB 3.2는 안전하고 이식 가능한 분산형 트랜잭션 Java EE 및 Jakarta EE 애플리케이션을 개발하기 위한 API입니다. EJB는 Enterprise Beans라는 서버 쪽 구성 요소를 사용하여 재사용을 권장하는 분리된 방식으로 애플리케이션의 비즈니스 논리를 구현합니다. 자세한 내용은 [EAP 7.2에서 EJB 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) 또는 [EAP 7.3에서 EJB 애플리케이션 개발](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)을 참조하세요.

* Hibernate 애플리케이션 - 개발자와 관리자는 JBoss EAP를 사용하여 JPA(Java Persistence API)/Hibernate 애플리케이션을 개발하고 배포할 수 있습니다. Hibernate Core는 Java 언어용 개체 관계형 매핑 프레임워크입니다. 개체 지향 도메인 모델을 관계형 데이터베이스에 매핑하는 프레임워크를 제공하여 애플리케이션에서 데이터베이스와 직접 상호 작용하지 않도록 방지합니다. Hibernate Entity Manager는 [JPA 2.1 사양](https://www.jcp.org/en/jsr/overview)에서 정의한 프로그래밍 인터페이스 및 수명 주기 규칙을 구현합니다. 이 래퍼는 Hibernate Annotations와 함께 완성도 높은 Hibernate Core를 기반으로 하는 완전한(및 독립 실행형) JPA 솔루션을 구현합니다. Hibernate에 대한 자세한 내용은 [EAP 7.2 기반 JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) 또는 [EAP 7.3 기반 Jakarta Persistence](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api)를 참조하세요.

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat MTA(Migration Toolkit for Applications)
[Red Hat MTA(Migration Toolkit for Applications)](https://developers.redhat.com/products/mta/overview)는 Java 애플리케이션 서버용 마이그레이션 도구입니다. 이 도구를 사용하여 다른 앱 서버에서 JBoss EAP로 마이그레이션합니다. Java용 [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) 및 [VS Code(Visual Studio Code)](https://code.visualstudio.com/docs/languages/java)에 대한 IDE 플러그 인에서 작동합니다.  MTA는 다음과 같은 기능을 제공하는 무료 오픈 소스 도구입니다.
* 애플리케이션 분석 자동화
* 예상 작업량 지원
* 코드 마이그레이션 가속화
* 컨테이너화 지원
* Azure Workload Builder와 통합

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 JBoss EAP 마이그레이션
RHEL 기반 JBoss EAP에 대한 Azure Marketplace 제품은 Azure VM을 20분 이내에 설치하고 프로비저닝합니다. 이러한 제품은 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에서 액세스할 수 있습니다.

이 Marketplace 제품에는 요구 사항을 지원하기 위한 EAP 및 RHEL 버전의 다양한 조합이 포함됩니다. JBoss EAP는 항상 BYOS(Bring-Your-Own-Subscription)이지만, RHEL OS의 경우 BYOS 또는 PAYG(종량제) 중에서 선택할 수 있습니다. Azure Marketplace 제품에는 RHEL 기반 JBoss EAP를 독립 실행형 VM 또는 클러스터된 VM으로 사용할 수 있는 계획 옵션이 포함되어 있습니다.

* RHEL 7.7 VM 기반 JBoss EAP 7.2(PAYG)
* RHEL 8.0 VM 기반 JBoss EAP 7.2(PAYG)
* RHEL 8.0 VM 기반 JBoss EAP 7.3(PAYG)
* RHEL 7.7 VM 기반 JBoss EAP 7.2(BYOS)
* RHEL 8.0 VM 기반 JBoss EAP 7.2(BYOS)
* RHEL 8.0 VM 기반 JBoss EAP 7.3(BYOS)

Azure Marketplace 제품과 함께 Azure 마이그레이션 과정을 시작하는 데 사용할 수 있는 빠른 시작 템플릿이 있습니다. 이러한 빠른 시작에는 다양한 구성 및 버전 조합으로 RHEL 기반 JBoss EAP를 배포하기 위해 다음과 같이 미리 작성된 ARM(Azure Resource Manager) 템플릿과 스크립트가 포함되어 있습니다.

* LB(Load Balancer)
* 부하 분산 및 VM에 대한 개인 IP
* 단일 서브넷이 있는 VNET(Virtual Network)
* VM 구성(클러스터 또는 독립 실행형)
* Java 애플리케이션 샘플

이러한 템플릿에 대한 솔루션 아키텍처는 다음과 같습니다.

* 독립 실행형 RHEL VM 기반 JBoss EAP
* 여러 RHEL VM에서 클러스터된 JBoss EAP
* Azure Virtual Machine Scale Set를 사용하여 클러스터된 JBoss EAP

#### <a name="linux-workload-migration-for-jboss-eap"></a>JBoss EAP를 위한 Linux 워크로드 마이그레이션
Azure Workload Builder는 온-프레미스 Java 앱에 대한 POC(개념 증명), 평가 및 마이그레이션 프로세스를 Azure로 간소화합니다. Workload Builder는 Azure Migrate 검색 도구와 통합되어 JBoss EAP 서버를 식별합니다. 그런 다음, JBoss EAP 서버를 배포하는 Ansible 플레이북을 동적으로 생성합니다. Red Hat MTA 도구를 활용하여 서버를 다른 앱 서버에서 JBoss EAP로 마이그레이션합니다. 마이그레이션을 간소화하는 단계는 다음과 같습니다.
* 평가 - Azure VM 또는 Virtual Machine Scale Set를 사용하는 JBoss EAP 클러스터
* 평가 - 애플리케이션 및 인프라 검사
* 인프라 구성 - 워크로드 프로필 만들기
* 배포 및 테스트 - 워크로드 배포, 마이그레이션 및 테스트
* 배포 후 구성 - 데이터, 모니터링, 보안, 백업 등과 통합

## <a name="server-configuration-choice"></a>서버 구성 선택

RHEL VM을 배포하는 경우 PAYG 또는 BYOS 중에서 선택할 수 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com)의 이미지는 기본적으로 PAYG로 설정됩니다. 사용자 고유의 RHEL OS 이미지가 있는 경우 BYOS 유형 RHEL VM을 배포합니다. VM 또는 Virtual Machine Scale Set를 배포하기 전에 F Cloud Access를 통한 BYOS 자격이 RHSM 계정에 있는지 확인합니다.

JBoss EAP는 기능과 API 외에도 강력한 관리 기능을 애플리케이션에 제공합니다. 이러한 관리 기능은 JBoss EAP를 시작하는 데 사용되는 운영 모드에 따라 달라집니다. RHEL 및 Windows Server에서 지원됩니다. JBoss EAP는 개별 인스턴스를 관리하기 위한 독립 실행형 서버 운영 모드를 제공합니다. 또한 단일 제어 지점에서 인스턴스 그룹을 관리하기 위한 관리형 도메인 운영 모드도 제공합니다. 참고: HA(고가용성) 기능이 Azure 인프라 서비스에서 관리되므로 JBoss EAP 관리형 도메인은 Microsoft Azure에서 지원되지 않습니다. *EAP_HOME* 이라는 환경 변수는 JBoss EAP 설치 경로를 나타내는 데 사용됩니다.

**독립 실행형 서버로 JBoss EAP 시작** - 다음 명령은 독립 실행형 모드에서 EAP 서비스를 시작하는 방법입니다.

```
$EAP_HOME/bin/standalone.sh
```
    
이 시작 스크립트는 EAP_HOME/bin/standalone.conf 파일을 사용하여 JVM 옵션과 같은 일부 기본 설정을 지정합니다. 이 파일에서 설정을 사용자 지정할 수 있습니다. JBoss EAP는 기본적으로 standalone.xml 구성 파일을 사용하여 독립 실행형 모드에서 시작하지만 다른 모드를 사용하여 시작할 수 있습니다. 사용 가능한 독립 실행형 구성 파일 및 이를 사용하는 방법에 대한 자세한 내용은 [EAP 7.2용 독립 실행형 서버 구성 파일](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 또는 [EAP 7.3용 독립 실행형 서버 구성 파일](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) 섹션을 참조하세요. 다른 구성을 사용하여 JBoss EAP를 시작하려면 --server-config 인수를 사용합니다. 예를 들면 다음과 같습니다.
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
사용 가능한 모든 시작 스크립트 인수 및 해당 용도에 대한 전체 목록은 --help 인수를 사용하거나 [EAP 7.2의 서버 런타임 인수](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) 또는 [EAP 7.3의 서버 런타임 인수](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) 섹션을 참조하세요.
    
또한 JBoss EAP는 클러스터 모드에서 작동할 수도 있습니다. 자세한 내용은 [EAP 7.2의 클러스터 개요](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) 또는 [EAP 7.3의 클러스터 개요](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)를 참조하세요. JBoss EAP 클러스터 메시징을 사용하면 JBoss EAP 메시징 서버를 그룹화하여 메시지 처리 부하를 공유할 수 있습니다. 클러스터의 각 활성 노드는 자체 메시지를 관리하고 자체 연결을 처리하는 활성 JBoss EAP 메시징 서버입니다.

## <a name="support-and-subscription-notes"></a>지원 및 구독 정보
이러한 빠른 시작 템플릿은 다음과 같이 제공됩니다. 

- RHEL OS는 Red Hat RHEL Gold Image 모델을 통해 PAYG 또는 BYOS로 제공됩니다.
- JBoss EAP는 BYOS로만 제공됩니다.

#### <a name="using-rhel-os-with-payg-model"></a>PAYG 모델에서 RHEL OS 사용

이러한 빠른 시작 템플릿은 기본적으로 Azure Gallery의 주문형 RHEL 7.7 또는 8.0 PAYG 이미지를 사용합니다. PAYG 이미지에는 일반적인 컴퓨팅, 네트워크 및 스토리지 비용을 기반으로 하는 추가 시간당 RHEL 구독 요금이 청구됩니다. 이와 동시에 인스턴스가 Red Hat 구독에 등록됩니다. 즉, 자격 중 하나를 사용하게 됩니다. 이 PAYG 이미지는 "이중 청구"로 이어집니다. 이 문제는 사용자 고유의 RHEL 이미지를 빌드하여 방지할 수 있습니다. 자세한 내용은 이 [Microsoft Azure용 RHEL VM을 프로비저닝하는 방법](https://access.redhat.com/articles/uploading-rhel-image-to-azure) Red Hat KB(기술 자료) 문서를 참조하세요. 또는 [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image를 활성화합니다.

PAYG VM 가격 책정에 대한 자세한 내용은 [Red Hat Enterprise Linux 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)을 참조하세요. PAYG 모델에서 RHEL을 사용하려면 [RHEL 7.7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) 또는 [RHEL 8.0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)에 대해 지정된 결제 방법을 사용하는 Azure 구독이 필요합니다. 이러한 제품을 사용하려면 Azure 구독에서 결제 방법을 지정해야 합니다.

#### <a name="using-rhel-os-with-byos-model"></a>BYOS 모델에서 RHEL OS 사용

BYOS를 RHEL OS에 사용하려면 Azure에서 RHEL OS를 사용할 수 있는 자격이 있는 유효한 Red Hat 구독이 있어야 합니다. 이 빠른 시작 템플릿을 배포하기 전에 다음 필수 구성 요소를 완료합니다.

1. RHEL OS 및 JBoss EAP 자격이 Red Hat 구독에 연결되어 있는지 확인합니다.
2. RHEL BYOS 이미지를 사용할 수 있는 권한을 Azure 구독 ID에 부여합니다. [RHSM(Red Hat Subscription Management) 설명서](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)에 따라 다음 단계가 포함된 프로세스를 완료합니다.

    2.1 Red Hat Cloud Access 대시보드에서 Microsoft Azure를 공급자로 사용하도록 설정합니다.

    2.2 Azure 구독 ID를 추가합니다.

    2.3 Microsoft Azure에서 새 Cloud Access 제품을 사용하도록 설정합니다.
    
    2.4 Azure 구독에 대한 Red Hat Gold Images를 활성화합니다. 자세한 내용은 [Cloud Access에 대한 구독 사용 및 유지 관리](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure) 챕터를 참조하세요.

    2.5 Azure 구독에서 Red Hat Gold Images를 사용할 수 있을 때까지 기다립니다. 이러한 Gold Images는 일반적으로 제출 후 3시간 이내에 사용할 수 있습니다.
    
3. RHEL BYOS 이미지에 대한 Azure Marketplace 사용 약관에 동의합니다. 이 프로세스는 아래와 같이 Azure CLI(명령줄 인터페이스) 명령을 실행하여 완료할 수 있습니다. 자세한 내용은 [Azure의 RHEL BYOS Gold Images](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) 설명서를 참조하세요. 최신 Azure CLI 버전을 실행해야 합니다.

    3.1 Azure CLI 세션을 시작하고, Azure 계정으로 인증합니다. 도움이 필요하면 [Azure CLI를 사용하여 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)을 참조하세요. 계속 진행하기 전에 최신 Azure CLI 버전을 실행하고 있는지 확인하세요.

    3.2 다음 CLI 명령을 실행하여 구독에서 RHEL BYOS 이미지를 사용할 수 있는지 확인합니다. 여기서 결과를 얻지 못하면 #2를 참조하고 Azure 구독이 RHEL BYOS 이미지에 대해 활성화되어 있는지 확인하세요.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 다음 명령을 실행하여 RHEL 7.7 BYOS 및 RHEL 8.0 BYOS 각각에 대한 Marketplace 사용 약관에 동의합니다.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. 이제 구독에서 RHEL 7.7 또는 8.0 BYOS를 Azure 가상 머신에 배포할 준비가 되었습니다.

#### <a name="using-jboss-eap-with-byos-model"></a>BYOS 모델에서 JBoss EAP 사용

JBoss EAP는 BYOS 모델을 통해서만 Azure에서 사용할 수 있습니다. 이 템플릿을 배포할 때 유효한 EAP 자격을 사용하여 RHSM 자격 증명을 RHSM 풀 ID와 함께 제공해야 합니다. EAP 자격이 없는 경우 시작하기 전에 [JBoss EAP 평가 구독](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)을 받으세요.

## <a name="how-to-consume"></a>사용 방법

다음 세 가지 방법으로 템플릿을 배포할 수 있습니다.

- PowerShell 사용 - 다음 명령을 실행하여 템플릿을 배포합니다. (Azure PowerShell 설치 및 구성에 대한 자세한 내용은 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)을 확인하세요.)

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Azure CLI 사용 - 다음 명령을 실행하여 템플릿을 배포합니다. (Azure CLI 설치 및 구성에 대한 자세한 내용은 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 확인하세요.)

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Azure Portal 사용 - 아래 섹션에서 설명한 대로 *Azure 빠른 시작 템플릿* 으로 이동하여 Azure Portal에 배포할 수 있습니다. 빠른 시작에서 **Azure에 배포** 또는 **GitHub에서 찾아보기** 단추를 클릭합니다.

## <a name="azure-quickstart-templates"></a>Azure 빠른 시작 템플릿

배포 목표를 충족하는 RHEL 기반 JBoss EAP 조합의 빠른 시작 템플릿 중 하나로 시작할 수 있습니다. 사용할 수 있는 빠른 시작 템플릿의 목록은 다음과 같습니다.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> RHEL 기반 JBoss EAP(독립 실행형 VM)</a> - Azure에서 JBoss-EAP라는 웹 애플리케이션을 RHEL 7.7 또는 8.0 VM에서 실행되는 JBoss EAP 7.2 또는 7.3에 배포합니다.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> RHEL 기반 JBoss EAP(클러스터된 다중 VM)</a> - eap-session-replication이라는 웹 애플리케이션을 'n' 번호 RHEL 7.7 또는 8.0 VM에서 실행되는 JBoss EAP 7.2 또는 7.3 클러스터에 배포합니다. 'n' 값은 사용자가 결정합니다. 모든 VM이 Load Balancer의 백 엔드 풀에 추가됩니다.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> RHEL 기반 JBoss EAP(클러스터된 Virtual Machine Scale Set)</a> - eap-session-replication이라는 웹 애플리케이션을 RHEL 7.7 또는 8.0 Virtual Machine Scale Set 인스턴스에서 실행되는 JBoss EAP 7.2 또는 7.3 클러스터에 배포합니다.

## <a name="resource-links"></a>리소스 링크:

* [Azure 하이브리드 혜택](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Azure App Service용 Java 앱 구성](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [Azure Red Hat OpenShift 기반 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure App Service Linux 기반 JBoss EAP](https://docs.microsoft.com/azure/app-service/quickstart-java) 빠른 시작
* [JBoss EAP를 Azure App Service에 배포하는 방법](https://github.com/JasonFreeberg/jboss-on-app-service) 자습서

## <a name="next-steps"></a>다음 단계

* [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)에 대해 자세히 알아보기
* [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)에 대해 자세히 알아보기
* [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)에 대해 자세히 알아보기
* [Azure의 Red Hat](https://aka.ms/rhel-docs) Microsoft 문서
* [Azure Marketplace에서 RHEL VM/Virtual Machine Scale Set에 JBoss EAP](https://aka.ms/AMP-JBoss-EAP) 배포
* [Azure 빠른 시작에서 RHEL VM/Virtual Machine Scale Set에 JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP) 배포
