<properties
   pageTitle="Azure 구독 양도 | Microsoft Azure"
   description="다른 사용자에게 Azure 구독을 전송하는 방법과 프로세스에 대한 몇 가지 질문과 대답(FAQ)"
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/21/2015"
   ms.author="curtand;kareni;ruchic"/>

# Azure 구독 양도

다음을 하시겠습니까?

- Azure 구독 청구 소유권을 다른 사람에게 양도하시겠습니까?
- Azure에 등록하는 데 사용된 계정을 변경하시겠습니까? Microsoft 계정을 사용하지만 그 계정 대신 회사 또는 학교 계정을 사용하시겠습니까?
- Azure 구독을 한 디렉터리에서 다른 컴퓨터로 이동하시겠습니까?
- 다른 테넌트에 있는 Azure 및 Office 365를 통합하시겠습니까?

계정이 미국에 있는 경우 이제 종량제 구독을 위해 Microsoft Azure 계정 센터에서 이를 쉽게 수행할 수 있습니다. 다른 사용자에게 구독을 양도하는 기능을 추가했습니다. 즉, 이제 자신이 소유한 종량제 구독의 계정 관리자를 변경할 수 있습니다.

## Azure 구독을 양도하는 방법

1.  <https://account.windowsazure.com/Subscriptions>에 로그인

2.  양도할 구독을 선택합니다.

3.  **구독 양도** 옵션을 클릭합니다.

    ![Azure 계정 구독 탭](./media/billing-subscription-transfer/image1.png)

4.  프롬프트에 따라 받는 사람을 지정 합니다.

    ![구독 양도 대화 상자](./media/billing-subscription-transfer/image2.PNG)

5.  받는 사람은 수락 링크가 포함된 전자 메일을 자동으로 받게 됩니다.

    ![받는 사람에게 구독 양도 전자 메일](./media/billing-subscription-transfer/image3.png)

6.  받는 사람은 링크를 클릭하고 지불 정보 입력 등의 지침을 따릅니다.

    ![첫 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/image4.PNG)

    ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/image5.PNG)

7. 성공! 구독이 이제 양도됩니다.

## 질문과 대답(FAQ)

-   **구독 양도로 인해 서비스 가동 중지 시간이 발생합니까?**

    서비스에 영향은 없습니다. 이는 현재 계정 관리자 하에서 구독을 효과적으로 취소하고 받는 사람의 계정 하에서 새 계정을 만들어 새 구독과 기본 Azure 서비스를 연결합니다. 구독 ID는 동일합니다.

-   **이 메커니즘을 어떻게 사용하여 구독 디렉터리를 변경하나요?** - Azure 구독은 계정 관리자가 속한 디렉터리에 생성됩니다. 따라서 디렉터리를 변경하려면 구독을 대상 디렉터리의 사용자 계정으로 전달하면 됩니다. 해당 사용자가 전달을 수락하는 절차를 완료하면 구독이 자동으로 대상 디렉터리로 이동합니다.
   
-   **다른 조직으로부터 구독 청구 소유권을 양도 받은 경우, 내 리소스에 계속 액세스할 수 있습니까?**

    구독을 다른 테넌트에 양도하는 경우, 이전 테넌트와 연결된 사용자는 구독에 액세스할 수 없게 됩니다. 사용자가 더 이상 서비스 관리자 또는 공동 관리자가 아닌 경우에도, 다른 보안 메커니즘을 통해 구독에 액세스할 수 있을 수 있습니다. 여기에는 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서가 포함됩니다. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](https://msdn.microsoft.com/library/azure/gg551722.aspx) - 저장소와 같은 서비스 액세스 키를 참조하세요. 자세한 내용은 [저장소 액세스 키 보기, 복사 및 재생성](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) - Azure 가상 컴퓨터와 같은 서비스의 원격 액세스 자격 증명을 참조하세요.

    이는 완전한 목록이 아닙니다. 받는 사람은 리소스에 액세스를 제한해야 하는 경우 서비스와 연결된 암호 업데이트를 고려해야 합니다. 대부분의 리소스는 다음과 같이 업데이트할 수 있습니다.

    1.   Azure 포털로 이동합니다.[**https://portal.azure.com*](https://portal.azure.com)

    2.    모두 찾아보기 -&gt; 모든 리소스를 클릭합니다.

    3.    리소스를 선택합니다. 이렇게 하면 리소스 블레이드가 열립니다.

    4.    리소스 블레이드에서 **설정**을 클릭합니다. 여기서 기존 암호를 보고 업데이트할 수 있습니다.


-   **청구 주기 도중에 구독을 양도하는 경우 전체 청구 주기에 대해 받는 사람이 지불해야 합니까?**

    양도가 완료되는 지점까지 보고된 사용량에 대한 지불 책임은 보낸 사람에게 있습니다. 받는 사람은 양도 받는 시점부터 보고된 사용량에 대해 지불 책임이 있습니다. 양도하기 전에 발생하지만 나중에 보고되는 일부 사용량이 있을 수 있습니다. 이는 받은 사람의 청구서에 포함됩니다.

-   **받는 사람은 사용양 및 청구 내역에 액세스할 수 있습니까?**

    현재 받는 사람에게 공개되는 유일한 정보는 마지막 청구 금액입니다(또는 첫 번째 청구가 생성하기 전에 구독이 양도된 경우 현재 잔액). 나머지 사용량 및 청구 내역은 구독과 함께 양도되지 않습니다.

-   **양도하는 동안 제품을 변경할 수 있습니까?**

    제품을 동일하게 유지해야 합니다. 제품을 변경하려면 [지원 부서에 문의](http://go.microsoft.com/fwlink/?LinkID=619338)해야 합니다.

-   **다른 국가에서 사용자 계정에 대 한 구독을 양도할 수 있습니까?**

    아니요. 현재는 지원되지 않습니다. 받는 사람의 사용자 계정은 동일한 국가에 있어야 합니다.

-   **받는 사람이 다른 지불 메커니즘을 사용할 수 있습니까?**

    예, 실제로 송장에서 신용 카드까지 구독에 대한 지불 방법을 변경하려면 이 메커니즘을 사용할 수 있습니다. 소유하고 있는 다른 계정에 양도하고 구독을 수신하는 동안 사용자의 신용 카드를 입력합니다. 여기에 제한 사항이 있습니다. 현재 구독 청구 내역은 두 계정으로 분할됩니다. 하지만 [지원 부서에 문의](http://go.microsoft.com/fwlink/?LinkID=619338)할 필요 없이 이를 수행할 수 있는 장점이 있습니다.

## 구독 소유권을 수락한 후 다음 단계

1. 이제 계정 관리자입니다. 서비스 관리자 및 공동 관리자를 검토하고 업데이트합니다. [Azure 관리 포털](https://manage.windowsazure.com)에서 설정으로 이동하여 관리자를 관리합니다. [자세히 알아봅니다](http://go.microsoft.com/fwlink/?LinkID=533293).
2. 구독 및 서비스에 대해 RBAC(역할 기반 액세스 제어)를 사용할 수도 있습니다. [Azure Preview 포털](https://portal.azure.com) [RBAC에 대한 자세한 정보](http://go.microsoft.com/fwlink/?LinkID=544802)를 방문하세요.
3. 이 구독의 서비스와 연결된 자격 증명을 업데이트합니다. 내용은 다음과 같습니다.
    -   구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure용 관리 인증서 만들기 및 업로드](https://msdn.microsoft.com/library/azure/gg551722.aspx)를 참조하세요.
    -	저장소와 같은 서비스에 대한 액세스 키. 자세한 내용은 [저장소 액세스 키 보기, 복사 및 다시 생성](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.
    -	Azure 가상 컴퓨터와 같은 서비스에 대한 원격 액세스 자격 증명
4. [Azure 계정 센터](https://account.windowsazure.com/Subscriptions) [자세한 정보](http://go.microsoft.com/fwlink/?LinkID=533292)에서 이 구독에 대한 청구 경고를 업데이트합니다.
5. 	파트너와 함께 작업하는 경우 이 구독에서 파트너 ID를 업데이트하는 것이 좋습니다. 이 작업은 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 수행할 수 있습니다.

<!---HONumber=Oct15_HO3-->