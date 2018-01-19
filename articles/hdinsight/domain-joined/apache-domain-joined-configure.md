---
title: "도메인 가입 HDInsight 클러스터 구성 - Azure | Microsoft Docs"
description: "도메인에 가입된 HDInsight 클러스터를 설치 및 구성하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 0cbb49cc-0de1-4a1a-b658-99897caf827c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: saurinsh
ms.openlocfilehash: 4921e329c2ec8ce3d5bbf8a0851146e13d5f6cd3
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2018
---
# <a name="configure-domain-joined-hdinsight-sandbox-environment"></a>도메인 가입 HDInsight 샌드박스 환경 구성

독립 실행형 Active Directory 및 [Apache Ranger](http://hortonworks.com/apache/ranger/)로 Azure HDInsight 클러스터를 설치하여 강력한 인증 및 다양한 RBAC(역할 기반 액세스 제어) 정책을 활용하는 방법을 알아봅니다. 자세한 내용은 [도메인에 가입된 HDInsight 클러스터 소개](apache-domain-joined-introduction.md)를 참조하세요.

도메인 가입 HDInsight 클러스터가 없으면 각 클러스터는 Hadoop HTTP 사용자 계정과 SSH 사용자 계정만 사용할 수 있습니다.  다음을 사용하여 다중 사용자 인증을 수행할 수 있습니다.

-   Azure IaaS에서 실행되는 독립 실행형 Active Directory.
-   Azure Active Directory.
-   고객 온-프레미스 환경에서 실행되는 Active Directory.

이 문서에서는 Azure IaaS에서 독립 실행형 Active Directory를 사용하는 내용을 다룹니다. HDInsight에서 다중 사용자를 지원하기 위해 고객이 따라 할 수 있는 방법 중 가장 간단한 아키텍처입니다. 이 문서에는 이 구성에 대한 두 가지 방법을 다룹니다.

- 옵션 1: 하나의 Azure 리소스 관리 템플릿을 사용하여 독립 실행형 active directory와 HDInsight 클러스터를 모두 만듭니다.
- 옵션 2: 전체 프로세스가 다음과 같은 단계로 나뉩니다.
    - 템플릿을 사용하여 Active Directory를 만듭니다.
    - LDAPS를 설치합니다.
    - AD 사용자 및 그룹 만들기
    - HDInsight 클러스터 만들기

> [!IMPORTANT]
> 도메인에 연결된 HDInsight에서는 Oozie를 사용할 수 없습니다.

## <a name="prerequisite"></a>필수 요소
* Azure 구독

## <a name="option-1-one-step-approach"></a>옵션 1: 1단계 접근 방식
이 섹션에서는 Azure Portal에서 Azure 리소스 관리 템플릿을 엽니다. 이 템플릿은 독립 실행형 Active Directory와 HDInsight 클러스터를 만드는 데 사용됩니다. 현재 도메인 가입 Hadoop 클러스터, Spark 클러스터 및 대화형 쿼리 클러스터를 만들 수 있습니다.

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다. 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에 있습니다.
   
    Spark 클러스터를 만들려면:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fspark%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    대화형 쿼리 클러스터를 만들려면:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Finteractivequery%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Hadoop 클러스터를 만들려면:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/http%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fdomain-joined%2Fhadoop%2Ftemplate.json" target="_blank"><img src="../hbase/media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 값을 입력하고, **위에 명시된 사용 약관에 동의함**을 선택하고, **대시보드에 고정**을 선택한 다음 **구매**를 클릭합니다. 필드 옆에 있는 설명 기호 위로 마우스 커서를 이동하여 설명을 봅니다. 대부분의 값이 채워져 있습니다. 기본값을 사용해도 되고 원하는 값을 사용해도 됩니다.

    - **리소스 그룹**: Azure 리소스 그룹 이름을 입력합니다.
    - **위치**: 본인과 가까운 위치를 선택합니다.
    - **새 저장소 계정 이름**: Azure Storage 계정 이름을 입력합니다. 이 새 저장소 계정은 PDC, BDC 및 HDInsight 클러스터에서 기본 저장소 계정으로 사용됩니다.
    - **관리자 이름**: 도메인 관리자 이름을 입력합니다.
    - **관리자 암호**: 도메인 관리자 암호를 입력합니다.
    - **도메인 이름**: 기본 이름은 *contoso.com*입니다.  도메인 이름을 변경하는 경우 **보안 LDAP 인증서** 필드와 **조직 구성 단위 DN** 필드도 업데이트해야 합니다.
    - **클러스터 이름**: HDInsight 클러스터 이름을 입력합니다.
    - **클러스터 유형**: 이 값은 변경하지 마세요. 클러스터 유형을 변경하려면 마지막 단계의 특정 템플릿을 사용하세요.

    일부 값은 템플릿에 하드 코드됩니다. 예를 들어 작업자 노드 인스턴스 수는 2입니다.  하드 코드된 값을 변경하려면 **템플릿 편집**을 클릭합니다.

    ![HDInsight 도메인 가입 클러스터 템플릿 편집](./media/apache-domain-joined-configure/hdinsight-domain-joined-edit-template.png)

템플릿을 완료하면 리소스 그룹에 23개 리소스가 생성됩니다.

## <a name="option-2-multi-step-approach"></a>옵션 2: 다단계 접근 방식

이 섹션에는 네 단계가 있습니다.

1. 템플릿을 사용하여 Active Directory를 만듭니다.
2. LDAPS를 설치합니다.
3. AD 사용자 및 그룹 만들기
4. HDInsight 클러스터 만들기

### <a name="create-an-active-directory"></a>Active Directory 만들기

Azure Resource Manager 템플릿을 사용하면 보다 쉽게 Azure 리소스를 만들 수 있습니다. 이 섹션에서는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/active-directory-new-domain-ha-2-dc/)을 사용하여 가상 머신 2대가 있는 새 포리스트와 도메인을 만듭니다. 두 가상 머신은 주 도메인 컨트롤러와 백업 도메인 컨트롤러 역할을 수행합니다.

**두 개의 도메인 컨트롤러가 있는 도메인을 만들려면**

1. Azure 포털에서 템플릿을 열려면 다음 이미지를 클릭합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Factive-directory-new-domain-ha-2-dc%2Fazuredeploy.json" target="_blank"><img src="./media/apache-domain-joined-configure/deploy-to-azure.png" alt="Deploy to Azure"></a>

    템플릿은 다음과 같습니다.

    ![HDInsight 도메인 가입 포리스트 도메인 가상 머신 만들기](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-arm-template.png)

2. 다음 값을 입력합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹 이름**: 리소스 그룹 이름을 입력합니다.  리소스 그룹은 프로젝트와 관련된 Azure 리소스를 관리하는 데 사용됩니다.
    - **위치**: 본인과 가까운 Azure 위치를 선택합니다.
    - **관리자 이름**: 도메인 관리자 사용자 이름입니다. 이 사용자는 HDInsight 클러스터의 HTTP 사용자 계정이 아닙니다. 이 계정은 자습서 전체에서 사용하는 계정입니다.
    - **관리자 암호**: 도메인 관리자의 암호를 입력합니다.
    - **도메인 이름**: 도메인 이름은 두 부분으로 구성되어야 합니다. 예: contoso.com, contoso.local 또는 hdinsight.test.
    - **DNS 접두사**: DNS 접두사를 입력합니다.
    - **PDC RDP 포트**: (이 자습서에서는 기본값 사용)
    - **BDC RDP 포트**: (이 자습서에서는 기본값 사용)
    - **아티팩트 위치**: (이 자습서에서는 기본값 사용)
    - **아티팩트 위치 SAS 토큰**: (이 자습서에서는 비워 둡니다.)

리소스를 만드는 데 약 20분이 걸립니다.

### <a name="setup-ldaps"></a>LDAPS 설치

LDAP(Lightweight Directory Access Protocol)는 AD에서 읽고 쓰는 데 사용됩니다.

**원격 데스크톱을 사용하여 PDC에 연결하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 리소스 그룹을 열고 PDC(주 도메인 컨트롤러) 가상 머신을 엽니다. 기본 PDC 이름은 adPDC입니다. 
3. **연결**을 클릭하고 원격 데스크톱을 사용하여 PDC에 연결합니다.

    ![HDInsight 도메인 가입 PDC 원격 데스크톱 연결](./media/apache-domain-joined-configure/hdinsight-domain-joined-remote-desktop-pdc.png)


**Active Directory 인증서 서비스를 추가하려면**

4. 아직 열려 있지 않은 경우 **서버 관리자**를 엽니다.
5. **관리**를 클릭한 다음 **역할 및 기능 추가**를 클릭합니다.

    ![HDInsight 도메인 가입 역할 및 기능 추가](./media/apache-domain-joined-configure/hdinsight-domain-joined-add-roles.png)
5. "시작하기 전에" 페이지에서 **다음**을 클릭합니다.
6. **역할 기반 또는 기능 기반 설치**를 선택하고 **다음**을 클릭합니다.
7. PDC를 선택하고 **다음**을 클릭합니다.  기본 PDC 이름은 adPDC입니다.
8. **Active Directory 인증서 서비스**를 선택합니다.
9. 팝업 대화 상자에서 **기능 추가**를 클릭합니다.
10. 마법사를 따르고, 나머지 절차에서는 기본 설정을 사용합니다.
11. **닫기** 를 클릭하여 마법사를 닫습니다.

**AD 인증서를 구성하려면**

1. 서버 관리자에서 노란색 알림 아이콘을 클릭한 다음 **Active Directory 인증서 서비스 구성**을 클릭합니다.

    ![HDInsight 도메인 가입 AD 인증서 구성](./media/apache-domain-joined-configure/hdinsight-domain-joined-configure-ad-certificate.png)

2. 왼쪽에서 **역할 서비스**를 클릭하고, **인증 기관**을 선택하고, **다음**을 클릭합니다.
3. 마법사를 따르고, 나머지 절차에서는 기본 설정을 사용합니다(마지막 단계에서는 **구성** 클릭).
4. **닫기** 를 클릭하여 마법사를 닫습니다.

### <a name="optional-create-ad-users-and-groups"></a>(선택 사항) AD 사용자 및 그룹 만들기

**AD에서 사용자 및 그룹을 만들려면**
1. 원격 데스크톱을 사용하여 PDC에 연결
1. **Active Directory 사용자 및 컴퓨터**를 엽니다.
2. 왼쪽 창에서 도메인 이름을 선택합니다.
3. 상단 메뉴에서 **현재 컨테이너에 새 사용자를 만듭니다.** 아이콘을 클릭합니다.

    ![HDInsight 도메인 가입 사용자 만들기](./media/apache-domain-joined-configure/hdinsight-domain-joined-create-ad-user.png)
4. 지침에 따라 사용자 몇 명을 만듭니다. 예를 들어 hiveuser1 및 hiveuser2를 만듭니다.
5. 상단 메뉴에서 **현재 컨테이너에 새 그룹을 만듭니다.** 아이콘을 클릭합니다.
6. 지침에 따라 **HDInsightUsers**라는 그룹을 만듭니다.  이 그룹은 이 자습서의 뒷부분에서 HDInsight 클러스터를 만들 때 사용됩니다.

> [!IMPORTANT]
> 도메인 가입 HDInsight 클러스터를 만들려면 PDC 가상 머신을 다시 부팅해야 합니다.

### <a name="create-an-hdinsight-cluster-in-the-vnet"></a>VNet에서 HDInsight 클러스터 만들기

이 섹션에서는 Azure Portal을 사용하여 자습서의 앞부분에서 Resource Manager 템플릿을 통해 만든 가상 네트워크에 HDInsight 클러스터를 추가하겠습니다. 이 문서에서는 도메인 가입 클러스터 구성에 대한 정보만 다룹니다.  일반 정보는 [Azure Portal을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.  

**도메인 가입 HDInsight 클러스터를 만들려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 이 자습서의 앞부분에서 Resource Manager 템플릿을 사용하여 만든 리소스 그룹을 엽니다.
3. 리소스 그룹에 HDInsight 클러스터를 추가합니다.
4. **사용자 지정** 옵션을 선택합니다.

    ![HDInsight 도메인 가입 사용자 지정 옵션 만들기](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-custom-configuration-option.png)

    사용자 지정 구성 옵션을 사용하는 6개 섹션(기본, 저장소, 응용 프로그램, 클러스터 크기, 고급 설정, 요약)이 있습니다.
5. **기본** 섹션에서 할 일:

    - 클러스터 유형: **엔터프라이즈 보안 패키지**를 선택합니다. 현재 엔터프라이즈 보안 패키지는 Hadoop, 대화형 쿼리 및 Spark에만 사용할 수 있습니다.

        ![HDInsight 도메인 가입 엔터프라이즈 보안 패키지](./media/apache-domain-joined-configure/hdinsight-creation-enterprise-security-package.png)
    - 클러스터 로그인 사용자 이름: Hadoop HTTP 사용자입니다. 이 계정은 도메인 관리자 계정과 다릅니다.
    - 리소스 그룹: 앞에서 Resource Manager 템플릿을 사용하여 만든 리소스 그룹을 선택합니다.
    - 위치: Resource Manager 템플릿을 사용하여 vnet 및 DC를 만들 때 사용한 위치와 동일해야 합니다.

6. **고급 설정** 섹션에서 할 일:

    - 도메인 설정:

        ![HDInsight 도메인 가입 고급 설정 도메인](./media/apache-domain-joined-configure/hdinsight-domain-joined-portal-advanced-domain-settings.png)
        
        - 도메인 이름: [Active Directory 만들기](#create-an-active-directory)에서 사용한 도메인 이름을 입력합니다.
        - 도메인 사용자 이름: [Active Directory 만들기](#create-an-active-directory)에서 사용한 AD 관리자 이름을 입력합니다.
        - 조직 구성 단위: 예제를 보려면 스크린샷을 참조하세요.
        - LDAPS URL: 예제를 보려면 스크린샷을 참조하세요.
        - 사용자 그룹 액세스: [AD 사용자 및 그룹 만들기](#optionally-createad-users-and-groups)에서 만든 사용자 그룹 이름을 입력합니다.
    - 가상 네트워크: [Active Directory 만들기](#create-an-active-directory)에서 만든 가상 네트워크를 선택합니다. 템플릿에서 사용되는 기본 이름은 **adVNET**입니다.
    - 서브넷: 템플릿에서 사용되는 기본 이름은 **adSubnet**입니다.



이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다. 클러스터 삭제에 대한 내용은 [Azure Portal을 사용하여 HDInsight에서 Hadoop 클러스터 관리](../hdinsight-administer-use-management-portal.md#delete-clusters)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Hive 정책 및 Hive 쿼리 실행에 대한 자세한 내용은 [도메인에 가입된 HDInsight 클러스터에 대한 Hive 정책 구성](apache-domain-joined-run-hive.md)을 참조하세요.
* SSH를 사용하여 도메인 가입 HDInsight 클러스터에 연결하려면 [Linux, Unix 또는 OS X의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)을 참조하세요.

