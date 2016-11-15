---
title: "HDInsight 응용 프로그램 게시 | Microsoft Docs"
description: "HDInsight 응용 프로그램을 만들고 게시하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ca18e2660d2e59f6dee12010abc9d1780f3a717a


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Azure 마켓플레이스에 HDInsight 응용 프로그램 게시
HDInsight 응용 프로그램은 Linux 기반 HDInsight 클러스터에 사용자가 설치할 수 있는 응용 프로그램입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 응용 프로그램을 개발할 수 있습니다. 이 문서에서는 HDInsight 응용 프로그램을 Azure 마켓플레이스에 게시하는 방법을 알아봅니다.  Azure 마켓플레이스에 게시하는 방법에 대한 일반 정보는 [Azure 마켓플레이스에 제품 게시](../marketplace-publishing/marketplace-publishing-getting-started.md)를 참조하세요.

HDInsight 응용 프로그램은 *BYOL(사용자 라이선스 필요)* 을 사용하며 여기서 응용 프로그램 공급자는 최종 사용자에게 응용 프로그램을 라이선싱하는 작업을 담당하고 Azure에서 최종 사용자에게 HDInsight 클러스터와 해당 VM/노드 등 만들 리소스에 대한 요금을 청구합니다. 이번에 응용 프로그램 자체에 대한 청구는 Azure를 통해 수행되지 않습니다.

다른 HDInsight 응용 프로그램 관련 문서:

* [HDInsight 응용 프로그램 설치](hdinsight-apps-install-applications.md): HDInsight 응용 프로그램을 클러스터에 설치하는 방법을 알아봅니다.
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): 사용자 지정 HDInsight 응용 프로그램을 설치하고 테스트하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
마켓플레이스에 사용자 지정 응용 프로그램을 제출하기 위해 사용자 지정 응용 프로그램을 만들고 테스트해야 합니다. 다음 문서를 참조하세요.

* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): 사용자 지정 HDInsight 응용 프로그램을 설치하고 테스트하는 방법을 알아봅니다.

또한 개발자 계정을 등록해야 합니다. [Azure Marketplace에 제품 게시](../marketplace-publishing/marketplace-publishing-getting-started.md) 및 [Microsoft 개발자 계정 만들기](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)를 참조하세요.

## <a name="define-application"></a>응용 프로그램 정의
Azure 마켓플레이스에 응용 프로그램을 게시하기 위해 두 가지 단계가 있습니다.  먼저 **createUiDef.json** 파일을 정의하여 응용 프로그램과 호환되고 클러스터를 나타낸 다음 Azure 포털에서 템플릿을 게시합니다. 다음은 샘플 createUiDef.json 파일입니다.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| 필드 | 설명 | 가능한 값 |
| --- | --- | --- |
| types |응용 프로그램과 호환되는 클러스터 종류입니다. |Hadoop, HBase, Storm, Spark(또는 이들의 조합) |
| tiers |응용 프로그램과 호환되는 클러스터 계층입니다. |Standard, Premium(또는 둘 다) |
| versions |응용 프로그램과 호환되는 HDInsight 클러스터 종류입니다. |3.4 |

## <a name="package-application"></a>패키지 응용 프로그램
HDInsight 응용 프로그램을 설치하는 데 필요한 모든 파일을 포함하는 zip 파일을 만듭니다. [응용 프로그램 게시](#publish-application)에 zip 파일이 필요합니다.

* [createUiDefinition.json](#define-application).
* mainTemplate.json. [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md)의 샘플을 참조하세요.
  
  > [!IMPORTANT]
  > 아래 형식을 사용하는 응용 프로그램 설치 스크립트의 이름은 특정 클러스터에 대해 고유해야 합니다. 또한 스크립트 작업을 설치 및 제거하는 것은 idempotent이여야 합니다. 즉, 동일한 결과를 생성하는 동안 스크립트를 반복하여 호출할 수 있습니다.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > 스크립트 이름은 세 부분으로 구성됩니다.
  > 
  > 1. 응용 프로그램 이름 또는 응용 프로그램에 관련된 이름을 포함하는 스크립트 이름 접두사.
  > 2. 가독성을 위한 "-".
  > 3. 매개 변수로 응용 프로그램 이름이 사용된 고유 문자열 함수.
  > 
  > 한 가지 예로서, 위 스크립트는 지속형 스크립트 작업 목록의 hue-install-v0-4wkahss55hlas가 됩니다. 샘플 JSON 페이로드는 [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json)를 참조하세요.
  > 
  > 
* 모든 필수 스크립트입니다.

> [!NOTE]
> 응용 프로그램 파일(있는 경우 웹 응용 프로그램 파일 포함)은 공개적으로 액세스할 수 있는 끝점에 위치할 수 있습니다.
> 
> 

## <a name="publish-application"></a>응용 프로그램 게시
다음 단계를 수행하여 HDInsight 응용 프로그램을 게시합니다.

1. [Azure 게시 포털](https://publish.windowsazure.com/)에 로그인합니다.
2. 왼쪽에서 **솔루션 템플릿** 을 클릭하여 새 솔루션 템플릿을 만듭니다.
3. 제목을 입력한 다음 **새 솔루션 템플릿 만들기**를 클릭합니다.
4. **Dev Center 계정 만들기 및 Azure 프로그램 조인** 을 클릭하여 아직 수행하지 않은 경우 회사를 등록합니다.  [Microsoft 개발자 계정 만들기](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)를 참조하세요.
5. **시작할 몇 가지 토폴로지 정의**를 클릭합니다. 솔루션 템플릿은 해당하는 모든 토폴로지의 "부모"입니다. 하나의 제품/솔루션 템플릿에서 여러 토폴로지를 정의할 수 있습니다. 제품이 스테이징으로 푸시될 때 해당 토폴로지도 모두 함께 푸시됩니다. 
6. 토폴로지 이름을 입력하고 더하기 기호를 클릭합니다.
7. 새 버전을 입력하고 더하기 기호를 클릭합니다.
8. [패키지 응용 프로그램](#package-application)에서 준비한 zip 파일을 업로드합니다.  
9. **인증 요청**을 클릭합니다. Microsoft 인증 팀이 파일을 검토하고 토폴로지를 인증합니다.

## <a name="next-steps"></a>다음 단계
* [HDInsight 응용 프로그램 설치](hdinsight-apps-install-applications.md): HDInsight 응용 프로그램을 클러스터에 설치하는 방법을 알아봅니다.
* [사용자 지정 HDInsight 응용 프로그램 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 응용 프로그램을 배포하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 응용 프로그램을 설치하는 방법을 알아봅니다.
* [Azure Resource Manager 템플릿을 사용하여 HDInsight의 Linux 기반 Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드 사용](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 액세스, HDInsight 응용 프로그램 테스트 및 HDInsight 응용 프로그램 호스팅하는 데 비어 있는 에지 노드를 사용하는 방법을 알아봅니다.




<!--HONumber=Nov16_HO2-->


