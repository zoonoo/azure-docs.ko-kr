<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">리소스</th>
   <th align="left" valign="middle">기본 제한</th>
   <th align="left" valign="middle">최대 제한</th>
</tr>
<tr>
   <td valign="middle"><p>클라우드 서비스당 <a href="http://azure.microsoft.com/documentation/services/virtual-machines/">가상 컴퓨터</a><sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>클라우드 서비스당 입력 끝점<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Azure 리소스 그룹 외부에서 가상 컴퓨터를 만들면 컴퓨터를 포함할 클라우드 서비스가 자동으로 만들어집니다. 그러면 이 클라우드 서비스에 여러 개의 가상 컴퓨터를 추가할 수 있습니다.

<sup>2</sup>입력 끝점은 포함 클라우드 서비스 외부에 있는 가상 컴퓨터와 통신을 허용하는 데 사용됩니다. 이 클라우드 서비스 내의 가상 컴퓨터는 내부 통신을 위해 모든 UDP 포트와 TCP 포트 사이의 통신을 자동으로 허용합니다.

<!---HONumber=62-->