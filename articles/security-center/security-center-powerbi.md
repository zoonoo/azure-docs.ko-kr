<properties
   pageTitle="Power BI로 Azure 보안 센터 데이터에서 통찰력 얻기| Microsoft Azure"
   description="Azure 보안 센터 Power BI 콘텐츠 팩을 사용하면 보고를 위해 생성된 데이터 집합에 기반하여 보안 경고, 권장 사항, 공격 받은 리소스 및 추세를 쉽게 찾을 수 있습니다."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/11/2016"
   ms.author="yurid"/>

# Power BI로 Azure 보안 센터 데이터에서 통찰력 얻기
Azure 보안 센터에 대한 [Power BI 대시보드](http://aka.ms/azure-security-center-power-bi)를 사용하면 어디서든 모바일 장치를 포함하여 권장 사항 및 보안 경고를 시각화, 분석 및 필터링할 수 있습니다. Power BI 대시보드를 사용하여 추세를 표시하고 패턴을 공격합니다. 리소스 또는 원본 IP 주소에서 보안 경고 및 리소스 또는 시대에서 알려지지 않은 보안 위험을 봅니다. 또한 Power BI 대시보드를 제공하는 [Azure 감사 로그](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) 및 [Azure SQL 데이터베이스 감사](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/) 등 다른 데이터와 보안 센터 권장 사항 및 보안 경고를 흥미로운 방식으로 매시업할 수 있습니다. 혹은 클라우드 리소스의 보안 상태에 대한 간단한 보고를 위해 이 데이터를 Excel로 내보낼 수 있습니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## Power BI로 Azure 보안 센터 데이터 탐색
Power BI에서 [Azure 보안 센터 콘텐츠 팩](https://app.powerbi.com/groups/me/getdata/services/azure-security-center)에 연결하고 다음 단계를 수행합니다.

1\.Azure 보안 센터 타일에서 **연결**을 클릭하여 계속합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig1.png)

2\.**Azure 보안 센터에 연결** 창이 열립니다. **Azure 구독 ID** 필드에서 Azure 구독을 입력하고 **다음**을 클릭합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig2.png)

3\.**인증 방법** 드롭다운 목록에서 **oAuth2**를 선택하고 **로그인**을 클릭합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig3.png)

4\.Azure 보안 센터에 연결하는 데 사용하는 자격 증명을 입력해야 하는 인증 페이지로 이동합니다. 인증 프로세스를 완료한 후에 Power BI에서 데이터를 가져오기 시작하여 보고서를 작성합니다. 이 시간 동안 브라우저의 오른쪽 모퉁이에 다음과 같은 메시지가 나타날 수 있습니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig4.png)

5\.프로세스가 완료되면 아래와 같이 Azure 보안 센터 Power BI 대시보드가 보고서와 함께 로드됩니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig5.png)

한 눈에 Azure 보안 센터에서 모니터링된 VM, Azure SQL 데이터베이스 및 네트워크 리소스의 수 뿐만 아니라 보안 경고 및 권장 사항 수를 확인할 수 있습니다.

Azure 보안 센터에 대한 링크는 Azure 포털로 이동합니다. 차트는 다음을 포함하여 보안 권장 사항 및 경고에 대한 정보를 쉽게 시각화합니다.

- 리소스 보안 상태
- 보류 중인 전체 권장 사항
- VM 권장 사항
- 시간에 따른 경고
- 공격 받은 리소스
- 공격 받은 IP

각 차트 뒤에 자세한 내용이 있습니다. 자세한 내용을 보려면 타일을 선택합니다. 예를 들어 리소스 보안 상태 타일은 다음과 같이 리소스에 의해 보류 중인 권장 사항에 대한 추가 세부 정보를 표시합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig6.png)

이 그래프의 줄을 클릭하면 다른 줄은 회색으로 표시되고 사용자가 선택한 줄에만 집중됩니다. 대시보드 돌아가려면 이 페이지의 왼쪽된 창에서 **대시보드** 옵션의 **Azure 보안 센터**를 클릭합니다.

> [AZURE.NOTE] 추가 필드를 추가하거나 기존 시각화를 변경하여 보고서를 사용자 지정하려는 경우 보고서를 편집할 수 있습니다. 자세한 내용은 [Power BI의 편집용 보기에서 보고서와 상호 작용](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/)을 참고합니다.

**시간에 따른 경고**, **공격 받은 리소스** 및 **공격자 IP** 타일은 각각을 클릭할 때 비슷한 출력을 갖습니다. 보고서가 이러한 세 개의 변수 모두에 대한 정보를 집계하고 다음과 같이 **공격받는 리소스**를 호출하기 때문에 발생합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig7.png)

이 시점에서 이 보고서의 복사본을 저장하고 인쇄하거나 **파일** 메뉴에서 사용할 수 있는 옵션을 사용하여 웹에 게시할 수 있습니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig8.png)


##Azure 보안 센터 대시보드를 사용하여 Power BI에 액세스
또한 Azure 보안 센터 대시보드를 사용하여 Power BI 보고서에 액세스할 수 있습니다. 이 작업을 수행하려면 다음 단계를 따릅니다.

1\.**Azure 보안 센터** 대시보드에서 **Power BI에서 탐색** 단추를 클릭합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig9-new.png)

2\.**Power BI에서 탐색** 블레이드가 아래와 같이 오른쪽에 열립니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig10-1.png)

3\.**구독을 선택하여 Power BI에서 탐색** 드롭다운 목록에서 사용하려는 구독을 선택합니다.

4\.**구독 ID 복사** 필드에서 복사 단추를 클릭합니다. 5.**Power BI로 이동** 단추를 클릭합니다. 6.**Azure 보안 센터에 연결** 창이 열립니다. **Azure 구독 ID** 필드에서 Azure 구독을 입력하고 **다음**을 클릭합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig2.png)

7\.**인증 방법** 드롭다운 목록에서 **oAuth2**를 선택하고 **로그인**을 클릭합니다.

![Power BI를 사용하여 Azure 보안 센터에 연결](./media/security-center-powerbi/security-center-powerbi-fig3.png)

8\.Azure 보안 센터에 연결하는 데 사용하는 자격 증명을 입력해야 하는 인증 페이지로 이동합니다. 인증 프로세스를 완료한 후에 Power BI에서 데이터를 가져오기 시작하여 보고서를 작성합니다.

> [AZURE.NOTE] 보고서의 새로 고침을 매일 수행하도록 예약했을 경우 이 새로 고침에 오류가 발생한다면 문제를 해결하는 방법에 대한 자세한 내용은 [Azure 보안 센터 Power BI의 잠재적인 새로 고침 문제](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/)를 참조하세요.

## 다음 단계
이 문서에서는 Azure 보안 센터의 Power BI를 사용하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 보안 센터에서 보안 설정을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!---HONumber=AcomDC_0413_2016-->