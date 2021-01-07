---
title: '자습서: Azure HDInsight에서 Apache Ambari 이메일 알림 구성'
description: 이 문서에서는 이메일 알림을 위해 SendGrid를 Apache Ambari와 함께 사용하는 방법을 설명합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: bdce735bdacbe7ff0752650c6949fdb361342c73
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542559"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>자습서: Azure HDInsight에서 Apache Ambari 이메일 알림 구성

이 자습서에서는 SendGrid를 사용하여 Apache Ambari 이메일 알림을 구성합니다. [Apache Ambari](./hdinsight-hadoop-manage-ambari.md)는 사용하기 쉬운 웹 UI 및 REST API를 제공하여 HDInsight 클러스터의 관리 및 모니터링을 간소화합니다. Ambari는 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다. [SendGrid](https://sendgrid.com/solutions/)는 클라우드 기반 무료 이메일 서비스이며, 신뢰할 수 있는 트랜잭션 이메일 전송, 확장성 및 실시간 분석과 함께 사용자 지정 통합을 쉽게 만드는 유연한 통합 API를 제공합니다. Azure 고객은 매달 25,000통의 무료 전자 메일의 잠금을 해제할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Sendgrid 사용자 이름 가져오기
> * Apache Ambari 이메일 알림 구성

## <a name="prerequisites"></a>사전 요구 사항

* SendGrid 이메일 계정. 지침은 [Azure에서 SendGrid를 사용하여 이메일을 보내는 방법](../sendgrid-dotnet-how-to-send-email.md)을 참조하세요.

* HDInsight 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](./hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

## <a name="obtain-sendgrid-username"></a>SendGrid 사용자 이름 가져오기

1. [Azure Portal](https://portal.azure.com)에서 SendGrid 리소스로 이동합니다.

1. 개요 페이지에서 **관리** 를 선택하고 계정의 SendGrid 웹 페이지로 이동합니다.

    ![Azure Portal의 SendGrid 개요](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. 왼쪽 메뉴에서 계정 이름으로 이동한 다음, **계정 정보** 로 이동합니다.

    ![SendGrid 대시보드 탐색](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. **계정 정보** 페이지에서 **사용자 이름** 을 기록합니다.

    ![SendGrid 계정 정보](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Ambari 이메일 알림 구성

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. **작업** 드롭다운 목록에서 **알림 관리** 를 선택합니다.

1. **Manage Alert Notifications** (경고 알림 관리) 창에서 **+** 아이콘을 선택합니다.

    ![스크린샷은 Alert Notifications 관리 대화 상자를 보여줍니다.](./media/apache-ambari-email/azure-portal-create-notification.png)

1. **Create Alert Notification** (경고 알림 만들기) 대화 상자에서 다음 정보를 제공합니다.

    |속성 |Description |
    |---|---|
    |속성|알림 이름을 제공합니다.|
    |그룹|원하는 대로 구성합니다.|
    |심각도|원하는 대로 구성합니다.|
    |Description|(선택 사항)|
    |방법|**EMAIL** 로 둡니다.|
    |Email To(받는 사람 이메일)|알림을 받을 이메일을 쉼표로 구분하여 제공합니다.|
    |SMTP Server|`smtp.sendgrid.net`|
    |SMTP 포트|25 또는 587(암호화되지 않은/TLS 연결인 경우).|
    |Email From(보낸 사람 이메일)|이메일 주소를 입력합니다. 이 주소는 확실하지 않아도 됩니다.|
    |인증 사용|이 확인란을 선택합니다.|
    |사용자 이름|SendGrid 사용자 이름을 제공합니다.|
    |암호|Azure에서 SendGrid 리소스를 만들 때 사용한 암호를 제공합니다.|
    |암호 확인|암호를 다시 입력합니다.|
    |Start TLS(TLS 시작)|이 확인란을 선택합니다.|

    ![스크린샷은 Alert Notification 만들기 대화 상자를 보여줍니다.](./media/apache-ambari-email/ambari-create-alert-notification.png)

    **저장** 을 선택합니다. **Manage Alert Notifications** (경고 알림 관리) 창이 다시 열립니다.

1. **Manage Alert Notifications** (경고 알림 관리) 창에서 **닫기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 SendGrid를 사용하여 Apache Ambari 이메일 알림을 구성하는 방법을 알아보았습니다. Apache Ambari에 대해 자세히 알아보려면 다음 항목을 사용하세요.

* [Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리](./hdinsight-hadoop-manage-ambari.md)

* [경고 알림 만들기](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)