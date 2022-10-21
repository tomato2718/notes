#Markdown 筆記
---
###1. Heading (標題)
#####說明:
於行首插入1~6個 # 號可設定1至6級標題，可選擇性於行末插入 # 作為關閉符號 (不影響結構)
#####sample:
###h3
####h4####
#####h5#
#####code:
	###h3
	####h4####
	#####h5#
	
###2. Horizontal Rule (水平分隔線)
#####說明:
使用3個以上連續的 - 可新增水平分隔線，行中不可有任何其餘字符
#####sample:
title
---
text
#####code:
	title
	---
	text

###3. Bold and Italic character (粗體與斜體)
#####說明:
使用 1 至 3 個連續的 * 或 _ 符號可以分別設定斜體、粗體、粗斜體
#####sample:
*斜體*

**粗體**

***粗斜體***

_斜體_

__粗體__

#####code:
	*斜體*

	**粗體**

	***粗斜體***

	_斜體_

	__粗體__

###4. Code (程式碼)
#####說明:
使用反引號 \` 包住文字可新增行內程式碼，若程式碼中包含 ` 可於頭尾使用多個反引號包裹
#####sample:
`print("Hello, World!")`

``print("backtick ` ")``
#####code:
	`print("Hello, World!")`

	``print("backtick ` ")``

###5. Code block (程式碼區塊)
#####說明:
於行首新增縮排或半形空格可新增程式碼區塊
使用三連續反引號 ` 於上下行包住文字也可新增程式碼區塊
#####sample:
	foo="Hello, World!"	
	print(foo)

```
foo="Hello, World!"	
print(foo)
```

#####code:
		foo="Hello, World!"	
		print(foo)
		
	```
	foo="Hello, World!"	
	print(foo)
	```

###6. Icon bullets (項目符號)
#####說明:
使用 +, -, * 搭配縮排 (tab) 或半形空格可新增項目符號
#####sample:
+	123
-	456
*	789

#####code:
	+	123
	-	456
	*	789

###7. Number bullets (編號)
#####說明:
使用半形數字搭配縮排 (tab) 或半形空格可新增編號
#####sample:
1.	abc
2.	def
3.	ghi

#####code:
	1.	abc
	2.	def
	3.	ghi

###8. Multilevel list (多層次清單)
#####說明:
於編號或項目符號中新增縮排可定義多層次清單
#####sample:
1.	123
	1.	abc
	2.	def
2.	456
	1.	abc
		*	xyz
	2.	def

#####code:
	1.	123
		1.	abc
		2.	def
	2.	456
		1.	abc
			*	xyz
		2.	def

###9. Hyperlink (超連結)
#####說明:
使用

	角括號 <>
	[文字](連結 "說明文字")
	[文字][ID] 
	[ID]: 連結 "說明文字"
新增超連結，\[ID] 可放置於文件任何地方
#####sample:
<https://www.google.com/>

[Google](https://www.google.com/ "點我前往Google")

[Google][1]

[1]: https://www.google.com/ "點我前往Google"
#####code:
	<https://www.google.com/>
	
	[Google](https://www.google.com/ "點我前往Google")

	[Google][1]

	[1]: https://www.google.com/ "點我前往Google"

###10. Image (圖片)
#####說明:
使用

	![替代文字](路徑 "說明文字")
	![替代文字][ID] 
	[ID]: 路徑 "說明文字"
可新增圖片
#####sample:
![替代文字](https://markdown.tw/images/208x128.png "Markdown")

![替代文字][2]
[2]: https://markdown.tw/images/208x128.png "Markdown"
#####code:
	![替代文字](https://markdown.tw/images/208x128.png "Markdown")
	
	![替代文字][2]
	[2]: https://markdown.tw/images/208x128.png "Markdown"

###11. Quote (引用)
#####說明:
使用 \> 可新增引用
#####sample:
>apple
>>banana
>>>cherry
>
>>date
>
>eggplant
#####code:
	>apple
	>>banana
	>>>cherry
	>
	>>date
	>
	>eggplant

###12. Table
#####說明:
使用 |、-、: 可編制表格，須注意並非所有 Markdown 編輯器與使用 Markdown 語法之網站都支援表格功能
#####sample:
A|B|C
:---|:---:|---:
left|center|right
#####code:
	A|B|C
	:---|:---:|---:
	left|center|right
###13. Escape character (跳脫字元)
#####說明:
於具功能之特定符號前加入反斜線 \ 可取消該符號之功能，如

	\	反斜線
	`	反引號
	*	星號
	_	底線
	{}	大括號
	[]	方括號
	<>	角括號
	()	括號
	#	井號
	+	加號
	-	減號
	.	英文句點
	!	驚嘆號
#####sample:
1\*2\*3=6
#####code:
	
	1\*2\*3=6

###14. HTML tag (HTML 標籤)
#####說明:
在 Markdown 文件 與部分使用 Markdown 語法 之網站，支援 HTML 語法的使用