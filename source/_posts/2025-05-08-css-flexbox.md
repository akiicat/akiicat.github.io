---
title: CSS Flexbox 是什麼：從入門到精通
tags:
  - CSS
categories:
  - CSS
date: 2025-05-08 08:20:11
---

Flexbox 正式的名稱又稱為 Flexible Box Layout (彈性框排列)，是一種在頁面上排列元素 (element) 的方法，它主要用於將元素排列成行或列，也為歷史上困擾已久的垂直居中和等高列的問題提供了一個簡單的解決方案。要說 flexbox 的缺點，那就是它提供的選項數量太多了。

其實不需要把所有新的屬性都學會才能使用 flexbox，因為通常會使用的就那幾個，其他的屬性主要是用來調整對齊方式和元素之間的間距。

這是我們的 Outline:
- Flexbox 原理
- Flex item 調整彈性項目
- Flex Direction 排列方向
- Flex 參數資料整理

## Flexbox 原理

從我們熟悉的 `display` 屬性開始的。當你在一個元素上設置 `display: flex` 時，它就會變成一個「[彈性容器][Flex_Container]」 ([flexbox container][Flex_Container])，而它的直接子元素則會變成「[彈性項目][Flex_Item]」 ([flexbox item][Flex_Item])。預設情況下，這些彈性項目會橫向排列，從左到右排成一行；彈性容器會像 `block` 區塊元素撐滿可用的寬度，不過裡面的彈性項目不一定會填滿整個容器的寬度，另外它們的高度通常一樣，主要由內容的大小決定。

![CSS Flexbox Principle](images/css-flexbox-1.svg)

Note: 你也可以使用 `display: inline-flex`，這會讓元素變成一個彈性容器，不過它的行為更像是 `inline-block`，而不是一般的 `block` 區塊元素。它會跟其他內聯 (inline) 在一行上，不會像區塊元素那樣自動拉到 100% 寬度。至於裡面的彈性項目，表現基本上和 `display: flex` 是差不多的。實際上不太常會用到這個設定。

這些彈性項目會沿著一條叫「[主軸][Main_Axis]」 ([Main Axis][Main_Axis]) 的方向排列，這條軸是從主軸起點 (Main Start) 延伸到主軸終點 (Main End)，方向從左到右。主軸垂直的方向叫做「[交叉軸][Cross_Axis]」([Cross Axis][Cross_Axis])，是從交叉軸起點 (Cross Start)到交叉軸終點 (Cross End)，方向由上到下。Flexbox 屬性一致使用 start 和 end 術語。

### 導覽列範例 Menu Bar

我們想要建立一個的選單，大部分的項目會靠左排列，只有其中一個項目在右邊：

![CSS Flexbox Image](images/css-flexbox-1.png)

```html
<ul class="site-nav">
  <li><a href="/">Home</a></li>
  <li><a href="/features">Features</a></li>
  <li><a href="/pricing">Pricing</a></li>
  <li><a href="/support">Support</a></li>
  <li class="nav-right"><a href="/about">About</a></li>
</ul>
```

在建立這個選單時，你需要先想清楚哪個元素要設為彈性容器。注意它的子元素會自動變成彈性項目。我們下方的範例，應該把 `<ul>` 設為彈性容器，而它裡面的 `<li>` 就是彈性項目。把瀏覽器預設的清單樣式取消掉，並加上一些顏色設定：

```css
.site-nav {
  display: flex;        /* 設定 flexbox */
  padding: 0.5rem;
  list-style-type: none;
  background-color: #eee;
}

.site-nav > li > a {
  display: block;          
  padding: 0.5em 1em;  
  background-color: #bbb;
  color: black;
  text-decoration: none;     
}
```

在這個範例裡，選單項目的內邊距 `padding` 是加在裡面的 `<a>` 標籤上，而不是直接加在 `<li>` 上。這是因為在點擊時，選單連結的區域都能像連結一樣運作，如果 `padding` 加在 `<li>` 上，則 `<li>` 看起來有一個按鈕，但只有裡面那一小塊 `<a>` 是能點的。

我們把連結設為 `block` 元素。這是因為如果還是維持 `inline` 狀態，那 `<a>` 對父元素高度的影響只會來自行高 `line-height`，而不會包含 `padding` 的高度。但在這裡，我們希望能用 `padding` 來調整連結區域的大小。

我們要在選單之間加點間距。雖然可以用 `margin` 辦到，不過 Flexbox 有個更方便的屬性叫 `gap`，專門用來處理這種情況，比如設定 `gap: 1rem`，就能在每個彈性項目之間加入 1rem 的間距。此外，Flexbox 也支援用 `margin: auto` 來填滿項目之間所有剩下的空間，這個技巧也可以用來把最後一個選單項目推到最右邊。

```css
.site-nav {
  display: flex;
  gap: 1.5rem;     
  padding: 0.5rem;
  list-style-type: none;
  background-color: #eee;
}

.site-nav > .nav-right {
  margin-inline-start: auto;      
}

.site-nav > li > a {
  display: block;          
  padding: 0.5em 1em;  
  background-color: #bbb;
  color: black;
  text-decoration: none;     
}
```

> `margin-inline-start` 的 `inline` 代表的是文字書寫方向，這裡指的是水平的方向，也就是寬度。`start` 代表的是書寫方向的起點，以我們的例子來說就是左邊。

## 調整彈性項目 Flex item

在調整 Flexbox 元素大小時，雖然你還是可以用我們熟悉的 `width` 和 `height` 屬性，不過 flexbox 提供的調整方式比單靠這兩個屬性還要靈活得多。接下來我們要看看其中一個非常實用的屬性：`flex` ([MDN Flex][Flex])。這個屬性能控制彈性項目在主軸方向上的大小（通常是寬度）。

```html
<div class="flexed">
  <div class="column-main">Header</div>
  <div class="column-sidebar">Sidebar</div>
</div>
```

我們將內容分為兩列：左側是主欄，右側是側欄。如果還沒做任何事情來指定兩列的寬度，因此它們會根據其內容自然地調整大小。彈性項目的 `flex` 屬性為您提供了多種選擇，我們先來看最基本的範例熟悉它

```css
.flexed {
  display: flex;
  gap: 1.5rem; 
}
.column-main {
  flex: 2;
}
.column-sidebar {
  flex: 1;
}

/* add some color */
.flexed > div {
  background-color: #eee;
  height: 100px;
  text-align: center;
}
```

![CSS Flexbox Image](images/css-flexbox-2.png)

使用 `.column-main` 和 `.column-sidebar` 來定位列，分別套用 `flex` 2/3 和  1/3 的寬度。這兩個欄位會自動延伸來填滿整個區域，主欄的寬度會是側欄的兩倍。這就是 Flexbox 的厲害之處，它能幫你處理這些計算的細節。

`flex` 這個屬性，其實是三個屬性的縮寫：`flex-grow`、`flex-shrink` 和 `flex-basis`。在這個例子裡，我們只設定了 `flex-grow`，其他兩個就會採用預設值（分別是 1 和 0%）。所以像 `flex: 2` 這樣的寫法，其實等同於 `flex: 2 1 0%`。雖然這種縮寫形式很常用，但如果需要更細的控制，你也可以分別設定這三個屬性。

```css
flex-grow: 2;
flex-shrink: 1;
flex-basis: 0%;
```

我們分別介紹這些屬性，先從第三個 `flex-basis` 開始，因為另外兩個會基於 `flex-basis` 的結果來做調整。

### Flex basis: 設定項目大小 (主軸方向)

`flex-basis` ([MDN Flex Basis][Flex_Basis]) 是用來設定元素沿主軸方向的大小。你可以用任何寬度的單位來設定 `flex-basis`，像是 px、em 或百分比等。

`flex-basis` 的[初始值 initial][Initial] 是 `auto`，如果是 `auto` 的話瀏覽器會先看當前元素有沒有設定寬度 `width`。如果有，就使用 `width` 的值；如果沒有，就依照內容自動決定大小。

也就是說，當你給一個元素設定了非 `auto` 的 `flex-basis` 值，原本的寬度 `width` 設定就會被忽略。

![CSS Flexbox Basis](images/css-flexbox-2.svg)

### Flex grow: 設定項目放大權重

當每個彈性項目有了 `flex-basis` 之後，它們有時還需要放大或縮小，以便適應或填滿彈性容器在主軸上的空間。這時候，就輪到 `flex-grow` ([MDN Flex Glow][Flex_Grow]) 和 `flex-shrink` 發揮作用了。

當每個彈性項目的 `flex-basis` 被計算出來後，這些項目加上它們之間的 `margin` 或 `padding`，總寬度會達到一個固定值。但這個總寬度不一定剛好填滿整個彈性容器，通常會剩下一些空間。這些多出來的空間，會依照每個彈性項目的 `flex-grow` 值來分配。這些值都是非負整數。如果某個項目的 `flex-grow` 是 0，那它就不會比它原本的 `flex-basis` 更大；但如果某些項目的 `flex-grow` 是非零的，那它們就會開始擴展，直到把所有剩下的空間都填滿為止。這樣一來，彈性項目就能完全撐滿容器的寬度。

![CSS Flexbox Grow](images/css-flexbox-3.svg)

設定較高的 `flex-grow` 值，等於是賦予這個元素更多的「權重」，它就會拿走剩餘空間中更大的一部分。比如，一個 `flex-grow: 2` 的項目，會比一個 `flex-grow: 1` 的項目多擴展一倍的寬度。

![CSS Flexbox Grow](images/css-flexbox-4.svg)

這個設定正是我們先前的範例。簡寫寫法 `flex: 2` 和 `flex: 1` 表示 `flex-basis` 是 `0%`，也就是說整個容器寬度的 100% 都算作剩餘空間（扣掉兩欄之間的 1.5rem 間隙）。然後這些剩下的空間會被分配給兩個欄位：第一欄拿到三分之二，第二欄則拿到剩下的三分之一。

`flex` 跟大多數[簡寫屬性 (shorthand property)][Shorthand_properties] 不同的是，這裡如果你省略了一些值，它們不會被還原成[初始值][Initial]，而是會自動套用一些實用的預設值：也就是 `flex-grow: 1`、`flex-shrink: 1` 和 `flex-basis: 0%`。這些預設通常就是你需要的設定。這種情況就會建議你使用 `flex` 的簡寫屬性，而不是只單獨設定 `flex-grow`。

### Flex shrink: 設定項目縮小權重

`flex-shrink` ([MDN Flex Shrink][Flex_Shrink]) 屬性的作用和 `flex-grow` 類似。當彈性項目的 `flex-basis` 確定後，如果這些項目的總寬度超出了彈性容器的可用空間，就可能會出現溢位 (overflow)。

![CSS Flexbox Shrink](images/css-flexbox-5.svg)

這時候，每個項目的 `flex-shrink` 值就決定它是否應該縮小來避免溢出。如果某個項目的 `flex-shrink` 設為 0，那它就不會縮小。而如果設為大於 0 的數值，它就會縮小，直到不再超出容器。值越高的項目會比值低的項目縮小得更多，縮小的比例是根據 `flex-shrink` 的值來分配的。同時也會考慮原本的大小，所以當權重相同時，大元素通常會比小元素縮得更多。

在頁面佈局中，這提供了另一種調整欄位寬度的方法。你可以設定每欄的 `flex-basis` 為所需的百分比（例如 66.67% 和 33.33%），這樣它們加上中間的 1.5rem 間隙總寬度就會多出 1.5rem。然後將兩欄的 `flex-shrink` 都設為 1，這樣它們就會各自縮小一點來適應容器的寬度。

```css
.column-main {
  flex: 66.67%;     /* 等同於 flex: 1 1 66.67% */
}
.column-sidebar {
  flex: 33.33%;     /* 等同於 flex: 1 1 33.33% */
}
```

這是一種不同的做法，但一樣能有效地達到和之前相同的效果。(跟使用 `flex-grow` 的方式相比，會有一些微小的差別，使用 `flex-shrink` 的例子左邊欄位會大一點點)

### Flex 範例與各種變化

你可以用很多種方式來運用 `flex` 屬性。就像我們在頁面上做的那樣，可以透過 `flex-grow` 或 `flex-basis` 來設定欄位的比例。你也可以設定某些欄是固定寬度，而其他欄則根據視窗大小變化，形成流動欄位 (fluid column)。

![CSS Flexbox Example](images/css-flexbox-6.svg)

第三個例子展示的是以前常被稱作「聖杯佈局」版型 ([Holy Grail Layout][Holy_Grail])，這在早期的 CSS 裡是非常難實現的一種排版方式。在這個佈局中，左右兩側的欄位寬度是固定的，而中間那欄是「流動的」，也就是說它會自動擴展來填滿剩下的空間。最特別的是，這三個欄位的高度會自動一致，依內容而定。

## Flex Direction

Flexbox 裡另一個很重要的功能，就是可以改變排列項目的軸向。你可以透過設定彈性容器的 `flex-direction` 屬性來控制這一點。它的預設值是 `row`，讓項目沿著橫向排列，也就是沿著[內聯方向][Block_and_inline_layout]排列。如果你改成 `flex-direction: column`，那項目就會垂直堆疊，也就是沿著[區塊方向][Block_and_inline_layout]排列。Flexbox 同時還支援 `row-reverse`，可以讓項目從右往左排，或是 `column-reverse`，讓項目從下往上排。

![CSS Flexbox Direction](images/css-flexbox-7.svg)

[內聯方向 (inline direction)][Block_and_inline_layout]是書寫時的方向。英文是由左到右書寫，因此內聯方向代表著寬度 width。但中文可能由上到下書寫，則內聯方向代表高度 height。區塊方向 (block) 則是跟書寫垂直的方向。

## 對齊、間距、其他參數

Flex 提供了各式各樣的設定。通常來說，這些步驟可以幫你把元素大致排到想要的位置：

- 先找出要當作容器的元素，然後在它上面加上 `display: flex`，讓它變成彈性容器
- 如果需要的話，可以在容器上設定 `gap` 或是改變排列方向`flex-direction`
- 接著在需要控制大小的彈性項目上設定 `flex` 屬性

之後如果需要再微調，你可以再加上其他屬性。

**彈性容器參數表格：**

![CSS Flexbox Cheat Sheet](images/css-flexbox-8.svg)

**彈性項目參數表格：**

![CSS Flexbox Cheat Sheet](images/css-flexbox-9.svg)

### 彈性容器參數

#### Flex Wrap

`flex-wrap` 屬性可以讓彈性項目換行，也可以讓它們排成多行。這個屬性有三個值可以設：`nowrap`（預設值）、`wrap` 和 `wrap-reverse`。如果開啟了換行，項目就不會再根據它們的 `flex-shrink` 值來縮小，而是會直接換到新的一行，避免擠不下。
如果彈性方向是 `column` 或 `column-reverse`，那開啟 `flex-wrap` 會讓項目換到新的一列。不過，只有當容器的高度有設定上限時才會換列，否則容器會自己變高，以容納所有的彈性項目。

#### Flex Flow

`flex-flow` 是 `flex-direction` 和 `flex-wrap` 的簡寫。例如，`flex-flow: column wrap` 就表示彈性項目會從上往下排列，必要的時候會換行，排到新的那一列上。

#### Justify Content

當彈性項目沒有填滿整個容器時，`justify-content` 屬性可以用來控制它們在主軸上的排列方式。它支援幾個常用的值：`flex-start`、`flex-end`、`center`、`space-between`、`space-around` 和 `space-evenly`。

預設的 `flex-start` 會把項目排在主軸的起點，也就是從左邊開始排，而且彼此之間不會有間距，除非你有加 `gap` 或設定 `margin`。`flex-end` 則是把項目排在主軸的尾端，看起來會集中在右邊。`start` 和 `end` 是邏輯值，會根據書寫模式來決定項目對齊左邊還是右邊，不管主軸方向為何；`left` 和 `right` 是絕對的，永遠左對齊或右對齊，跟書寫模式和主軸方向無關。

如果你想讓項目之間平均分配空間，有三種方式可選。`space-between` 是把第一個項目貼齊主軸起點，最後一個貼齊主軸終點，其他項目平均分布在中間；`space-around` 會在第一個和最後一個項目外側加上間距，不過這些間距只有中間間距的一半；而 `space-evenly` 則是所有的間距都相等，兩端外側間距與中間的間距大小一樣。

要注意的是，這些間距是在算完 `margin` 和 `flex-grow` 值之後才會套用。所以如果有項目的 `flex-grow` 不為 0，或是用了主軸方向的 `auto` margin，那 `justify-content` 可能不會發揮作用。

#### Align Content

如果有開啟換行（也就是用了 `flex-wrap`），這個屬性可以用來控制每一行在容器裡橫向的間距。它支援的值包括：`flex-start`、`flex-end`、`start`、`end`、`center`、`stretch`（預設值）、`space-between`、`space-around` 和 `space-evenly`。
這些值的排列方式，其實跟前面提到的 `justify-content` 很像，只是是用在每一行之間的距離上。

另外還有一個叫 `place-content` 的屬性，其實就是 `align-content` 和 `justify-content` 的簡寫。

#### Align Items

`justify-content` 是用來控制項目在『主軸』上的對齊方式，而 `align-items` 則是用來調整項目在『交叉軸』上的對齊方式。
它的預設值是 `stretch`，也就是讓所有項目自動撐滿容器的高度（橫向排列時）或寬度（直向排列時），這樣可以讓每一列的高度都一樣，看起來更整齊。

不過如果你不想讓項目撐滿，也可以用其他的對齊方式，這些方式其實有點像 CSS 裡的垂直對齊。像是：
- `flex-start` 和 `flex-end`：讓項目靠交叉軸的起點或終點對齊（通常是一行的頂部或底部）。
- `start` 和 `end`：這是根據容器的書寫方向來決定的邏輯對齊方式。
- `self-start` 和 `self-end`：這會根據每個彈性項目的書寫方向來對齊，如果彈性項目跟彈性容器的書寫方向不同，就會跟 `start`、`end` 會有差異。
- `center`：把項目垂直置中對齊。
- `baseline`：讓每個項目中文字的第一行基線對齊（基線就是字的底部那條線）。這在你有些項目字體比較大、有標題，而其他項目是一般小字時特別有用。

另外，你也可以指定對齊的是「第一行」或「最後一行」的基線，比如用 `align-items: last baseline`，就是讓每個項目中最後一行的文字基線對齊。

> `justify-content` 和 `align-items` 這兩個屬名很容易搞混。可以把它們想像成 Word 裡的文字對齊功能。像是 `justify-content` 有點像設定文字是靠左、靠右還是左右分散一樣，是用來控制水平方向的排列。
而 `align-items` 就像是在做垂直對齊一樣，用來調整內部項目在上下方向上的對齊方式。

`place-items` 屬性是 `align-items` 和 `justify-items` 的簡寫。但 `justify-items` 只有在 grid 才有用，對於 flexbox 是沒效果的。

### 彈性項目參數

前面我們已經介紹過 `flex-grow`、`flex-shrink`、`flex-basis`，還有它們的縮寫 `flex`。接下來要看的兩個，是跟彈性項目有關的額外屬性：`align-self` 和 `order`。

#### Align Self

`align-self` 是用來控制單個彈性項目在容器交叉軸上的對齊方式。它的作用跟容器用的 `align-items` 很像，不同的是，`align-self` 可以讓你針對每個項目個別設定對齊方式。

預設值是 `auto`，也就是跟著容器的 `align-items` 設定。但如果你給它設定別的值，那就會覆蓋容器的對齊設定。

`align-self` 的值和 `align-items` 一樣，包括：`flex-start`、`flex-end`、`start`、`end`、`self-start`、`self-end`、`center`、`stretch`，還有 `baseline`（基線對齊）。

另外 `place-self` 屬性，它是 `align-self` 和 `justify-self` 的簡寫。注意 `justify-self` 是用在 grid 裡的，在 flexbox 不會有作用。

#### Order

一般來說，彈性項目的排列順序會跟它們在 HTML 原始碼裡出現的順序一樣，從主軸的起點開始一個接一個排下去。不過，你可以用 `order` 屬性來改變這個排列順序。

你可以設定任何整數，正的、負的都可以。如果好幾個項目的 `order` 值一樣，它們之間還是會照 HTML 的順序來排列。預設情況下，所有彈性項目的 `order` 值都是 `0`。舉個例子，設成 `-1` 的項目會被排到最前面，而設成 `1` 的就會排到後面。你可以自由地給每個項目設定不同的值來調整它們的順序，而且這些值不需要連續的。

> 注意：雖然 `order` 很方便，使用上會改變了畫面上的視覺排列順序，但原始碼順序沒改，可能會影響到網站的無障礙體驗。像是用鍵盤（Tab 鍵）切換時，瀏覽器通常還是會按照 HTML 原始碼順序來移動焦點；螢幕閱讀器在讀取時也一樣，大多還是會依照原本的 HTML 順序，這樣可能會讓使用者感到混亂。

## Reference

- [CSS in Depth, Second Edition][1]
- [MDN Flex Container][Flex_Container]
- [MDN Flex Item][Flex_Item]
- [MDN Main Axis][Main_Axis]
- [MDN Cross Axis][Cross_Axis]
- [MDN Initial][Initial]
- [MDN Flex Grow][Flex_Grow]
- [MDN Flex Basis][Flex_Basis]
- [MDN Flex Shrink][Flex_Shrink]
- [MDN Flex][Flex]
- [MDN Shorthand Properties][Shorthand_properties]
- [MDN Block and Inline Layout][Block_and_inline_layout]
- [Wiki Holy Grail Layout][Holy_Grail]

[1]: https://www.manning.com/books/css-in-depth-second-edition
[Flex_Container]: https://developer.mozilla.org/zh-CN/docs/Glossary/Flex_Container
[Flex_Item]: https://developer.mozilla.org/zh-CN/docs/Glossary/Flex_Item
[Main_Axis]: https://developer.mozilla.org/zh-CN/docs/Glossary/Main_Axis
[Cross_Axis]: https://developer.mozilla.org/zh-CN/docs/Glossary/Cross_Axis
[Initial]: https://developer.mozilla.org/en-US/docs/Web/CSS/initial
[Flex_Grow]: https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-grow
[Flex_Basis]: https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-basis
[Flex_Shrink]: https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-shrink
[Flex]: https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex
[Shorthand_properties]: https://developer.mozilla.org/zh-TW/docs/Web/CSS/CSS_cascade/Shorthand_properties
[Holy_Grail]: https://en.wikipedia.org/wiki/Holy_grail_(web_design)
[Block_and_inline_layout]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_display/Block_and_inline_layout_in_normal_flow
