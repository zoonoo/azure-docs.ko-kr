
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Manage server-level firewall rules through the new Azure portal
-->


1. http://portal.azure.com/에서 [Azure 포털](http://portal.azure.com/)에 로그인합니다.

2. 왼쪽 배너에서 **모두 찾아보기**를 클릭합니다. **찾아보기** 블레이드가 표시됩니다.

3. 스크롤하여 **SQL Server**를 클릭합니다. **SQL Server** 블레이드가 표시됩니다.

	![포털에서 Azure SQL 데이터베이스 서버를 찾습니다.][b21-FindServerInPortal]

4. 편의를 위해 이전 **찾아보기** 블레이드에서 최소화 컨트롤을 클릭합니다.

5. 필터 텍스트 상자에 서버 이름을 입력하기 시작합니다. 해당 행이 표시됩니다.

6. 서버에 대한 행을 클릭합니다. 서버 블레이드가 표시됩니다.

7. 서버 블레이드에서 **설정**을 클릭합니다. **설정** 블레이드가 표시됩니다.

8. **방화벽**을 클릭합니다. **방화벽 설정** 블레이드가 표시됩니다.

	![설정 > 방화벽을 클릭합니다.][b31-SettingsFirewallNavig]

9. **클라이언트 IP 추가**를 클릭합니다. 첫 번째 텍스트 상자에 새 규칙의 이름을 입력합니다.

10. 사용하도록 설정하려는 범위의 하한 및 상한 IP 주소 값을 입력합니다.
 - 하한 값은 **.0**으로 끝나고 상한 값은 **.255**로 끝나도록 하면 유용할 수 있습니다. 

	![허용할 IP 주소 범위를 추가합니다.][b41-AddRange]

11. **Save**를 클릭합니다.



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_1223_2015-->