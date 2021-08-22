---
title: Azure에서 Oracle BareMetal 서버로 트래픽을 라우팅하도록 SnapCenter 설정
description: Azure에서 Oracle BareMetal Infrastructure 서버로 트래픽을 라우팅하도록 NetApp SnapCenter를 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: 9d8f83712822dcf556efe1098eadd1cfdb4867e6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576233"
---
# <a name="set-up-netapp-snapcenter-to-route-traffic-from-azure-to-oracle-baremetal-servers"></a>Azure에서 Oracle BareMetal 서버로 트래픽을 라우팅하도록 NetApp SnapCenter 설정

이 문서에서는 Azure에서 Oracle BareMetal Infrastructure 서버로 트래픽을 라우팅하도록 NetApp SnapCenter를 설정하는 단계를 안내합니다. 

## <a name="prerequisites"></a>필수 구성 요소

> [!div class="checklist"]
> - **SnapCenter 서버 시스템 요구 사항:** 4 vCPU, 16GB RAM, 500GB 이상의 관리형 프리미엄 SSD 스토리지가 있는 Azure Windows 2019 이상
> - **ExpressRoute 네트워킹 요구 사항:** Oracle BareMetal용 SnapCenter 사용자는 Microsoft Operations 팀과 협력하여 Azure에서 개인 스토리지 VM(가상 머신)과 SnapCenter VM 간의 통신을 가능하게 하는 네트워킹 계정을 만들어야 합니다.
> - **BareMetal 인스턴스의 Java 1.8:** SnapCenter 플러그 인을 사용하려면 BareMetal 인스턴스에 Java 1.8이 설치되어 있어야 합니다.

## <a name="steps-to-integrate-snapcenter"></a>SnapCenter를 통합하는 단계

Azure에서 Oracle BareMetal Infrastructure 서버로 트래픽을 라우팅하도록 NetApp SnapCenter를 설정하기 위해 완료해야 하는 단계는 다음과 같습니다. 

1. 사용자가 생성한 공개 키를 Microsoft 운영 팀에 전달해 달라는 지원 티켓 요청을 발생시킵니다. SnapCenter 사용자가 스토리지 시스템에 액세스할 수 있도록 설정하려면 지원이 필요합니다. 

2. BareMetal 인스턴스에 액세스할 수 있는 Azure VNet(Virtual Network)에 VM을 만듭니다. 이 VM은 SnapCenter에 사용됩니다. 

3. SnapCenter를 다운로드하여 설치합니다. 

4. 작업을 백업 및 복구합니다. 

>[!NOTE]
> 이 단계에서는 Azure의 구독과 Oracle HaaS의 테넌트 간에 ExpressRoute 연결을 이미 만들었다고 가정합니다.

## <a name="create-a-support-ticket-for-user-role-storage-setup"></a>사용자 역할 스토리지 설정에 대한 지원 티켓 만들기

1. Azure Portal을 열고 구독 페이지로 이동합니다. BareMetal 구독을 선택합니다.
2. BareMetal 구독 페이지에서 **리소스 그룹** 을 선택합니다.
3. 지역에서 적절한 리소스 그룹을 선택합니다.
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-resource-group.png" alt-text="구독 페이지의 리소스 그룹을 보여주는 스크린샷":::

4. Azure 스토리지의 SAP HANA에 해당하는 SKU 항목을 선택합니다. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-sku.png" alt-text="SAP HANA on Azure 형식이 강조 표시된 SKU를 보여주는 리소스 그룹 스크린샷":::

5. **새 지원 요청** 을 선택합니다.

6. **기본** 탭에서 다음과 같은 티켓 정보를 입력합니다.
    - **문제 유형**: 기술
    -   **구독**: 사용자의 구독
    -   **서비스**: BareMetal Infrastructure
    -   **리소스**: 해당 리소스 그룹
    -   **요약**: SnapCenter 액세스 설정
    -   **문제 유형**: 구성 및 설정
    -   **문제 하위 유형**: SnapCenter 설정

7. 자세한 내용을 보려면 지원 티켓에 대한  **설명** 의  **세부 정보**  탭에서 *.pem 파일의 내용을 텍스트 상자에 붙여넣습니다. *.pem 파일을 압축하여 업로드할 수도 있습니다. snapcenter.pem은 SnapCenter 사용자의 공개 키가 됩니다. 다음 예를 사용하여 BareMetal 인스턴스 중 하나에서 *.pem 파일을 만듭니다. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/pem.png" alt-text=".pem 파일의 샘플 콘텐츠를 보여주는 스크린샷":::

    >[!NOTE]
    >"snapcenter" 파일의 이름은 REST API 호출에 필요한 사용자 이름입니다. 따라서 파일 이름에 설명이 포함되도록 만드는 것이 좋습니다.

8.  **검토 + 만들기** 를 선택하고 지원 티켓을 검토합니다.

9.  공개 키 인증서가 제출되면 Microsoft에서 테넌트의 SnapCenter 사용자 이름을 SVM(스토리지 가상 머신) IP 주소와 함께 설정합니다. Microsoft에서 SVM IP를 제공합니다.

10. SVM IP를 받은 후에는 본인이 제어하는 SVM에 액세스하는 데 사용할 암호를 설정합니다.

    다음은 HANA 대규모 인스턴스 환경에 대한 액세스 권한이 있으며 암호를 설정하는 데 사용되는 HANA 대규모 인스턴스 또는 가상 네트워크의 VM에서 수행하는 REST CALL의 예입니다.
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/sample-rest-call.png" alt-text="샘플 REST 호출을 보여주는 스크린샷":::

11. *.pem 파일을 만드는 데 사용된 BareMetal 인스턴스에 프록시 변수가 활성 상태가 아닌지 확인 합니다.

     :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/ensure-no-proxy.png" alt-text="*.pem 파일을 만들 때 BareMetal 인스턴스에 활성 프록시 변수가 없도록 하기 위해 http 프록시를 설정 해제(unset)하는 것을 보여주는 스크린샷":::

12. 이제 클라이언트 컴퓨터에서 사용하도록 설정된 REST 명령에 대한 사용자 이름/암호 없이 명령을 실행할 수 있습니다. 연결을 테스트합니다. 

    프록시 없음:

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/connection-test.png" alt-text="사용자 이름/암호 없이 연결 및 활성화된 REST 명령 테스트를 보여주는 스크린샷":::


       >[!NOTE]
       > 두 curl 명령에 "--verbose"를 추가하면 명령이 성공하지 못한 이유에 대한 자세한 정보를 제공할 수 있습니다.

## <a name="install-snapcenter"></a>SnapCenter 설치

1. 프로비전된 Windows VM에서 [NetApp 웹 사이트](https://mysupport.netapp.com/site/products/all/details/snapcenter/downloads-tab)로 이동합니다.

2. 로그인하고 SnapCenter를 다운로드합니다. 다운로드 > SnapCenter > **버전 4.4** 를 선택합니다.

3. SnapCenter 4.4를 설치합니다. **다음** 을 선택합니다.

    설치 프로그램이 VM의 필수 구성 요소를 확인합니다. 특히 대규모 환경에서는 VM 크기에 주의합니다. 다시 시작이 보류 중인 경우에도 설치를 계속해도 됩니다.

4. SnapCenter의 사용자 자격 증명을 구성합니다. 기본적으로 설치를 위해 애플리케이션을 시작한 Windows 사용자로 채워집니다. 포트 충돌이 없는 한 기본 포트를 사용하는 것이 좋습니다.

    설치 마법사가 완료되고 진행 상황을 표시하는 데 다소 시간이 소요됩니다.
 
5. 설치가 완료되면 **마침** 을 선택합니다.  SnapCenter 웹 포털의 웹 주소를 기록해 둡니다.  설치가 완료된 후 바탕 화면에 나타나는 SnapCenter 아이콘을 두 번 클릭하여 연결할 수도 있습니다.
 
## <a name="disable-enhanced-messaging-service-ems-messages-to-netapp-auto-support"></a>NetApp 자동 지원에 대한 EMS(Enhanced Messaging Service) 메시지 사용 안 함

EMS 데이터 수집은 기본적으로 사용하도록 설정되며 설치 날짜 이후 7일마다 실행됩니다. 언제든지 데이터 수집을 사용하지 않도록 설정할 수 있습니다.

1. PowerShell 명령줄에서 SnapCenter와 세션을 설정합니다.

   ```powershell-interactive
   Open-SmConnection
   ```

2. 자격 증명을 사용하여 로그인합니다.

3. EMS 데이터 수집을 사용하지 않도록 설정합니다. 

   ```powershell-interactive
   Disable-SmDataCollectionEms
   ```
   
## <a name="next-steps"></a>다음 단계

SnapCenter 구성 방법 알아보기:

> [!div class="nextstepaction"]
> [SnapCenter 구성](configure-snapcenter-oracle-baremetal.md)
