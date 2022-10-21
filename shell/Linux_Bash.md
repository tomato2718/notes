#Bash 基礎筆記	
---
###1.  宣告語言種類
#####說明:
於腳本起始使用 `#!/bin/bash` 宣告該腳本使用之語言
#####code:
	#!/bin/bash

###2.  變數
#####說明:
Bash 的變數不需要宣告型態，直接賦值即可<br>
須注意 `=` 兩側不可留有空白<br>
呼叫時使用 `$` 進行<br>
若變數於字串中，可用 `{}` 將變數部分包住<br>
使用 `unset` 可移除變數
#####code:
	a=1 #直接賦值
	ab=2
	echo $a #使用 $ 呼叫變數
	echo $ab
	echo ${a}b #若變數於字串中，可用 {} 將變數部分包住 
	unset $a
	$echo $a
	$echo ${a}b
#####執行結果:
	1
	2
	1b
	
	b

###3. 註解
#####說明:
使用 `#` 可對程式碼進行註解
#####code:
	echo 'apple' #印出 apple
#####執行結果:
	apple

###4. echo 輸出
#####說明:
使用 `echo` 可以印出字串<br>
在單引號 `''` 中，特殊字元如 `$` 不起作用；而雙引號 `""` 中，特殊字元會起作用
#####code:
	a="apple"
	b="banana"
	echo "tomato"
	echo '$a' #特殊字元無作用
	echo "$b" #特殊字元有作用
#####執行結果:
	tomato
	$a
	banana

###5. printf 輸出
#####說明:
使用 `printf` 可以進行格式化輸出，規則與其他語言大致相同
#####code:
	a="apple"
	b="banana"
	printf "tomato\n"
	printf "%s %s\n" $a $b
#####執行結果:
	tomato
	apple banana

###6. 執行指令
#####說明:
使用反引號 \`\` 或 `$()` 可以執行指令
#####code:
	echo `expr 2 + 2`
	echo $(expr 3 \* 3)
#####執行結果:
	4
	9

###7. 運算式
#####說明:
使用 `expr`, `$(())`, `$[]` 可以進行運算，其中 `expr` 為 linux 指令，因此需要搭配反引號執行<br>
另外需要搭配反斜線乘號為 `\*` 
#####code:
	echo `expr 2 + 2`
	echo $(expr 3 \* 3)
	echo $((4-4))
	echo $[5/5]
#####執行結果:
	4
	9
	0
	1

###8. if 條件判斷
#####說明:
使用 `if[]...elif[]...else` 可以進行條件判斷，並使用 `fi` 作為結尾
判斷符號取自英文字首

	-eq: equal（相等）
	-ne: not equal（不等） 
	-gt: greater than（大於）
	-ge: greater than or equal（大於等於）
	-lt: less than（小於）
	-le: less than or equal（小於等於）
#####code:
	a=1
	if [ $a -gt 10 ]; then
		echo "++"
	elif [$a -eq 01 ]; then
		echo "=="
	else
		echo "--"
	fi
#####執行結果:
	--

###9. [[ ]] 高階比較
#####說明:
使用 `[[]]` 與 `=~` 可以進行正規表示式比較
#####code:
	a='tomato'
	if [[ $a =~ to?m* ]]; then
		echo 1
	else
		echo 0
	fi
#####執行結果:
	1

###10. case 條件判斷
#####說明:
使用 `case` 可以進行多重條件判斷，並使用 `esac` 作為結尾
#####code:
	food='tomato'
	case $food in
		apple) echo "APPLE"
			;;
		banaba) echo "BANANA"
			;;
		tomato) echo "TOMATO"
			;;
		*) echo "NONE"
	esac
#####執行結果:
	TOMATO

###11. for 迴圈
#####說明:
使用 `for...do...done` 可以進行 for 迴圈，並搭配 `(( ; ; ))`, `{}`, `$(seq)` 設定範圍<br>
`{}` 之設定方式為 `{start..end..step}`
`seq` 為指令，需搭配 `$()` 或 \`\` 呼叫，設定方式為 `seq start step end`
#####code:
	for ((i=0;i<5;i++)) do
		echo $i
	done

	echo 

	for i in {1..10..2}; do #使用 {} 時無法讀入變數，須使用 seq 進行
		echo $i
	done

	echo
	
	for i in $(seq 3 -1 0); do #同 `seq 3 -1 0`
		echo $i
	done
#####執行結果:
	0
	1
	2

	1
	3
	5
	7
	9

	3
	2
	1
	0

###12. while, until 迴圈
#####說明:
`while` 與 `until` 語法相同，差別在於 `while` 判定結果為真時繼續；`until` 判定結果為真時停止
#####code:
	c=0
	while [ $c -le 3 ]; do
		echo $c
		c=`expr $c + 1`
	done
	
	d=0
	until [ $d -gt 3]; do
		echo $d
		d=$(expt $d + 1)
	done
#####執行結果:
	0
	1
	2
	3
	0
	1
	2
	3

###13. Array 陣列
#####說明:
使用 `()` 建立陣列，元素間以空白隔開，`ary[i]` 呼叫陣列中元素，`ary[@]`, `ary[*]` 呼叫陣列所有元素，`!ary[*]` 呼叫所有索引值
#####code:
	ary=('apple' 'banana' 'tomato')
	echo ${ary[*]}
#####執行結果:
	apple banana tomato

###14.  Dictionary
#####說明:
使用 `declare -A name` 可以建立字典 (雜湊表)，操作與陣列相同，但將索引值 (0, 1, 2, ...) 改為鍵 (key)，`!dict[*]` 呼叫所有鍵
#####code:
	declare -A dict
	dict=(['a']='apple' ['b']=banana ['t']='tomato')
	for i in ${!dict[*]}; do
		echo "$i: ${dict[$i]}"
	done
#####執行結果:
	a: apple
	b: banana
	t: tomato

###15. foreach
#####說明:
使用 `for` 可以達到 foreach 之效果
#####code:
	ary=('apple' 'banana' 'tomato')
	for i in ${ary[*]}; do
		echo $i
	done
#####執行結果:
	apple
	banana
	tomato