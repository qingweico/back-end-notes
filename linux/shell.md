## 1 输出文件file.txt的第十行内容

file.txt

```bash
Line 1
Line 2
Line 3
Line 4
Line 5
Line 6
Line 7
Line 8
Line 9
Line 10
```

```bash
awk 'NR == 10' file.txt 
```

```bash
sed -n 10p file.txt
```

```bash
tail -n+10 file.txt | head -1
```

```shell
#/bin/bash
count=`cat file.txt | wc -l`
out=`cat file.txt | head -10 | tail -1`
if [ $count -lt 10 ]
then
    echo ""
else echo $out
fi
```

```shell
#/bin/bash
c=10
cat file.txt|
while read line
do
    if [ $c == 1 ]
    then
        echo $line
        exit 0
    fi
    c=$(($c-1))
done
```

