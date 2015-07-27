<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p>구독당 <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">가상 네트워크</a><sup>1</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>가상 네트워크당 총 컴퓨터<sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>가상 컴퓨터 또는 역할 인스턴스용 동시 TCP 연결</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>끝점당 ACL(액세스 제어 목록)<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>가상 네트워크당 로컬 네트워크 사이트</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>각 가상 네트워크는 단일 [가상 네트워크 게이트웨이](http://msdn.microsoft.com/library/azure/jj156210.aspx)를 지원합니다.

<sup>2</sup>총 컴퓨터 수에는 가상 컴퓨터와 웹/작업자 역할 인스턴스가 포함됩니다.

<sup>3</sup>ACL은 가상 컴퓨터의 입력 끝점에서 지원됩니다. 웹/작업자 역할의 경우, 입력 끝점 및 인스턴스 입력 끝점에서 지원됩니다.

<!---HONumber=July15_HO3-->