---
title:  "C 輸入資料常見的處理方式"
date:   2016-08-09 19:00:00 +0800
---
## Integer Type
### 給定總數
第一行先給定接下來要輸入資料的個數

```c
3
1 2
3 4
5 6
```

```c
int n, a, b;
scanf("%d", &n);
while (n--) {                         // 需要第幾筆資料的話，也可以用 for 迴圈往上加
    scanf("%d %d", &a, &b);

    ...
    do something
    ...
}
```

<!--excerpt-->
### 結尾為 0

沒有給多少筆資料，結尾以 `0` 表示

```c
1 2
3 4
5 6
0 0     // 可能只有一個 0
```

```c
int a, b;
while (1) {
    scanf("%d %d", &a, &b);
    if (a==0 && b==0) break;    // 若為 0 則跳出

    ...
    do something
    ...
}
```

### 不知道資料長度

沒有任何能判定資料長度的資料，只能用是否為文件末端 `EOF` 來判定

#### 方法 1
```c
while (scanf("%d %d", &a, &b) != EOF) {
    ...
    do something
    ...
}
```

#### 方法 2
```c
char line[256];
while(fgets(line, 256, stdin) != NULL) {
    sscanf(line, "%d %d", &a, &b);

    ...
    do something
    ...
}
```

## String Type
### 讀取字串 Reading a String
```c
char s[256];                           // 最多可以放 255 個字元

scanf("%s", s);                        // 以空白鍵、tab、換行 當作中斷點
fegts(s, 256, stdin);                  // 僅以換行當作中斷點
```

```c
#include <stdio.h>
#include <string.h>

char line[256];
while(fgets(line, 256, stdin) != NULL) {
    char* token;

    token = strtok(line, " \t\n");     // the first word
    first = atoi(token);               // integer type

    token = strtok(NULL, " \t\n");     // the second word
    second = token                     // string type

    ...
    do something
    ...
}
```


### 常用的 string function
在使用之前要都要先 `#include <string.h>`

- 字串長度: `strlen`
- 複製字串: `strcpy` `strdup`
- 切割字串: `strtok`
- 連接字串: `strstr`
- 反轉字串: `reverse`

### 字串長度 strlen

```c
strlen("ABCDEFGHI");                   // return 9
```

| A  | B  | C  | D  | E  | F  | G  | H  | I  | \0 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 65 | 66 | 67 | 68 | 69 | 70 | 71 | 72 | 73 | 0  |

### 複製字串

`strcpy` 跟 `strdup` 的差別就在於：

- `strcpy` 需要自己準備好字串的長度
- `strdup` 則會自己幫你 alloc 一塊記憶體，但之後要自己 free 掉

#### strcpy
```c
char p[256];
strcpy(p, "__AA__B__");
```

#### strdup
```c
char* p;
p = strdup("__AA__B__");
free(p);
```

| _  | _  | A  | A  | _  | _  | B  | _  | _  | \0 |
|----|----|----|----|----|----|----|----|----|----|
| 95 | 95 | 65 | 65 | 95 | 95 | 66 | 95 | 95 | 0  |


### 切割字串 strtok

#### 第一次 token 字串

擷取的字串為 `p`，切割字元為 `_`，最後把切割字串的起始位置存在 `token` 中 (表格中第二個箭頭的位置)

```c
char* p = "__AA__B__";
char* token;

token = strtok(p, "_");                // return "AA"
```
由左往右的箭頭分別為:

- 第一個箭頭: string 起始位置
- 第二個箭頭: `token` 指向的位置
- 第三個箭頭: `token` 結尾

| _  | _  | A  | A  | \0 | _  | B  | _  | _  | \0 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 95 | 95 | 65 | 65 | 0  | 95 | 66 | 95 | 95 | 0  |
| ↑  |    | ↑  |    | ↑  |    |    |    |    |    |

#### 第二次之後 token 字串

注意之後要擷取字串的話，`strtok` 的第一個參數為 `NULL`，因為前一個 `strtok` 已經自動幫你指向下一個字串的頭

```c
char* token;

token = strtok(NULL, "_");             // return "B"
```

| _  | _  | A  | A  | \0 | _  | B  | _  | _  | \0 |
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 95 | 95 | 65 | 65 | 0  | 95 | 66 | 95 | 95 | 0  |
|    |    |    |    |    | ↑  | ↑  | ↑  |    |    |

### 連接字串 strstr
```c
char* s;

s = strstr("ABCD", "EFGH");            // return "ABCDEFGH"
```


### 反轉字串 reverse
```c
char* s;

s = reverse("ABCDEFGH");               // return "HGFEDCBA"
```
