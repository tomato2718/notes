# Bash 基礎筆記	
## 宣告語言種類
### 說明
於腳本起始使用 `#!/bin/bash` 宣告該腳本使用之語言。

### 範例

```sh
#!/bin/bash
```

## 變數
### 說明
Bash 的變數不需要宣告型態，直接賦值即。

須注意 `=` 兩側不可留有空白，呼叫時使用 `$` 進行，若變數於字串中，可用 `{}` 將變數部分包住。

使用 `unset` 可移除變數。

### 範例
```sh
>>> a=1 #直接賦值
>>> ab=2
>>> echo $a #使用 $ 呼叫變數
1
>>> echo $ab
2
>>> echo ${a}b #若變數於字串中，可用 {} 將變數部分包住 
1b
>>> unset $a
>>> echo $a

>>> echo ${a}b
b
```

## 註解
### 說明
使用 `#` 可對程式碼進行註解。

### 範例
```sh
>>> echo 'apple' #印出 apple
apple
```

## echo 輸出
### 說明
使用 `echo` 可以印出字串，在單引號 `''` 中，特殊字元如 `$` 不起作用；而雙引號 `""` 中，特殊字元會起作用。

### 範例
```sh
>>> a="apple"
>>> b="banana"
>>> echo "tomato"
tomato
>>> echo '$a' #特殊字元無作用
$a
>>> echo "$b" #特殊字元有作用
banana
```

## printf 輸出
### 說明
使用 `printf` 可以進行格式化輸出，規則與其他語言大致相同。

### 範例
```sh
>>> a="apple"
>>> b="banana"
>>> printf "tomato\n"
tomato
>>> printf "%s %s\n" $a $b
apple banana
```

## 執行指令
### 說明
使用反引號 `\` 或 `$()` 可以執行指令。

### 範例
```sh
>>> echo `expr 2 + 2`
4
>>> echo $(expr 3 \* 3)
9
```

## 運算式
### 說明
使用 `expr`, `$(())`, `$[]` 可以進行運算，其中 `expr` 為 linux 指令，因此需要搭配反引號執行。

另外，乘號需要搭配反斜線為 `\*`。

### 範例
```
>>> echo `expr 2 + 2`
4
>>> echo $(expr 3 \* 3)
9
>>> echo $((4-4))
0
>>> echo $[5/5]
1
```

## if 條件判斷
### 說明
使用 `if[]...elif[]...else` 可以進行條件判斷，並使用 `fi` 作為結尾。

**判斷符號取自英文字首**

- `-eq`: equal（相等）
- `-ne`: not equal（不等） 
- `-gt`: greater than（大於）
- `-ge`: greater than or equal（大於等於）
- `-lt`: less than（小於）
- `-le`: less than or equal（小於等於）

### 範例
```sh
>>> a=1
>>> if [ $a -gt 10 ]; then
		echo "++"
	elif [$a -eq 01 ]; then
		echo "=="
	else
		echo "--"
	fi
--
```
### 執行結果
	--

## [[ ]] 高階比較
### 說明
使用 `[[]]` 與 `=~` 可以進行正規表示式比較。

### 範例
```sh
>>> a='tomato'
>>> if [[ $a =~ to?m* ]]; then
		echo 1
	else
		echo 0
	fi
1
```

## case 條件判斷
### 說明
使用 `case` 可以進行多重條件判斷，並使用 `esac` 作為結尾。

### 範例
```sh
>>> food='tomato'
>>> case $food in
		apple) echo "APPLE"
			;;
		banaba) echo "BANANA"
			;;
		tomato) echo "TOMATO"
			;;
		*) echo "NONE"
	esac
TOMATO
```

## for 迴圈
### 說明
使用 `for...do...done` 可以進行 for 迴圈，並搭配 `(( ; ; ))`, `{}`, `$(seq)` 設定範圍。

`{}` 之設定方式為 `{start..end..step}`。

`seq` 為指令，需搭配 `$()` 或 \`\` 呼叫，設定方式為 `seq start step end`。

### 範例
```sh
>>> for ((i=0;i<5;i++)) do
		echo $i
	done
0
1
2

>>> for i in {1..10..2}; do #使用 {} 時無法讀入變數，須使用 seq 進行
		echo $i
	done
1
3
5
7
9

>>> for i in $(seq 3 -1 0); do #同 `seq 3 -1 0`
		echo $i
	done
3
2
1
0
```

## while, until 迴圈
### 說明
`while` 與 `until` 語法相同，差別在於 `while` 判定結果為真時繼續；`until` 判定結果為真時停止。

### 範例
```sh
>>> c=0
>>> while [ $c -le 3 ]; do
		echo $c
		c=`expr $c + 1`
	done
0
1
2
3

>>> d=0
>>> until [ $d -gt 3]; do
		echo $d
		d=$(expt $d + 1)
	done
0
1
2
3
```

## Array 陣列
### 說明
使用 `()` 建立陣列，元素間以空白隔開。`ary[i]` 呼叫陣列中元素，`ary[@]`, `ary[*]` 呼叫陣列所有元素，`!ary[*]` 呼叫所有索引值。

### 範例
```sh
>>> ary=('apple' 'banana' 'tomato')
>>> echo ${ary[*]}
apple banana tomato
```

## Dictionary
### 說明
使用 `declare -A name` 可以建立字典 (雜湊表)，操作與陣列相同，但將索引值 (0, 1, 2, ...) 改為鍵 (key)，`!dict[*]` 呼叫所有鍵。

### 範例
```sh
>>> declare -A dict
>>> dict=(['a']='apple' ['b']=banana ['t']='tomato')
>>> for i in ${!dict[*]}; do
		echo "$i ${dict[$i]}"
	done
a apple
b banana
t tomato
```

## foreach
### 說明
使用 `for` 可以達到 `foreach` 之效果。

### 範例
```sh
>>> ary=('apple' 'banana' 'tomato')
>>> for i in ${ary[*]}; do
		echo $i
	done
apple
banana
tomato
```