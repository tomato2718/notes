# Markdown HTML 介紹
## 概要
在 Markdown 與部分使用 Markdown 語法之網站，可以直接使用 HTML 標籤。

但需要注意，盡量避免在 markdown 中使用 HTML 標籤，以維持文件翻譯前的可讀性。

## 說明
### CSS
透過 CSS 可對 HTML 標籤 中內容進行額外樣式設定，於起始標籤內加入 `style=""` 可對標籤進行設定。

長度類型之屬性使用 CSS Units，如：`px (像素)`、 `cm (公分)` 、 `% (百分比)`。

顏色類型之屬性使用 `HTML Color Named` 或 16進制色碼 如 `blue`、`red`、`#00FFFF`。

### span
一般使用 `<span>` 標籤對文字進行設定，常用屬性如下：

- `font-size`: 文字大小。
- `color`: 文字顏色。
- `background-color`: 背景顏色。

```md
<span style="color: #FF0000; font-size: 16px;">Hello, world!</span>

<span style="color: blue;">Hello, world!</span>

<span style="background-color: yellow;">Hello, world!</span>
```


### style
使用 `<style>` 標籤可將 CSS 獨立為一標籤進行統一設定，使用 `目標{屬性 : 值;}` 的方式設定，目標設定方式如下：

- **標籤:** 直接指定標籤種類即可。
	- 如：`span{}`
- **class:** 指定時須加上 `.` 號。
	- 如：`.c1{}`
- **id:** 指定時須加上 `#` 號。
	- 如：`#last{}`

```html
<style>
span{
	color: red;
}

#foo{
	color: yellow;
}
</style>
<span>test</span>

<span id="foo">foo</span>
```

### table
使用 `<table>`、`<tr>`、`<th>`、`<td>` 標籤也可以繪製表格，常用屬性如下：

- `border-collapse:` 設定儲存格間是否分離，支援以下值：
	- `serperate` (分離)
	- `collapse` (合併)
- `height:` 設定表格高度。
- `width:` 設定表格寬度。
- `text-align:` 設定文字對齊，，支援以下值：
	- `left` (左)
	- `center` (置中)
	- `right` (右)
- `colspan:` 設定儲存格所佔欄數 (合併儲存格)。
- `border:` 設定表格邊框，會自動抓取填入之顏色、邊框類型、邊框粗細

```html
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
```

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