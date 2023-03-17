#Markdown_HTML
---
###1. CSS
#####說明:``
透過 CSS 可對 HTML 標籤 中內容進行額外樣式設定，於起始標籤內加入 `style=""` 可對標籤進行設定

長度類型之屬性使用 CSS Units，如 `px (像素)`、 `cm (公分)` 、 `% (百分比)`

顏色類型之屬性使用 `HTML Color Named` 或 16進制色碼 如 `blue`、`red`、`#00FFFF`

###2. span
#####說明:
一般使用 `<span>` 標籤對文字進行設定，常用屬性如下

 	font-size: 文字大小
 	color: 文字顏色
 	background-color: 背景顏色 (醒目標記) 

#####sample:
<span style="color: #FF0000; font-size: 16px;">Hello, world!</span>

<span style="color: blue;">Hello, world!</span>

<span style="background-color: yellow;">Hello, world!</span>
#####code:
	<span style="color: #FF0000; font-size: 16px;">Hello, world!</span>
	
	<span style="color: blue;">Hello, world!</span>
	
	<span style="background-color: yellow;">Hello, world!</span>


###3. style
#####說明:
使用 `<style>` 標籤可將 CSS 獨立為一標籤進行統一設定，使用 `目標{屬性 : 值;}` 的方式設定，目標設定方式如下
	
	標籤：直接指定標籤種類即可，如
	span{}
	
	class：指定時須加上 . 號，如
	.c1{}
	
	ID：指定時須加上 # 號，如
	#last{}

###4. table
#####說明:
使用 `<table>`、`<tr>`、`<th>`、`<td>` 標籤可繪製 table、row、header、column，常用屬性如下

	border-collapse: 設定儲存格間是否分離，值為 serperate (分離)、collapse (合併)
	height: 設定表格高度
	width: 設定表格寬度
	text-align: 設定文字對齊，值為 left (左)、center (置中) 、right (右)
	colspan: 設定儲存格所佔 columns
	border: 設定表格邊框，會自動抓取填入之 顏色、邊框類型、邊框粗細

#####sample:
<style>
table{
	border: #000000 solid 3px;
	border-collapse: collapse;
	width: 300px;
	font-size: 16px;
}
table th{
	border: #000000 dashed 1px;
	background-color: #999999;
	height: 40px;
}
table td{
	border: #000000 solid 1px;
	height: 60px;
}
.c1{
	text-align: left;
}
.c2{
	text-align: center;
}
.c3{
	text-align: right;
}
table #last{
	text-align: center;
}
</style>

<table>
	<tr>
		<th>header 1</th>
		<th>header 2</th>
		<th>header 3</th>
	</tr>
	<tr>
		<td class="c1">1</td>
		<td class="c2">2</td>
		<td class="c3">3</td>
	</tr>
	<tr>
		<td class="c1">4</td>
		<td class="c2">5</td>
		<td class="c3">6</td>
	</tr>
	<tr>
		<td class="c1">7</td>
		<td class="c2">8</td>
		<td class="c3">9</td>
	</tr>
	<tr>
		<td colspan=2 style="text-align: center;">合併儲存格</td>
		<td id="last">0</td>
	</tr>
</table>
#####code:
	<style>
	table{
		border: #000000 solid 3px;
		border-collapse: collapse;
		width: 300px;
		font-size: 16px;
	}
	table th{
		border: #000000 dashed 1px;
		background-color: #999999;
		height: 40px;
	}
	table td{
		border: #000000 solid 1px;
		height: 60px;
	}
	.c1{
		text-align: left;
	}
	.c2{
		text-align: center;
	}
	.c3{
		text-align: right;
	}
	table #last{
		text-align: center;
	}
	</style>
	
	<table>
		<tr>
			<th>header 1</th>
			<th>header 2</th>
			<th>header 3</th>
		</tr>
		<tr>
			<td class="c1">1</td>
			<td class="c2">2</td>
			<td class="c3">3</td>
		</tr>
		<tr>
			<td class="c1">4</td>
			<td class="c2">5</td>
			<td class="c3">6</td>
		</tr>
		<tr>
			<td class="c1">7</td>
			<td class="c2">8</td>
			<td class="c3">9</td>
		</tr>
		<tr>
			<td colspan=2 style="text-align: center;">合併儲存格</td>
			<td id="last">0</td>
		</tr>
	</table>

