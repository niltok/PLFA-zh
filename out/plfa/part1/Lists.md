---
src       : "src/plfa/part1/Lists.lagda.md"
title     : "Lists: 列表与高阶函数"
layout    : page
prev      : /Decidable/
permalink : /Lists/
next      : /Lambda/
translators: ["Fangyi Zhou"]
progress  : 100
---

{% raw %}<pre class="Agda"><a id="170" class="Keyword">module</a> <a id="177" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}" class="Module">plfa.part1.Lists</a> <a id="194" class="Keyword">where</a>
</pre>{% endraw %}
{::comment}
This chapter discusses the list data type.  It gives further examples
of many of the techniques we have developed so far, and provides
examples of polymorphic types and higher-order functions.
{:/}

本章节讨论列表（List）数据类型。我们用列表作为例子，来使用我们之前学习的技巧。同时，列表也给我们带来多态类型（Polymorphic Types）和高阶函数（Higher-order Functions）的例子。

{::comment}
## Imports
{:/}

## 导入

{% raw %}<pre class="Agda"><a id="567" class="Keyword">import</a> <a id="574" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="612" class="Symbol">as</a> <a id="615" class="Module">Eq</a>
<a id="618" class="Keyword">open</a> <a id="623" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="626" class="Keyword">using</a> <a id="632" class="Symbol">(</a><a id="633" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">_≡_</a><a id="636" class="Symbol">;</a> <a id="638" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a><a id="642" class="Symbol">;</a> <a id="644" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a><a id="647" class="Symbol">;</a> <a id="649" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#984" class="Function">trans</a><a id="654" class="Symbol">;</a> <a id="656" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a><a id="660" class="Symbol">)</a>
<a id="662" class="Keyword">open</a> <a id="667" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2499" class="Module">Eq.≡-Reasoning</a>
<a id="682" class="Keyword">open</a> <a id="687" class="Keyword">import</a> <a id="694" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.html" class="Module">Data.Bool</a> <a id="704" class="Keyword">using</a> <a id="710" class="Symbol">(</a><a id="711" href="Agda.Builtin.Bool.html#135" class="Datatype">Bool</a><a id="715" class="Symbol">;</a> <a id="717" href="Agda.Builtin.Bool.html#160" class="InductiveConstructor">true</a><a id="721" class="Symbol">;</a> <a id="723" href="Agda.Builtin.Bool.html#154" class="InductiveConstructor">false</a><a id="728" class="Symbol">;</a> <a id="730" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.Base.html#1480" class="Function">T</a><a id="731" class="Symbol">;</a> <a id="733" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.Base.html#1015" class="Function Operator">_∧_</a><a id="736" class="Symbol">;</a> <a id="738" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.Base.html#1073" class="Function Operator">_∨_</a><a id="741" class="Symbol">;</a> <a id="743" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.Base.html#961" class="Function">not</a><a id="746" class="Symbol">)</a>
<a id="748" class="Keyword">open</a> <a id="753" class="Keyword">import</a> <a id="760" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.html" class="Module">Data.Nat</a> <a id="769" class="Keyword">using</a> <a id="775" class="Symbol">(</a><a id="776" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="777" class="Symbol">;</a> <a id="779" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a><a id="783" class="Symbol">;</a> <a id="785" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a><a id="788" class="Symbol">;</a> <a id="790" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a><a id="793" class="Symbol">;</a> <a id="795" href="Agda.Builtin.Nat.html#501" class="Primitive Operator">_*_</a><a id="798" class="Symbol">;</a> <a id="800" href="Agda.Builtin.Nat.html#388" class="Primitive Operator">_∸_</a><a id="803" class="Symbol">;</a> <a id="805" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#895" class="Datatype Operator">_≤_</a><a id="808" class="Symbol">;</a> <a id="810" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#960" class="InductiveConstructor">s≤s</a><a id="813" class="Symbol">;</a> <a id="815" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#918" class="InductiveConstructor">z≤n</a><a id="818" class="Symbol">)</a>
<a id="820" class="Keyword">open</a> <a id="825" class="Keyword">import</a> <a id="832" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="852" class="Keyword">using</a>
  <a id="860" class="Symbol">(</a><a id="861" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11578" class="Function">+-assoc</a><a id="868" class="Symbol">;</a> <a id="870" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11679" class="Function">+-identityˡ</a><a id="881" class="Symbol">;</a> <a id="883" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11734" class="Function">+-identityʳ</a><a id="894" class="Symbol">;</a> <a id="896" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#18464" class="Function">*-assoc</a><a id="903" class="Symbol">;</a> <a id="905" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#17362" class="Function">*-identityˡ</a><a id="916" class="Symbol">;</a> <a id="918" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#17426" class="Function">*-identityʳ</a><a id="929" class="Symbol">)</a>
<a id="931" class="Keyword">open</a> <a id="936" class="Keyword">import</a> <a id="943" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html" class="Module">Relation.Nullary</a> <a id="960" class="Keyword">using</a> <a id="966" class="Symbol">(</a><a id="967" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#535" class="Function Operator">¬_</a><a id="969" class="Symbol">;</a> <a id="971" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#605" class="Datatype">Dec</a><a id="974" class="Symbol">;</a> <a id="976" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#641" class="InductiveConstructor">yes</a><a id="979" class="Symbol">;</a> <a id="981" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#668" class="InductiveConstructor">no</a><a id="983" class="Symbol">)</a>
<a id="985" class="Keyword">open</a> <a id="990" class="Keyword">import</a> <a id="997" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html" class="Module">Data.Product</a> <a id="1010" class="Keyword">using</a> <a id="1016" class="Symbol">(</a><a id="1017" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1162" class="Function Operator">_×_</a><a id="1020" class="Symbol">;</a> <a id="1022" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1364" class="Function">∃</a><a id="1023" class="Symbol">;</a> <a id="1025" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1783" class="Function">∃-syntax</a><a id="1033" class="Symbol">)</a> <a id="1035" class="Keyword">renaming</a> <a id="1044" class="Symbol">(</a><a id="1045" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">_,_</a> <a id="1049" class="Symbol">to</a> <a id="1052" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨_,_⟩</a><a id="1057" class="Symbol">)</a>
<a id="1059" class="Keyword">open</a> <a id="1064" class="Keyword">import</a> <a id="1071" href="https://agda.github.io/agda-stdlib/v1.1/Function.html" class="Module">Function</a> <a id="1080" class="Keyword">using</a> <a id="1086" class="Symbol">(</a><a id="1087" href="https://agda.github.io/agda-stdlib/v1.1/Function.html#1099" class="Function Operator">_∘_</a><a id="1090" class="Symbol">)</a>
<a id="1092" class="Keyword">open</a> <a id="1097" class="Keyword">import</a> <a id="1104" href="https://agda.github.io/agda-stdlib/v1.1/Level.html" class="Module">Level</a> <a id="1110" class="Keyword">using</a> <a id="1116" class="Symbol">(</a><a id="1117" href="Agda.Primitive.html#408" class="Postulate">Level</a><a id="1122" class="Symbol">)</a>
<a id="1124" class="Keyword">open</a> <a id="1129" class="Keyword">import</a> <a id="1136" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Isomorphism.md %}{% raw %}" class="Module">plfa.part1.Isomorphism</a> <a id="1159" class="Keyword">using</a> <a id="1165" class="Symbol">(</a><a id="1166" href="plfa.part1.Isomorphism.html#5849" class="Record Operator">_≃_</a><a id="1169" class="Symbol">;</a> <a id="1171" href="plfa.part1.Isomorphism.html#15292" class="Record Operator">_⇔_</a><a id="1174" class="Symbol">)</a>
</pre>{% endraw %}

{::comment}
## Lists
{:/}

## 列表

{::comment}
Lists are defined in Agda as follows:
{:/}

Agda 中的列表如下定义：
{% raw %}<pre class="Agda"><a id="1291" class="Keyword">data</a> <a id="List"></a><a id="1296" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1296" class="Datatype">List</a> <a id="1301" class="Symbol">(</a><a id="1302" href="plfa.part1.Lists.html#1302" class="Bound">A</a> <a id="1304" class="Symbol">:</a> <a id="1306" class="PrimitiveType">Set</a><a id="1309" class="Symbol">)</a> <a id="1311" class="Symbol">:</a> <a id="1313" class="PrimitiveType">Set</a> <a id="1317" class="Keyword">where</a>
  <a id="List.[]"></a><a id="1325" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a>  <a id="1329" class="Symbol">:</a> <a id="1331" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="1336" href="plfa.part1.Lists.html#1302" class="Bound">A</a>
  <a id="List._∷_"></a><a id="1340" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">_∷_</a> <a id="1344" class="Symbol">:</a> <a id="1346" href="plfa.part1.Lists.html#1302" class="Bound">A</a> <a id="1348" class="Symbol">→</a> <a id="1350" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="1355" href="plfa.part1.Lists.html#1302" class="Bound">A</a> <a id="1357" class="Symbol">→</a> <a id="1359" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="1364" href="plfa.part1.Lists.html#1302" class="Bound">A</a>

<a id="1367" class="Keyword">infixr</a> <a id="1374" class="Number">5</a> <a id="1376" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">_∷_</a>
</pre>{% endraw %}
{::comment}
Let's unpack this definition. If `A` is a set, then `List A` is a set.
The next two lines tell us that `[]` (pronounced _nil_) is a list of
type `A` (often called the _empty_ list), and that `_∷_` (pronounced
_cons_, short for _constructor_) takes a value of type `A` and a value
of type `List A` and returns a value of type `List A`.  Operator `_∷_`
has precedence level 5 and associates to the right.
{:/}

我们来仔细研究这个定义。如果 `A` 是个集合，那么 `List A` 也是一个集合。接下来的两行告诉我们
`[]` （读作 *nil*）是一个类型为 `A` 的列表（通常被叫做*空*列表），`_∷_`（读作 *cons*，是
*constructor* 的简写）取一个类型为 `A` 的值，和一个类型为 `List A` 的值，返回一个类型为
`List A` 的值。`_∷_` 运算符的优先级是 5，向右结合。

{::comment}
For example,
{:/}

例如：

{% raw %}<pre class="Agda"><a id="2055" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#2055" class="Function">_</a> <a id="2057" class="Symbol">:</a> <a id="2059" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="2064" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="2066" class="Symbol">_</a> <a id="2068" class="Symbol">=</a> <a id="2070" class="Number">0</a> <a id="2072" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="2074" class="Number">1</a> <a id="2076" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="2078" class="Number">2</a> <a id="2080" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="2082" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
</pre>{% endraw %}
{::comment}
denotes the list of the first three natural numbers.  Since `_∷_`
associates to the right, the term parses as `0 ∷ (1 ∷ (2 ∷ []))`.
Here `0` is the first element of the list, called the _head_,
and `1 ∷ (2 ∷ [])` is a list of the remaining elements, called the
_tail_. A list is a strange beast: it has a head and a tail,
nothing in between, and the tail is itself another list!
{:/}

表示了一个三个自然数的列表。因为 `_∷_` 向右结合，这一项被解析成 `0 ∷ (1 ∷ (2 ∷ []))`。在这里，`0` 是列表的第一个元素，称之为*头*（Head），`1 ∷ (2 ∷ [])` 是剩下元素的列表，称之为*尾*（Tail）。列表是一个奇怪的怪兽：它有一头一尾，中间没有东西，然而它的尾巴又是一个列表！

{::comment}
As we've seen, parameterised types can be translated to
indexed types. The definition above is equivalent to the following:
{:/}

正如我们所见，参数化的类型可以被转换成索引类型。上面的定义与下列等价：

{% raw %}<pre class="Agda"><a id="2837" class="Keyword">data</a> <a id="List′"></a><a id="2842" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#2842" class="Datatype">List′</a> <a id="2848" class="Symbol">:</a> <a id="2850" class="PrimitiveType">Set</a> <a id="2854" class="Symbol">→</a> <a id="2856" class="PrimitiveType">Set</a> <a id="2860" class="Keyword">where</a>
  <a id="List′.[]′"></a><a id="2868" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#2868" class="InductiveConstructor">[]′</a>  <a id="2873" class="Symbol">:</a> <a id="2875" class="Symbol">∀</a> <a id="2877" class="Symbol">{</a><a id="2878" href="plfa.part1.Lists.html#2878" class="Bound">A</a> <a id="2880" class="Symbol">:</a> <a id="2882" class="PrimitiveType">Set</a><a id="2885" class="Symbol">}</a> <a id="2887" class="Symbol">→</a> <a id="2889" href="plfa.part1.Lists.html#2842" class="Datatype">List′</a> <a id="2895" href="plfa.part1.Lists.html#2878" class="Bound">A</a>
  <a id="List′._∷′_"></a><a id="2899" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#2899" class="InductiveConstructor Operator">_∷′_</a> <a id="2904" class="Symbol">:</a> <a id="2906" class="Symbol">∀</a> <a id="2908" class="Symbol">{</a><a id="2909" href="plfa.part1.Lists.html#2909" class="Bound">A</a> <a id="2911" class="Symbol">:</a> <a id="2913" class="PrimitiveType">Set</a><a id="2916" class="Symbol">}</a> <a id="2918" class="Symbol">→</a> <a id="2920" href="plfa.part1.Lists.html#2909" class="Bound">A</a> <a id="2922" class="Symbol">→</a> <a id="2924" href="plfa.part1.Lists.html#2842" class="Datatype">List′</a> <a id="2930" href="plfa.part1.Lists.html#2909" class="Bound">A</a> <a id="2932" class="Symbol">→</a> <a id="2934" href="plfa.part1.Lists.html#2842" class="Datatype">List′</a> <a id="2940" href="plfa.part1.Lists.html#2909" class="Bound">A</a>
</pre>{% endraw %}
{::comment}
Each constructor takes the parameter as an implicit argument.
Thus, our example list could also be written:
{:/}

每个构造子将参数作为隐式参数。因此我们列表的例子也可以写作：

{% raw %}<pre class="Agda"><a id="3109" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3109" class="Function">_</a> <a id="3111" class="Symbol">:</a> <a id="3113" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="3118" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="3120" class="Symbol">_</a> <a id="3122" class="Symbol">=</a> <a id="3124" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">_∷_</a> <a id="3128" class="Symbol">{</a><a id="3129" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="3130" class="Symbol">}</a> <a id="3132" class="Number">0</a> <a id="3134" class="Symbol">(</a><a id="3135" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">_∷_</a> <a id="3139" class="Symbol">{</a><a id="3140" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="3141" class="Symbol">}</a> <a id="3143" class="Number">1</a> <a id="3145" class="Symbol">(</a><a id="3146" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">_∷_</a> <a id="3150" class="Symbol">{</a><a id="3151" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="3152" class="Symbol">}</a> <a id="3154" class="Number">2</a> <a id="3156" class="Symbol">(</a><a id="3157" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="3160" class="Symbol">{</a><a id="3161" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="3162" class="Symbol">})))</a>
</pre>{% endraw %}
{::comment}
where here we have provided the implicit parameters explicitly.
{:/}

此处我们将隐式参数显式地声明。

{::comment}
Including the pragma:
{:/}

包含下面的编译器指令

    {-# BUILTIN LIST List #-}

{::comment}
tells Agda that the type `List` corresponds to the Haskell type
list, and the constructors `[]` and `_∷_` correspond to nil and
cons respectively, allowing a more efficient representation of lists.
{:/}

告诉 Agda，`List` 类型对应了 Haskell 的列表类型，构造子 `[]` 和 `_∷_`
分别代表了 nil 和 cons，这可以让列表的表示更加的有效率。

{::comment}
## List syntax
{:/}

## 列表语法

{::comment}
We can write lists more conveniently by introducing the following definitions:
{:/}

我们可以用下面的定义，更简便地表示列表：

{% raw %}<pre class="Agda"><a id="3822" class="Keyword">pattern</a> <a id="[_]"></a><a id="3830" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3830" class="InductiveConstructor Operator">[_]</a> <a id="3834" href="plfa.part1.Lists.html#3838" class="Bound">z</a> <a id="3836" class="Symbol">=</a> <a id="3838" href="plfa.part1.Lists.html#3838" class="Bound">z</a> <a id="3840" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3842" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="3845" class="Keyword">pattern</a> <a id="[_,_]"></a><a id="3853" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3853" class="InductiveConstructor Operator">[_,_]</a> <a id="3859" href="plfa.part1.Lists.html#3865" class="Bound">y</a> <a id="3861" href="plfa.part1.Lists.html#3869" class="Bound">z</a> <a id="3863" class="Symbol">=</a> <a id="3865" href="plfa.part1.Lists.html#3865" class="Bound">y</a> <a id="3867" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3869" href="plfa.part1.Lists.html#3869" class="Bound">z</a> <a id="3871" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3873" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="3876" class="Keyword">pattern</a> <a id="[_,_,_]"></a><a id="3884" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3884" class="InductiveConstructor Operator">[_,_,_]</a> <a id="3892" href="plfa.part1.Lists.html#3900" class="Bound">x</a> <a id="3894" href="plfa.part1.Lists.html#3904" class="Bound">y</a> <a id="3896" href="plfa.part1.Lists.html#3908" class="Bound">z</a> <a id="3898" class="Symbol">=</a> <a id="3900" href="plfa.part1.Lists.html#3900" class="Bound">x</a> <a id="3902" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3904" href="plfa.part1.Lists.html#3904" class="Bound">y</a> <a id="3906" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3908" href="plfa.part1.Lists.html#3908" class="Bound">z</a> <a id="3910" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3912" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="3915" class="Keyword">pattern</a> <a id="[_,_,_,_]"></a><a id="3923" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3923" class="InductiveConstructor Operator">[_,_,_,_]</a> <a id="3933" href="plfa.part1.Lists.html#3943" class="Bound">w</a> <a id="3935" href="plfa.part1.Lists.html#3947" class="Bound">x</a> <a id="3937" href="plfa.part1.Lists.html#3951" class="Bound">y</a> <a id="3939" href="plfa.part1.Lists.html#3955" class="Bound">z</a> <a id="3941" class="Symbol">=</a> <a id="3943" href="plfa.part1.Lists.html#3943" class="Bound">w</a> <a id="3945" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3947" href="plfa.part1.Lists.html#3947" class="Bound">x</a> <a id="3949" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3951" href="plfa.part1.Lists.html#3951" class="Bound">y</a> <a id="3953" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3955" href="plfa.part1.Lists.html#3955" class="Bound">z</a> <a id="3957" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3959" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="3962" class="Keyword">pattern</a> <a id="[_,_,_,_,_]"></a><a id="3970" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3970" class="InductiveConstructor Operator">[_,_,_,_,_]</a> <a id="3982" href="plfa.part1.Lists.html#3994" class="Bound">v</a> <a id="3984" href="plfa.part1.Lists.html#3998" class="Bound">w</a> <a id="3986" href="plfa.part1.Lists.html#4002" class="Bound">x</a> <a id="3988" href="plfa.part1.Lists.html#4006" class="Bound">y</a> <a id="3990" href="plfa.part1.Lists.html#4010" class="Bound">z</a> <a id="3992" class="Symbol">=</a> <a id="3994" href="plfa.part1.Lists.html#3994" class="Bound">v</a> <a id="3996" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="3998" href="plfa.part1.Lists.html#3998" class="Bound">w</a> <a id="4000" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4002" href="plfa.part1.Lists.html#4002" class="Bound">x</a> <a id="4004" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4006" href="plfa.part1.Lists.html#4006" class="Bound">y</a> <a id="4008" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4010" href="plfa.part1.Lists.html#4010" class="Bound">z</a> <a id="4012" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4014" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="4017" class="Keyword">pattern</a> <a id="[_,_,_,_,_,_]"></a><a id="4025" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#4025" class="InductiveConstructor Operator">[_,_,_,_,_,_]</a> <a id="4039" href="plfa.part1.Lists.html#4053" class="Bound">u</a> <a id="4041" href="plfa.part1.Lists.html#4057" class="Bound">v</a> <a id="4043" href="plfa.part1.Lists.html#4061" class="Bound">w</a> <a id="4045" href="plfa.part1.Lists.html#4065" class="Bound">x</a> <a id="4047" href="plfa.part1.Lists.html#4069" class="Bound">y</a> <a id="4049" href="plfa.part1.Lists.html#4073" class="Bound">z</a> <a id="4051" class="Symbol">=</a> <a id="4053" href="plfa.part1.Lists.html#4053" class="Bound">u</a> <a id="4055" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4057" href="plfa.part1.Lists.html#4057" class="Bound">v</a> <a id="4059" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4061" href="plfa.part1.Lists.html#4061" class="Bound">w</a> <a id="4063" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4065" href="plfa.part1.Lists.html#4065" class="Bound">x</a> <a id="4067" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4069" href="plfa.part1.Lists.html#4069" class="Bound">y</a> <a id="4071" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4073" href="plfa.part1.Lists.html#4073" class="Bound">z</a> <a id="4075" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4077" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
</pre>{% endraw %}
{::comment}
This is our first use of pattern declarations.  For instance,
the third line tells us that `[ x , y , z ]` is equivalent to
`x ∷ y ∷ z ∷ []`, and permits the former to appear either in
a pattern on the left-hand side of an equation, or a term
on the right-hand side of an equation.
{:/}

这是我们第一次使用模式声明。举例来说，第三行告诉我们 `[ x , y , z ]` 等价于
`x ∷ y ∷ z ∷ []`。前者可以在模式或者等式的左手边，或者是等式右手边的项中出现。

{::comment}
## Append
{:/}

## 附加

{::comment}
Our first function on lists is written `_++_` and pronounced
_append_:
{:/}

我们对于列表的第一个函数写作 `_++_`，读作*附加*（Append）：

{% raw %}<pre class="Agda"><a id="4648" class="Keyword">infixr</a> <a id="4655" class="Number">5</a> <a id="4657" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#4663" class="Function Operator">_++_</a>

<a id="_++_"></a><a id="4663" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#4663" class="Function Operator">_++_</a> <a id="4668" class="Symbol">:</a> <a id="4670" class="Symbol">∀</a> <a id="4672" class="Symbol">{</a><a id="4673" href="plfa.part1.Lists.html#4673" class="Bound">A</a> <a id="4675" class="Symbol">:</a> <a id="4677" class="PrimitiveType">Set</a><a id="4680" class="Symbol">}</a> <a id="4682" class="Symbol">→</a> <a id="4684" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="4689" href="plfa.part1.Lists.html#4673" class="Bound">A</a> <a id="4691" class="Symbol">→</a> <a id="4693" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="4698" href="plfa.part1.Lists.html#4673" class="Bound">A</a> <a id="4700" class="Symbol">→</a> <a id="4702" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="4707" href="plfa.part1.Lists.html#4673" class="Bound">A</a>
<a id="4709" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a>       <a id="4718" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="4721" href="plfa.part1.Lists.html#4721" class="Bound">ys</a>  <a id="4725" class="Symbol">=</a>  <a id="4728" href="plfa.part1.Lists.html#4721" class="Bound">ys</a>
<a id="4731" class="Symbol">(</a><a id="4732" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#4732" class="Bound">x</a> <a id="4734" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4736" href="plfa.part1.Lists.html#4736" class="Bound">xs</a><a id="4738" class="Symbol">)</a> <a id="4740" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="4743" href="plfa.part1.Lists.html#4743" class="Bound">ys</a>  <a id="4747" class="Symbol">=</a>  <a id="4750" href="plfa.part1.Lists.html#4732" class="Bound">x</a> <a id="4752" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="4754" class="Symbol">(</a><a id="4755" href="plfa.part1.Lists.html#4736" class="Bound">xs</a> <a id="4758" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="4761" href="plfa.part1.Lists.html#4743" class="Bound">ys</a><a id="4763" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
The type `A` is an implicit argument to append, making it a _polymorphic_
function (one that can be used at many types). A list appended to the empty list
yields the list itself. A list appended to a non-empty list yields a list with
the head the same as the head of the non-empty list, and a tail the same as the
other list appended to tail of the non-empty list.
{:/}

The type `A` is an implicit argument to append, making it a _polymorphic_
function (one that can be used at many types). A list appended to the empty list
yields the list itself. A list appended to a non-empty list yields a list with
the head the same as the head of the non-empty list, and a tail the same as the
other list appended to tail of the non-empty list.

`A` 类型是附加的隐式参数，这让这个函数变为一个**多态（Polymorphic）**函数
（即可以用作多种类型）。一个列表附加到空列表会得到该列表本身；一个列表附加到非空列表所得到的列表，其头与附加到的非空列表相同，尾与所附加的列表相同。

{::comment}
Here is an example, showing how to compute the result
of appending two lists:
{:/}

我们举个例子，来展示将两个列表附加的计算过程：

{% raw %}<pre class="Agda"><a id="5768" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#5768" class="Function">_</a> <a id="5770" class="Symbol">:</a> <a id="5772" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="5774" class="Number">0</a> <a id="5776" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="5778" class="Number">1</a> <a id="5780" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="5782" class="Number">2</a> <a id="5784" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="5786" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="5789" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">[</a> <a id="5791" class="Number">3</a> <a id="5793" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">,</a> <a id="5795" class="Number">4</a> <a id="5797" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">]</a> <a id="5799" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="5801" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">[</a> <a id="5803" class="Number">0</a> <a id="5805" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">,</a> <a id="5807" class="Number">1</a> <a id="5809" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">,</a> <a id="5811" class="Number">2</a> <a id="5813" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">,</a> <a id="5815" class="Number">3</a> <a id="5817" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">,</a> <a id="5819" class="Number">4</a> <a id="5821" href="plfa.part1.Lists.html#3970" class="InductiveConstructor Operator">]</a>
<a id="5823" class="Symbol">_</a> <a id="5825" class="Symbol">=</a>
  <a id="5829" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="5839" class="Number">0</a> <a id="5841" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="5843" class="Number">1</a> <a id="5845" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5847" class="Number">2</a> <a id="5849" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5851" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="5854" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="5857" class="Number">3</a> <a id="5859" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5861" class="Number">4</a> <a id="5863" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5865" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="5870" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="5878" class="Number">0</a> <a id="5880" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="5882" class="Symbol">(</a><a id="5883" class="Number">1</a> <a id="5885" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5887" class="Number">2</a> <a id="5889" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5891" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="5894" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="5897" class="Number">3</a> <a id="5899" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5901" class="Number">4</a> <a id="5903" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5905" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="5907" class="Symbol">)</a>
  <a id="5911" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="5919" class="Number">0</a> <a id="5921" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="5923" class="Number">1</a> <a id="5925" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5927" class="Symbol">(</a><a id="5928" class="Number">2</a> <a id="5930" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5932" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="5935" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="5938" class="Number">3</a> <a id="5940" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5942" class="Number">4</a> <a id="5944" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5946" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="5948" class="Symbol">)</a>
  <a id="5952" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="5960" class="Number">0</a> <a id="5962" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="5964" class="Number">1</a> <a id="5966" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5968" class="Number">2</a> <a id="5970" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5972" class="Symbol">(</a><a id="5973" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="5976" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="5979" class="Number">3</a> <a id="5981" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5983" class="Number">4</a> <a id="5985" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="5987" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="5989" class="Symbol">)</a>
  <a id="5993" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6001" class="Number">0</a> <a id="6003" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="6005" class="Number">1</a> <a id="6007" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6009" class="Number">2</a> <a id="6011" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6013" class="Number">3</a> <a id="6015" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6017" class="Number">4</a> <a id="6019" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6021" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="6026" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Appending two lists requires time linear in the
number of elements in the first list.
{:/}

附加两个列表需要对于第一个列表元素个数线性的时间。


{::comment}
## Reasoning about append
{:/}

## 论证附加

{::comment}
We can reason about lists in much the same way that we reason
about numbers.  Here is the proof that append is associative:
{:/}

我们可以与用论证数几乎相同的方法来论证列表。下面是附加满足结合律的证明：
{% raw %}<pre class="Agda"><a id="++-assoc"></a><a id="6401" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6401" class="Function">++-assoc</a> <a id="6410" class="Symbol">:</a> <a id="6412" class="Symbol">∀</a> <a id="6414" class="Symbol">{</a><a id="6415" href="plfa.part1.Lists.html#6415" class="Bound">A</a> <a id="6417" class="Symbol">:</a> <a id="6419" class="PrimitiveType">Set</a><a id="6422" class="Symbol">}</a> <a id="6424" class="Symbol">(</a><a id="6425" href="plfa.part1.Lists.html#6425" class="Bound">xs</a> <a id="6428" href="plfa.part1.Lists.html#6428" class="Bound">ys</a> <a id="6431" href="plfa.part1.Lists.html#6431" class="Bound">zs</a> <a id="6434" class="Symbol">:</a> <a id="6436" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="6441" href="plfa.part1.Lists.html#6415" class="Bound">A</a><a id="6442" class="Symbol">)</a>
  <a id="6446" class="Symbol">→</a> <a id="6448" class="Symbol">(</a><a id="6449" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6425" class="Bound">xs</a> <a id="6452" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6455" href="plfa.part1.Lists.html#6428" class="Bound">ys</a><a id="6457" class="Symbol">)</a> <a id="6459" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6462" href="plfa.part1.Lists.html#6431" class="Bound">zs</a> <a id="6465" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="6467" href="plfa.part1.Lists.html#6425" class="Bound">xs</a> <a id="6470" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6473" class="Symbol">(</a><a id="6474" href="plfa.part1.Lists.html#6428" class="Bound">ys</a> <a id="6477" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6480" href="plfa.part1.Lists.html#6431" class="Bound">zs</a><a id="6482" class="Symbol">)</a>
<a id="6484" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6401" class="Function">++-assoc</a> <a id="6493" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="6496" href="plfa.part1.Lists.html#6496" class="Bound">ys</a> <a id="6499" href="plfa.part1.Lists.html#6499" class="Bound">zs</a> <a id="6502" class="Symbol">=</a>
  <a id="6506" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="6516" class="Symbol">(</a><a id="6517" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="6520" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6523" href="plfa.part1.Lists.html#6496" class="Bound">ys</a><a id="6525" class="Symbol">)</a> <a id="6527" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6530" href="plfa.part1.Lists.html#6499" class="Bound">zs</a>
  <a id="6535" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6543" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6496" class="Bound">ys</a> <a id="6546" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6549" href="plfa.part1.Lists.html#6499" class="Bound">zs</a>
  <a id="6554" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6562" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="6565" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6568" class="Symbol">(</a><a id="6569" href="plfa.part1.Lists.html#6496" class="Bound">ys</a> <a id="6572" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6575" href="plfa.part1.Lists.html#6499" class="Bound">zs</a><a id="6577" class="Symbol">)</a>
  <a id="6581" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="6583" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6401" class="Function">++-assoc</a> <a id="6592" class="Symbol">(</a><a id="6593" href="plfa.part1.Lists.html#6593" class="Bound">x</a> <a id="6595" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6597" href="plfa.part1.Lists.html#6597" class="Bound">xs</a><a id="6599" class="Symbol">)</a> <a id="6601" href="plfa.part1.Lists.html#6601" class="Bound">ys</a> <a id="6604" href="plfa.part1.Lists.html#6604" class="Bound">zs</a> <a id="6607" class="Symbol">=</a>
  <a id="6611" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="6621" class="Symbol">(</a><a id="6622" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6624" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6626" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6629" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6632" href="plfa.part1.Lists.html#6601" class="Bound">ys</a><a id="6634" class="Symbol">)</a> <a id="6636" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6639" href="plfa.part1.Lists.html#6604" class="Bound">zs</a>
  <a id="6644" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6652" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6654" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6656" class="Symbol">(</a><a id="6657" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6660" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6663" href="plfa.part1.Lists.html#6601" class="Bound">ys</a><a id="6665" class="Symbol">)</a> <a id="6667" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6670" href="plfa.part1.Lists.html#6604" class="Bound">zs</a>
  <a id="6675" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6683" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6685" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6687" class="Symbol">((</a><a id="6689" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6692" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6695" href="plfa.part1.Lists.html#6601" class="Bound">ys</a><a id="6697" class="Symbol">)</a> <a id="6699" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6702" href="plfa.part1.Lists.html#6604" class="Bound">zs</a><a id="6704" class="Symbol">)</a>
  <a id="6708" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="6711" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="6716" class="Symbol">(</a><a id="6717" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6719" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷_</a><a id="6721" class="Symbol">)</a> <a id="6723" class="Symbol">(</a><a id="6724" href="plfa.part1.Lists.html#6401" class="Function">++-assoc</a> <a id="6733" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6736" href="plfa.part1.Lists.html#6601" class="Bound">ys</a> <a id="6739" href="plfa.part1.Lists.html#6604" class="Bound">zs</a><a id="6741" class="Symbol">)</a> <a id="6743" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="6749" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6751" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6753" class="Symbol">(</a><a id="6754" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6757" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6760" class="Symbol">(</a><a id="6761" href="plfa.part1.Lists.html#6601" class="Bound">ys</a> <a id="6764" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6767" href="plfa.part1.Lists.html#6604" class="Bound">zs</a><a id="6769" class="Symbol">))</a>
  <a id="6774" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="6782" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6593" class="Bound">x</a> <a id="6784" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="6786" href="plfa.part1.Lists.html#6597" class="Bound">xs</a> <a id="6789" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6792" class="Symbol">(</a><a id="6793" href="plfa.part1.Lists.html#6601" class="Bound">ys</a> <a id="6796" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="6799" href="plfa.part1.Lists.html#6604" class="Bound">zs</a><a id="6801" class="Symbol">)</a>
  <a id="6805" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
The proof is by induction on the first argument. The base case instantiates
to `[]`, and follows by straightforward computation.
The inductive case instantiates to `x ∷ xs`,
and follows by straightforward computation combined with the
inductive hypothesis.  As usual, the inductive hypothesis is indicated by a recursive
invocation of the proof, in this case `++-assoc xs ys zs`.
{:/}

证明对于第一个参数进行归纳。起始步骤将列表实例化为 `[]`，由直接的运算可证。归纳步骤将列表实例化为 `x ∷ xs`，由直接的运算配合归纳假设可证。与往常一样，归纳假设由递归使用证明函数来表明，此处为 `++-assoc xs ys zs`。

{::comment}
Recall that Agda supports [sections]({{ site.baseurl }}/Induction/#sections).
Applying `cong (x ∷_)` promotes the inductive hypothesis:
{:/}

回忆到 Agda 支持[片段]({{ site.baseurl }}/Induction/#sections)。使用 `cong (x ∷_)`
可以将归纳假设：

    (xs ++ ys) ++ zs ≡ xs ++ (ys ++ zs)

{::comment}
to the equality:
{:/}

提升至等式：

    x ∷ ((xs ++ ys) ++ zs) ≡ x ∷ (xs ++ (ys ++ zs))

{::comment}
which is needed in the proof.
{:/}

即证明中所需。

{::comment}
It is also easy to show that `[]` is a left and right identity for `_++_`.
That it is a left identity is immediate from the definition:
{:/}

我们也可以简单地证明 `[]` 是 `_++_` 的左幺元和右幺元。左幺元的证明从定义中即可得：

{% raw %}<pre class="Agda"><a id="++-identityˡ"></a><a id="7977" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#7977" class="Function">++-identityˡ</a> <a id="7990" class="Symbol">:</a> <a id="7992" class="Symbol">∀</a> <a id="7994" class="Symbol">{</a><a id="7995" href="plfa.part1.Lists.html#7995" class="Bound">A</a> <a id="7997" class="Symbol">:</a> <a id="7999" class="PrimitiveType">Set</a><a id="8002" class="Symbol">}</a> <a id="8004" class="Symbol">(</a><a id="8005" href="plfa.part1.Lists.html#8005" class="Bound">xs</a> <a id="8008" class="Symbol">:</a> <a id="8010" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="8015" href="plfa.part1.Lists.html#7995" class="Bound">A</a><a id="8016" class="Symbol">)</a> <a id="8018" class="Symbol">→</a> <a id="8020" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="8023" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8026" href="plfa.part1.Lists.html#8005" class="Bound">xs</a> <a id="8029" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="8031" href="plfa.part1.Lists.html#8005" class="Bound">xs</a>
<a id="8034" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#7977" class="Function">++-identityˡ</a> <a id="8047" href="plfa.part1.Lists.html#8047" class="Bound">xs</a> <a id="8050" class="Symbol">=</a>
  <a id="8054" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="8064" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="8067" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8070" href="plfa.part1.Lists.html#8047" class="Bound">xs</a>
  <a id="8075" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="8083" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8047" class="Bound">xs</a>
  <a id="8088" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
That it is a right identity follows by simple induction:
{:/}

右幺元的证明可由简单的归纳得到：
{% raw %}<pre class="Agda"><a id="++-identityʳ"></a><a id="8191" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8191" class="Function">++-identityʳ</a> <a id="8204" class="Symbol">:</a> <a id="8206" class="Symbol">∀</a> <a id="8208" class="Symbol">{</a><a id="8209" href="plfa.part1.Lists.html#8209" class="Bound">A</a> <a id="8211" class="Symbol">:</a> <a id="8213" class="PrimitiveType">Set</a><a id="8216" class="Symbol">}</a> <a id="8218" class="Symbol">(</a><a id="8219" href="plfa.part1.Lists.html#8219" class="Bound">xs</a> <a id="8222" class="Symbol">:</a> <a id="8224" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="8229" href="plfa.part1.Lists.html#8209" class="Bound">A</a><a id="8230" class="Symbol">)</a> <a id="8232" class="Symbol">→</a> <a id="8234" href="plfa.part1.Lists.html#8219" class="Bound">xs</a> <a id="8237" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8240" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="8243" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="8245" href="plfa.part1.Lists.html#8219" class="Bound">xs</a>
<a id="8248" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8191" class="Function">++-identityʳ</a> <a id="8261" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="8264" class="Symbol">=</a>
  <a id="8268" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="8278" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="8281" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8284" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="8289" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="8297" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a>
  <a id="8302" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="8304" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8191" class="Function">++-identityʳ</a> <a id="8317" class="Symbol">(</a><a id="8318" href="plfa.part1.Lists.html#8318" class="Bound">x</a> <a id="8320" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="8322" href="plfa.part1.Lists.html#8322" class="Bound">xs</a><a id="8324" class="Symbol">)</a> <a id="8326" class="Symbol">=</a>
  <a id="8330" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="8340" class="Symbol">(</a><a id="8341" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8318" class="Bound">x</a> <a id="8343" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="8345" href="plfa.part1.Lists.html#8322" class="Bound">xs</a><a id="8347" class="Symbol">)</a> <a id="8349" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8352" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="8357" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="8365" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8318" class="Bound">x</a> <a id="8367" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="8369" class="Symbol">(</a><a id="8370" href="plfa.part1.Lists.html#8322" class="Bound">xs</a> <a id="8373" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="8376" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="8378" class="Symbol">)</a>
  <a id="8382" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="8385" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="8390" class="Symbol">(</a><a id="8391" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8318" class="Bound">x</a> <a id="8393" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷_</a><a id="8395" class="Symbol">)</a> <a id="8397" class="Symbol">(</a><a id="8398" href="plfa.part1.Lists.html#8191" class="Function">++-identityʳ</a> <a id="8411" href="plfa.part1.Lists.html#8322" class="Bound">xs</a><a id="8413" class="Symbol">)</a> <a id="8415" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="8421" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8318" class="Bound">x</a> <a id="8423" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="8425" href="plfa.part1.Lists.html#8322" class="Bound">xs</a>
  <a id="8430" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
As we will see later,
these three properties establish that `_++_` and `[]` form
a _monoid_ over lists.
{:/}

我们之后会了解到，这三条性质表明了 `_++_` 和 `[]` 在列表上构成了一个*幺半群*（Monoid）。

{::comment}
## Length
{:/}

## 长度

{::comment}
Our next function finds the length of a list:
{:/}

在下一个函数里，我们来寻找列表的长度：

{% raw %}<pre class="Agda"><a id="length"></a><a id="8740" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="8747" class="Symbol">:</a> <a id="8749" class="Symbol">∀</a> <a id="8751" class="Symbol">{</a><a id="8752" href="plfa.part1.Lists.html#8752" class="Bound">A</a> <a id="8754" class="Symbol">:</a> <a id="8756" class="PrimitiveType">Set</a><a id="8759" class="Symbol">}</a> <a id="8761" class="Symbol">→</a> <a id="8763" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="8768" href="plfa.part1.Lists.html#8752" class="Bound">A</a> <a id="8770" class="Symbol">→</a> <a id="8772" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="8774" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="8781" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>        <a id="8791" class="Symbol">=</a>  <a id="8794" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>
<a id="8799" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="8806" class="Symbol">(</a><a id="8807" href="plfa.part1.Lists.html#8807" class="Bound">x</a> <a id="8809" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="8811" href="plfa.part1.Lists.html#8811" class="Bound">xs</a><a id="8813" class="Symbol">)</a>  <a id="8816" class="Symbol">=</a>  <a id="8819" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="8823" class="Symbol">(</a><a id="8824" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="8831" href="plfa.part1.Lists.html#8811" class="Bound">xs</a><a id="8833" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
Again, it takes an implicit parameter `A`.
The length of the empty list is zero.
The length of a non-empty list
is one greater than the length of the tail of the list.
{:/}

同样，它取一个隐式参数 `A`。空列表的长度为零。非空列表的长度比其尾列表长度多一。

{::comment}
Here is an example showing how to compute the length of a list:
{:/}

我们用下面的例子来展示如何计算列表的长度：
{% raw %}<pre class="Agda"><a id="9179" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#9179" class="Function">_</a> <a id="9181" class="Symbol">:</a> <a id="9183" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="9190" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="9192" class="Number">0</a> <a id="9194" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="9196" class="Number">1</a> <a id="9198" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="9200" class="Number">2</a> <a id="9202" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="9204" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="9206" class="Number">3</a>
<a id="9208" class="Symbol">_</a> <a id="9210" class="Symbol">=</a>
  <a id="9214" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="9224" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="9231" class="Symbol">(</a><a id="9232" class="Number">0</a> <a id="9234" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9236" class="Number">1</a> <a id="9238" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9240" class="Number">2</a> <a id="9242" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9244" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="9246" class="Symbol">)</a>
  <a id="9250" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="9258" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9262" class="Symbol">(</a><a id="9263" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="9270" class="Symbol">(</a><a id="9271" class="Number">1</a> <a id="9273" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9275" class="Number">2</a> <a id="9277" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9279" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="9281" class="Symbol">))</a>
  <a id="9286" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="9294" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9298" class="Symbol">(</a><a id="9299" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9303" class="Symbol">(</a><a id="9304" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="9311" class="Symbol">(</a><a id="9312" class="Number">2</a> <a id="9314" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="9316" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="9318" class="Symbol">)))</a>
  <a id="9324" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="9332" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9336" class="Symbol">(</a><a id="9337" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9341" class="Symbol">(</a><a id="9342" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9346" class="Symbol">(</a><a id="9347" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="9354" class="Symbol">{</a><a id="9355" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="9356" class="Symbol">}</a> <a id="9358" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="9360" class="Symbol">)))</a>
  <a id="9366" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="9374" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9378" class="Symbol">(</a><a id="9379" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9383" class="Symbol">(</a><a id="9384" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="9388" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a><a id="9392" class="Symbol">))</a>
  <a id="9397" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Computing the length of a list requires time
linear in the number of elements in the list.
{:/}

计算列表的长度需要关于列表元素个数线性的时间。

{::comment}
In the second-to-last line, we cannot write simply `length []` but
must instead write `length {ℕ} []`.  Since `[]` has no elements, Agda
has insufficient information to infer the implicit parameter.
{:/}

在倒数第二行中，我们不可以直接写 `length []`，而需要写 `length {ℕ} []`。因为 `[]` 没有元素，Agda 没有足够的信息来推导其隐式参数。

{::comment}
## Reasoning about length
{:/}

## 论证长度

{::comment}
The length of one list appended to another is the
sum of the lengths of the lists:
{:/}

两个附加在一起的列表的长度是两列表长度之和：

{% raw %}<pre class="Agda"><a id="length-++"></a><a id="10024" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#10024" class="Function">length-++</a> <a id="10034" class="Symbol">:</a> <a id="10036" class="Symbol">∀</a> <a id="10038" class="Symbol">{</a><a id="10039" href="plfa.part1.Lists.html#10039" class="Bound">A</a> <a id="10041" class="Symbol">:</a> <a id="10043" class="PrimitiveType">Set</a><a id="10046" class="Symbol">}</a> <a id="10048" class="Symbol">(</a><a id="10049" href="plfa.part1.Lists.html#10049" class="Bound">xs</a> <a id="10052" href="plfa.part1.Lists.html#10052" class="Bound">ys</a> <a id="10055" class="Symbol">:</a> <a id="10057" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="10062" href="plfa.part1.Lists.html#10039" class="Bound">A</a><a id="10063" class="Symbol">)</a>
  <a id="10067" class="Symbol">→</a> <a id="10069" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10076" class="Symbol">(</a><a id="10077" href="plfa.part1.Lists.html#10049" class="Bound">xs</a> <a id="10080" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="10083" href="plfa.part1.Lists.html#10052" class="Bound">ys</a><a id="10085" class="Symbol">)</a> <a id="10087" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="10089" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="10096" href="plfa.part1.Lists.html#10049" class="Bound">xs</a> <a id="10099" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="10101" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="10108" href="plfa.part1.Lists.html#10052" class="Bound">ys</a>
<a id="10111" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#10024" class="Function">length-++</a> <a id="10121" class="Symbol">{</a><a id="10122" href="plfa.part1.Lists.html#10122" class="Bound">A</a><a id="10123" class="Symbol">}</a> <a id="10125" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="10128" href="plfa.part1.Lists.html#10128" class="Bound">ys</a> <a id="10131" class="Symbol">=</a>
  <a id="10135" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="10145" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10152" class="Symbol">(</a><a id="10153" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="10156" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="10159" href="plfa.part1.Lists.html#10128" class="Bound">ys</a><a id="10161" class="Symbol">)</a>
  <a id="10165" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="10173" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10180" href="plfa.part1.Lists.html#10128" class="Bound">ys</a>
  <a id="10185" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="10193" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10200" class="Symbol">{</a><a id="10201" href="plfa.part1.Lists.html#10122" class="Bound">A</a><a id="10202" class="Symbol">}</a> <a id="10204" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="10207" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="10209" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="10216" href="plfa.part1.Lists.html#10128" class="Bound">ys</a>
  <a id="10221" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="10223" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#10024" class="Function">length-++</a> <a id="10233" class="Symbol">(</a><a id="10234" href="plfa.part1.Lists.html#10234" class="Bound">x</a> <a id="10236" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="10238" href="plfa.part1.Lists.html#10238" class="Bound">xs</a><a id="10240" class="Symbol">)</a> <a id="10242" href="plfa.part1.Lists.html#10242" class="Bound">ys</a> <a id="10245" class="Symbol">=</a>
  <a id="10249" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="10259" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10266" class="Symbol">((</a><a id="10268" href="plfa.part1.Lists.html#10234" class="Bound">x</a> <a id="10270" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="10272" href="plfa.part1.Lists.html#10238" class="Bound">xs</a><a id="10274" class="Symbol">)</a> <a id="10276" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="10279" href="plfa.part1.Lists.html#10242" class="Bound">ys</a><a id="10281" class="Symbol">)</a>
  <a id="10285" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="10293" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="10297" class="Symbol">(</a><a id="10298" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10305" class="Symbol">(</a><a id="10306" href="plfa.part1.Lists.html#10238" class="Bound">xs</a> <a id="10309" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="10312" href="plfa.part1.Lists.html#10242" class="Bound">ys</a><a id="10314" class="Symbol">))</a>
  <a id="10319" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="10322" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="10327" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="10331" class="Symbol">(</a><a id="10332" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#10024" class="Function">length-++</a> <a id="10342" href="plfa.part1.Lists.html#10238" class="Bound">xs</a> <a id="10345" href="plfa.part1.Lists.html#10242" class="Bound">ys</a><a id="10347" class="Symbol">)</a> <a id="10349" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="10355" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="10359" class="Symbol">(</a><a id="10360" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10367" href="plfa.part1.Lists.html#10238" class="Bound">xs</a> <a id="10370" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="10372" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="10379" href="plfa.part1.Lists.html#10242" class="Bound">ys</a><a id="10381" class="Symbol">)</a>
  <a id="10385" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="10393" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8740" class="Function">length</a> <a id="10400" class="Symbol">(</a><a id="10401" href="plfa.part1.Lists.html#10234" class="Bound">x</a> <a id="10403" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="10405" href="plfa.part1.Lists.html#10238" class="Bound">xs</a><a id="10407" class="Symbol">)</a> <a id="10409" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="10411" href="plfa.part1.Lists.html#8740" class="Function">length</a> <a id="10418" href="plfa.part1.Lists.html#10242" class="Bound">ys</a>
  <a id="10423" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
The proof is by induction on the first argument. The base case
instantiates to `[]`, and follows by straightforward computation.  As
before, Agda cannot infer the implicit type parameter to `length`, and
it must be given explicitly.  The inductive case instantiates to
`x ∷ xs`, and follows by straightforward computation combined with the
inductive hypothesis.  As usual, the inductive hypothesis is indicated
by a recursive invocation of the proof, in this case `length-++ xs ys`,
and it is promoted by the congruence `cong suc`.
{:/}

证明对于第一个参数进行归纳。起始步骤将列表实例化为 `[]`，由直接的运算可证。如同之前一样，Agda 无法推导 `length` 的隐式参数，所以我们必须显式地给出这个参数。归纳步骤将列表实例化为 `x ∷ xs`，由直接的运算配合归纳假设可证。与往常一样，归纳假设由递归使用证明函数来表明，此处为 `length-++ xs ys`，由 `cong suc` 来提升。

{::comment}
## Reverse
{:/}

## 反转

{::comment}
Using append, it is easy to formulate a function to reverse a list:
{:/}

我们可以使用附加，来简单地构造一个函数来反转一个列表：
{% raw %}<pre class="Agda"><a id="reverse"></a><a id="11326" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11334" class="Symbol">:</a> <a id="11336" class="Symbol">∀</a> <a id="11338" class="Symbol">{</a><a id="11339" href="plfa.part1.Lists.html#11339" class="Bound">A</a> <a id="11341" class="Symbol">:</a> <a id="11343" class="PrimitiveType">Set</a><a id="11346" class="Symbol">}</a> <a id="11348" class="Symbol">→</a> <a id="11350" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="11355" href="plfa.part1.Lists.html#11339" class="Bound">A</a> <a id="11357" class="Symbol">→</a> <a id="11359" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="11364" href="plfa.part1.Lists.html#11339" class="Bound">A</a>
<a id="11366" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11374" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>        <a id="11384" class="Symbol">=</a>  <a id="11387" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="11390" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11398" class="Symbol">(</a><a id="11399" href="plfa.part1.Lists.html#11399" class="Bound">x</a> <a id="11401" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11403" href="plfa.part1.Lists.html#11403" class="Bound">xs</a><a id="11405" class="Symbol">)</a>  <a id="11408" class="Symbol">=</a>  <a id="11411" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="11419" href="plfa.part1.Lists.html#11403" class="Bound">xs</a> <a id="11422" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11425" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11427" href="plfa.part1.Lists.html#11399" class="Bound">x</a> <a id="11429" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a>
</pre>{% endraw %}
{::comment}
The reverse of the empty list is the empty list.
The reverse of a non-empty list
is the reverse of its tail appended to a unit list
containing its head.
{:/}

空列表的反转是空列表。非空列表的反转是其头元素构成的单元列表附加至其尾列表反转之后的结果。

{::comment}
Here is an example showing how to reverse a list:
{:/}

下面的例子展示了如何反转一个列表。
{% raw %}<pre class="Agda"><a id="11745" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11745" class="Function">_</a> <a id="11747" class="Symbol">:</a> <a id="11749" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="11757" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="11759" class="Number">0</a> <a id="11761" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="11763" class="Number">1</a> <a id="11765" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="11767" class="Number">2</a> <a id="11769" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="11771" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="11773" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="11775" class="Number">2</a> <a id="11777" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="11779" class="Number">1</a> <a id="11781" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="11783" class="Number">0</a> <a id="11785" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="11787" class="Symbol">_</a> <a id="11789" class="Symbol">=</a>
  <a id="11793" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="11803" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11811" class="Symbol">(</a><a id="11812" class="Number">0</a> <a id="11814" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11816" class="Number">1</a> <a id="11818" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11820" class="Number">2</a> <a id="11822" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11824" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="11826" class="Symbol">)</a>
  <a id="11830" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11838" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11846" class="Symbol">(</a><a id="11847" class="Number">1</a> <a id="11849" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11851" class="Number">2</a> <a id="11853" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11855" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="11857" class="Symbol">)</a> <a id="11859" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11862" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11864" class="Number">0</a> <a id="11866" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a>
  <a id="11870" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11878" class="Symbol">(</a><a id="11879" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11887" class="Symbol">(</a><a id="11888" class="Number">2</a> <a id="11890" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="11892" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="11894" class="Symbol">)</a> <a id="11896" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11899" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11901" class="Number">1</a> <a id="11903" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="11904" class="Symbol">)</a> <a id="11906" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11909" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11911" class="Number">0</a> <a id="11913" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a>
  <a id="11917" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11925" class="Symbol">((</a><a id="11927" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="11935" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="11938" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11941" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11943" class="Number">2</a> <a id="11945" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="11946" class="Symbol">)</a> <a id="11948" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11951" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11953" class="Number">1</a> <a id="11955" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="11956" class="Symbol">)</a> <a id="11958" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11961" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11963" class="Number">0</a> <a id="11965" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a>
  <a id="11969" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11977" class="Symbol">((</a><a id="11979" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="11982" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11985" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11987" class="Number">2</a> <a id="11989" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="11990" class="Symbol">)</a> <a id="11992" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="11995" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="11997" class="Number">1</a> <a id="11999" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="12000" class="Symbol">)</a> <a id="12002" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12005" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="12007" class="Number">0</a> <a id="12009" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a>
  <a id="12013" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12021" class="Symbol">((</a><a id="12023" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1325" class="InductiveConstructor">[]</a> <a id="12026" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12029" class="Number">2</a> <a id="12031" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12033" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12035" class="Symbol">)</a> <a id="12037" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12040" class="Number">1</a> <a id="12042" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12044" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12046" class="Symbol">)</a> <a id="12048" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12051" class="Number">0</a> <a id="12053" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12055" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="12060" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12068" class="Symbol">(</a><a id="12069" class="Number">2</a> <a id="12071" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12073" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="12076" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12079" class="Number">1</a> <a id="12081" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12083" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12085" class="Symbol">)</a> <a id="12087" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12090" class="Number">0</a> <a id="12092" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12094" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="12099" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12107" class="Number">2</a> <a id="12109" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12111" class="Symbol">(</a><a id="12112" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="12115" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12118" class="Number">1</a> <a id="12120" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12122" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12124" class="Symbol">)</a> <a id="12126" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12129" class="Number">0</a> <a id="12131" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12133" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="12138" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12146" class="Symbol">(</a><a id="12147" class="Number">2</a> <a id="12149" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12151" class="Number">1</a> <a id="12153" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12155" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12157" class="Symbol">)</a> <a id="12159" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12162" class="Number">0</a> <a id="12164" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12166" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="12171" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12179" class="Number">2</a> <a id="12181" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12183" class="Symbol">(</a><a id="12184" class="Number">1</a> <a id="12186" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12188" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="12191" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12194" class="Number">0</a> <a id="12196" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12198" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12200" class="Symbol">)</a>
  <a id="12204" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12212" class="Number">2</a> <a id="12214" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12216" class="Number">1</a> <a id="12218" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12220" class="Symbol">(</a><a id="12221" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="12224" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="12227" class="Number">0</a> <a id="12229" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12231" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="12233" class="Symbol">)</a>
  <a id="12237" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12245" class="Number">2</a> <a id="12247" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="12249" class="Number">1</a> <a id="12251" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12253" class="Number">0</a> <a id="12255" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="12257" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="12262" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="12270" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3884" class="InductiveConstructor Operator">[</a> <a id="12272" class="Number">2</a> <a id="12274" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="12276" class="Number">1</a> <a id="12278" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="12280" class="Number">0</a> <a id="12282" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
  <a id="12286" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Reversing a list in this way takes time _quadratic_ in the length of
the list. This is because reverse ends up appending lists of lengths
`1`, `2`, up to `n - 1`, where `n` is the length of the list being
reversed, append takes time linear in the length of the first
list, and the sum of the numbers up to `n - 1` is `n * (n - 1) / 2`.
(We will validate that last fact in an exercise later in this chapter.)
{:/}

这样子反转一个列表需要列表长度**二次**的时间。这是因为反转一个长度为 `n` 的列表需要将长度为 `1`、`2` 直到 `n - 1` 的列表附加起来，而附加两个列表需要第一个列表长度线性的时间，因此加起来就需要 `n * (n - 1) / 2` 的时间。（我们将在本章节后部分验证这一结果）

{::comment}
#### Exercise `reverse-++-distrib` (recommended)
{:/}

#### 练习 `reverse-++-distrib`（推荐）

{::comment}
Show that the reverse of one list appended to another is the
reverse of the second appended to the reverse of the first:
{:/}

证明一个列表附加到另外一个列表的反转即是反转后的第二个列表附加至反转后的第一个列表：

    reverse (xs ++ ys) ≡ reverse ys ++ reverse xs


{::comment}
#### Exercise `reverse-involutive` (recommended)
{:/}

#### 练习 `reverse-involutive`（推荐）

{::comment}
A function is an _involution_ if when applied twice it acts
as the identity function.  Show that reverse is an involution:
{:/}

当一个函数应用两次后与恒等函数作用相同，那么这个函数是一个**对合**（Involution）。证明反转是一个对合：

    reverse (reverse xs) ≡ xs


{::comment}
## Faster reverse
{:/}

## 更快地反转

{::comment}
The definition above, while easy to reason about, is less efficient than
one might expect since it takes time quadratic in the length of the list.
The idea is that we generalise reverse to take an additional argument:
{:/}

上面的定义虽然论证起来方便，但是它比期望中的实现更低效，因为它的运行时间是关于列表长度的二次函数。我们可以将反转进行推广，使用一个额外的参数：

{% raw %}<pre class="Agda"><a id="shunt"></a><a id="13903" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="13909" class="Symbol">:</a> <a id="13911" class="Symbol">∀</a> <a id="13913" class="Symbol">{</a><a id="13914" href="plfa.part1.Lists.html#13914" class="Bound">A</a> <a id="13916" class="Symbol">:</a> <a id="13918" class="PrimitiveType">Set</a><a id="13921" class="Symbol">}</a> <a id="13923" class="Symbol">→</a> <a id="13925" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="13930" href="plfa.part1.Lists.html#13914" class="Bound">A</a> <a id="13932" class="Symbol">→</a> <a id="13934" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="13939" href="plfa.part1.Lists.html#13914" class="Bound">A</a> <a id="13941" class="Symbol">→</a> <a id="13943" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="13948" href="plfa.part1.Lists.html#13914" class="Bound">A</a>
<a id="13950" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="13956" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>       <a id="13965" href="plfa.part1.Lists.html#13965" class="Bound">ys</a>  <a id="13969" class="Symbol">=</a>  <a id="13972" href="plfa.part1.Lists.html#13965" class="Bound">ys</a>
<a id="13975" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="13981" class="Symbol">(</a><a id="13982" href="plfa.part1.Lists.html#13982" class="Bound">x</a> <a id="13984" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="13986" href="plfa.part1.Lists.html#13986" class="Bound">xs</a><a id="13988" class="Symbol">)</a> <a id="13990" href="plfa.part1.Lists.html#13990" class="Bound">ys</a>  <a id="13994" class="Symbol">=</a>  <a id="13997" href="plfa.part1.Lists.html#13903" class="Function">shunt</a> <a id="14003" href="plfa.part1.Lists.html#13986" class="Bound">xs</a> <a id="14006" class="Symbol">(</a><a id="14007" href="plfa.part1.Lists.html#13982" class="Bound">x</a> <a id="14009" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14011" href="plfa.part1.Lists.html#13990" class="Bound">ys</a><a id="14013" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
The definition is by recursion on the first argument. The second argument
actually becomes _larger_, but this is not a problem because the argument
on which we recurse becomes _smaller_.
{:/}

这个定义对于第一个参数进行递归。第二个参数会变_大_，但这样做没有问题，因为我们递归的参数在变_小_。

{::comment}
Shunt is related to reverse as follows:
{:/}

转移（Shunt）与反转的关系如下：
{% raw %}<pre class="Agda"><a id="shunt-reverse"></a><a id="14360" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14360" class="Function">shunt-reverse</a> <a id="14374" class="Symbol">:</a> <a id="14376" class="Symbol">∀</a> <a id="14378" class="Symbol">{</a><a id="14379" href="plfa.part1.Lists.html#14379" class="Bound">A</a> <a id="14381" class="Symbol">:</a> <a id="14383" class="PrimitiveType">Set</a><a id="14386" class="Symbol">}</a> <a id="14388" class="Symbol">(</a><a id="14389" href="plfa.part1.Lists.html#14389" class="Bound">xs</a> <a id="14392" href="plfa.part1.Lists.html#14392" class="Bound">ys</a> <a id="14395" class="Symbol">:</a> <a id="14397" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="14402" href="plfa.part1.Lists.html#14379" class="Bound">A</a><a id="14403" class="Symbol">)</a>
  <a id="14407" class="Symbol">→</a> <a id="14409" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="14415" href="plfa.part1.Lists.html#14389" class="Bound">xs</a> <a id="14418" href="plfa.part1.Lists.html#14392" class="Bound">ys</a> <a id="14421" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="14423" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="14431" href="plfa.part1.Lists.html#14389" class="Bound">xs</a> <a id="14434" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14437" href="plfa.part1.Lists.html#14392" class="Bound">ys</a>
<a id="14440" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14360" class="Function">shunt-reverse</a> <a id="14454" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="14457" href="plfa.part1.Lists.html#14457" class="Bound">ys</a> <a id="14460" class="Symbol">=</a>
  <a id="14464" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="14474" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="14480" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="14483" href="plfa.part1.Lists.html#14457" class="Bound">ys</a>
  <a id="14488" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14496" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14457" class="Bound">ys</a>
  <a id="14501" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14509" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="14517" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="14520" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14523" href="plfa.part1.Lists.html#14457" class="Bound">ys</a>
  <a id="14528" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="14530" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14360" class="Function">shunt-reverse</a> <a id="14544" class="Symbol">(</a><a id="14545" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14547" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14549" href="plfa.part1.Lists.html#14549" class="Bound">xs</a><a id="14551" class="Symbol">)</a> <a id="14553" href="plfa.part1.Lists.html#14553" class="Bound">ys</a> <a id="14556" class="Symbol">=</a>
  <a id="14560" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="14570" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="14576" class="Symbol">(</a><a id="14577" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14579" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14581" href="plfa.part1.Lists.html#14549" class="Bound">xs</a><a id="14583" class="Symbol">)</a> <a id="14585" href="plfa.part1.Lists.html#14553" class="Bound">ys</a>
  <a id="14590" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14598" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="14604" href="plfa.part1.Lists.html#14549" class="Bound">xs</a> <a id="14607" class="Symbol">(</a><a id="14608" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14610" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14612" href="plfa.part1.Lists.html#14553" class="Bound">ys</a><a id="14614" class="Symbol">)</a>
  <a id="14618" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="14621" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14360" class="Function">shunt-reverse</a> <a id="14635" href="plfa.part1.Lists.html#14549" class="Bound">xs</a> <a id="14638" class="Symbol">(</a><a id="14639" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14641" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14643" href="plfa.part1.Lists.html#14553" class="Bound">ys</a><a id="14645" class="Symbol">)</a> <a id="14647" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="14653" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="14661" href="plfa.part1.Lists.html#14549" class="Bound">xs</a> <a id="14664" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14667" class="Symbol">(</a><a id="14668" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14670" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14672" href="plfa.part1.Lists.html#14553" class="Bound">ys</a><a id="14674" class="Symbol">)</a>
  <a id="14678" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14686" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="14694" href="plfa.part1.Lists.html#14549" class="Bound">xs</a> <a id="14697" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14700" class="Symbol">(</a><a id="14701" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="14703" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14705" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a> <a id="14707" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14710" href="plfa.part1.Lists.html#14553" class="Bound">ys</a><a id="14712" class="Symbol">)</a>
  <a id="14716" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="14719" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a> <a id="14723" class="Symbol">(</a><a id="14724" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#6401" class="Function">++-assoc</a> <a id="14733" class="Symbol">(</a><a id="14734" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="14742" href="plfa.part1.Lists.html#14549" class="Bound">xs</a><a id="14744" class="Symbol">)</a> <a id="14746" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="14748" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14750" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a> <a id="14752" href="plfa.part1.Lists.html#14553" class="Bound">ys</a><a id="14754" class="Symbol">)</a> <a id="14756" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="14762" class="Symbol">(</a><a id="14763" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="14771" href="plfa.part1.Lists.html#14549" class="Bound">xs</a> <a id="14774" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14777" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">[</a> <a id="14779" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14781" href="plfa.part1.Lists.html#3830" class="InductiveConstructor Operator">]</a><a id="14782" class="Symbol">)</a> <a id="14784" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14787" href="plfa.part1.Lists.html#14553" class="Bound">ys</a>
  <a id="14792" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14800" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="14808" class="Symbol">(</a><a id="14809" href="plfa.part1.Lists.html#14545" class="Bound">x</a> <a id="14811" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="14813" href="plfa.part1.Lists.html#14549" class="Bound">xs</a><a id="14815" class="Symbol">)</a> <a id="14817" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="14820" href="plfa.part1.Lists.html#14553" class="Bound">ys</a>
  <a id="14825" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
The proof is by induction on the first argument.
The base case instantiates to `[]`, and follows by straightforward computation.
The inductive case instantiates to `x ∷ xs` and follows by the inductive
hypothesis and associativity of append.  When we invoke the inductive hypothesis,
the second argument actually becomes *larger*, but this is not a problem because
the argument on which we induct becomes *smaller*.
{:/}

证明对于第一个参数进行归纳。起始步骤将列表实例化为 `[]`，由直接的运算可证。归纳步骤将列表实例化为 `x ∷ xs`，由归纳假设和附加的结合律可证。当我们使用归纳假设时，第二个参数实际上变**大**了，但是这样做没有问题，因为我们归纳的参数变**小**了。

{::comment}
Generalising on an auxiliary argument, which becomes larger as the argument on
which we recurse or induct becomes smaller, is a common trick. It belongs in
your quiver of arrows, ready to slay the right problem.
{:/}

使用一个会在归纳或递归的参数变小时，变大的辅助参数来进行推广，是一个常用的技巧。这个技巧在以后的证明中很有用。

{::comment}
Having defined shunt be generalisation, it is now easy to respecialise to
give a more efficient definition of reverse:
{:/}

在定义了推广的转移之后，我们可以将其特化，作为一个更高效的反转的定义：

{% raw %}<pre class="Agda"><a id="reverse′"></a><a id="15866" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#15866" class="Function">reverse′</a> <a id="15875" class="Symbol">:</a> <a id="15877" class="Symbol">∀</a> <a id="15879" class="Symbol">{</a><a id="15880" href="plfa.part1.Lists.html#15880" class="Bound">A</a> <a id="15882" class="Symbol">:</a> <a id="15884" class="PrimitiveType">Set</a><a id="15887" class="Symbol">}</a> <a id="15889" class="Symbol">→</a> <a id="15891" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="15896" href="plfa.part1.Lists.html#15880" class="Bound">A</a> <a id="15898" class="Symbol">→</a> <a id="15900" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="15905" href="plfa.part1.Lists.html#15880" class="Bound">A</a>
<a id="15907" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#15866" class="Function">reverse′</a> <a id="15916" href="plfa.part1.Lists.html#15916" class="Bound">xs</a> <a id="15919" class="Symbol">=</a> <a id="15921" href="plfa.part1.Lists.html#13903" class="Function">shunt</a> <a id="15927" href="plfa.part1.Lists.html#15916" class="Bound">xs</a> <a id="15930" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
</pre>{% endraw %}
{::comment}
Given our previous lemma, it is straightforward to show
the two definitions equivalent:
{:/}

因为我们之前证明的引理，我们可以直接地证明两个定义是等价的：

{% raw %}<pre class="Agda"><a id="reverses"></a><a id="16080" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#16080" class="Function">reverses</a> <a id="16089" class="Symbol">:</a> <a id="16091" class="Symbol">∀</a> <a id="16093" class="Symbol">{</a><a id="16094" href="plfa.part1.Lists.html#16094" class="Bound">A</a> <a id="16096" class="Symbol">:</a> <a id="16098" class="PrimitiveType">Set</a><a id="16101" class="Symbol">}</a> <a id="16103" class="Symbol">(</a><a id="16104" href="plfa.part1.Lists.html#16104" class="Bound">xs</a> <a id="16107" class="Symbol">:</a> <a id="16109" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="16114" href="plfa.part1.Lists.html#16094" class="Bound">A</a><a id="16115" class="Symbol">)</a>
  <a id="16119" class="Symbol">→</a> <a id="16121" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#15866" class="Function">reverse′</a> <a id="16130" href="plfa.part1.Lists.html#16104" class="Bound">xs</a> <a id="16133" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="16135" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="16143" href="plfa.part1.Lists.html#16104" class="Bound">xs</a>
<a id="16146" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#16080" class="Function">reverses</a> <a id="16155" href="plfa.part1.Lists.html#16155" class="Bound">xs</a> <a id="16158" class="Symbol">=</a>
  <a id="16162" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="16172" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#15866" class="Function">reverse′</a> <a id="16181" href="plfa.part1.Lists.html#16155" class="Bound">xs</a>
  <a id="16186" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16194" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="16200" href="plfa.part1.Lists.html#16155" class="Bound">xs</a> <a id="16203" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="16208" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="16211" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#14360" class="Function">shunt-reverse</a> <a id="16225" href="plfa.part1.Lists.html#16155" class="Bound">xs</a> <a id="16228" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="16231" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="16237" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="16245" href="plfa.part1.Lists.html#16155" class="Bound">xs</a> <a id="16248" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="16251" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="16256" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="16259" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#8191" class="Function">++-identityʳ</a> <a id="16272" class="Symbol">(</a><a id="16273" href="plfa.part1.Lists.html#11326" class="Function">reverse</a> <a id="16281" href="plfa.part1.Lists.html#16155" class="Bound">xs</a><a id="16283" class="Symbol">)</a> <a id="16285" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="16291" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#11326" class="Function">reverse</a> <a id="16299" href="plfa.part1.Lists.html#16155" class="Bound">xs</a>
  <a id="16304" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Here is an example showing fast reverse of the list `[ 0 , 1 , 2 ]`:
{:/}

下面的例子展示了如何快速反转列表 `[ 0 , 1 , 2 ]`：

{% raw %}<pre class="Agda"><a id="16437" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#16437" class="Function">_</a> <a id="16439" class="Symbol">:</a> <a id="16441" href="plfa.part1.Lists.html#15866" class="Function">reverse′</a> <a id="16450" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="16452" class="Number">0</a> <a id="16454" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="16456" class="Number">1</a> <a id="16458" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="16460" class="Number">2</a> <a id="16462" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="16464" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="16466" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="16468" class="Number">2</a> <a id="16470" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="16472" class="Number">1</a> <a id="16474" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="16476" class="Number">0</a> <a id="16478" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="16480" class="Symbol">_</a> <a id="16482" class="Symbol">=</a>
  <a id="16486" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="16496" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#15866" class="Function">reverse′</a> <a id="16505" class="Symbol">(</a><a id="16506" class="Number">0</a> <a id="16508" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16510" class="Number">1</a> <a id="16512" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16514" class="Number">2</a> <a id="16516" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16518" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16520" class="Symbol">)</a>
  <a id="16524" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16532" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="16538" class="Symbol">(</a><a id="16539" class="Number">0</a> <a id="16541" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16543" class="Number">1</a> <a id="16545" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16547" class="Number">2</a> <a id="16549" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16551" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16553" class="Symbol">)</a> <a id="16555" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="16560" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16568" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="16574" class="Symbol">(</a><a id="16575" class="Number">1</a> <a id="16577" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16579" class="Number">2</a> <a id="16581" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16583" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16585" class="Symbol">)</a> <a id="16587" class="Symbol">(</a><a id="16588" class="Number">0</a> <a id="16590" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16592" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16594" class="Symbol">)</a>
  <a id="16598" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16606" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="16612" class="Symbol">(</a><a id="16613" class="Number">2</a> <a id="16615" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16617" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16619" class="Symbol">)</a> <a id="16621" class="Symbol">(</a><a id="16622" class="Number">1</a> <a id="16624" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16626" class="Number">0</a> <a id="16628" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16630" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16632" class="Symbol">)</a>
  <a id="16636" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16644" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#13903" class="Function">shunt</a> <a id="16650" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="16653" class="Symbol">(</a><a id="16654" class="Number">2</a> <a id="16656" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16658" class="Number">1</a> <a id="16660" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16662" class="Number">0</a> <a id="16664" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16666" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="16668" class="Symbol">)</a>
  <a id="16672" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16680" class="Number">2</a> <a id="16682" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="16684" class="Number">1</a> <a id="16686" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16688" class="Number">0</a> <a id="16690" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="16692" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="16697" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Now the time to reverse a list is linear in the length of the list.
{:/}

现在反转一个列表需要的时间与列表的长度线性相关。

{::comment}
## Map {#Map}
{:/}

## 映射 {#Map}

{::comment}
Map applies a function to every element of a list to generate a corresponding list.
Map is an example of a _higher-order function_, one which takes a function as an
argument or returns a function as a result:
{:/}

映射将一个函数应用于列表中的所有元素，生成一个对应的列表。映射是一个**高阶函数**（Higher-Order Function）的例子，它取一个函数作为参数，返回一个函数作为结果：

{% raw %}<pre class="Agda"><a id="map"></a><a id="17187" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17187" class="Function">map</a> <a id="17191" class="Symbol">:</a> <a id="17193" class="Symbol">∀</a> <a id="17195" class="Symbol">{</a><a id="17196" href="plfa.part1.Lists.html#17196" class="Bound">A</a> <a id="17198" href="plfa.part1.Lists.html#17198" class="Bound">B</a> <a id="17200" class="Symbol">:</a> <a id="17202" class="PrimitiveType">Set</a><a id="17205" class="Symbol">}</a> <a id="17207" class="Symbol">→</a> <a id="17209" class="Symbol">(</a><a id="17210" href="plfa.part1.Lists.html#17196" class="Bound">A</a> <a id="17212" class="Symbol">→</a> <a id="17214" href="plfa.part1.Lists.html#17198" class="Bound">B</a><a id="17215" class="Symbol">)</a> <a id="17217" class="Symbol">→</a> <a id="17219" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="17224" href="plfa.part1.Lists.html#17196" class="Bound">A</a> <a id="17226" class="Symbol">→</a> <a id="17228" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="17233" href="plfa.part1.Lists.html#17198" class="Bound">B</a>
<a id="17235" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17187" class="Function">map</a> <a id="17239" href="plfa.part1.Lists.html#17239" class="Bound">f</a> <a id="17241" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>        <a id="17251" class="Symbol">=</a>  <a id="17254" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="17257" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17187" class="Function">map</a> <a id="17261" href="plfa.part1.Lists.html#17261" class="Bound">f</a> <a id="17263" class="Symbol">(</a><a id="17264" href="plfa.part1.Lists.html#17264" class="Bound">x</a> <a id="17266" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17268" href="plfa.part1.Lists.html#17268" class="Bound">xs</a><a id="17270" class="Symbol">)</a>  <a id="17273" class="Symbol">=</a>  <a id="17276" href="plfa.part1.Lists.html#17261" class="Bound">f</a> <a id="17278" href="plfa.part1.Lists.html#17264" class="Bound">x</a> <a id="17280" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17282" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="17286" href="plfa.part1.Lists.html#17261" class="Bound">f</a> <a id="17288" href="plfa.part1.Lists.html#17268" class="Bound">xs</a>
</pre>{% endraw %}
{::comment}
Map of the empty list is the empty list.
Map of a non-empty list yields a list
with head the same as the function applied to the head of the given list,
and tail the same as map of the function applied to the tail of the given list.
{:/}

空列表的映射是空列表。非空列表的映射生成一个列表，其头元素是原列表的头元素在应用函数之后的结果，其尾列表是原列表的尾列表映射后的结果。

{::comment}
Here is an example showing how to use map to increment every element of a list:
{:/}

下面的例子展示了如何使用映射来增加列表中的每一个元素：

{% raw %}<pre class="Agda"><a id="17749" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17749" class="Function">_</a> <a id="17751" class="Symbol">:</a> <a id="17753" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="17757" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17761" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="17763" class="Number">0</a> <a id="17765" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="17767" class="Number">1</a> <a id="17769" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="17771" class="Number">2</a> <a id="17773" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="17775" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="17777" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="17779" class="Number">1</a> <a id="17781" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="17783" class="Number">2</a> <a id="17785" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="17787" class="Number">3</a> <a id="17789" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="17791" class="Symbol">_</a> <a id="17793" class="Symbol">=</a>
  <a id="17797" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="17807" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17187" class="Function">map</a> <a id="17811" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17815" class="Symbol">(</a><a id="17816" class="Number">0</a> <a id="17818" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17820" class="Number">1</a> <a id="17822" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17824" class="Number">2</a> <a id="17826" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17828" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="17830" class="Symbol">)</a>
  <a id="17834" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="17842" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17846" class="Number">0</a> <a id="17848" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="17850" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="17854" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17858" class="Symbol">(</a><a id="17859" class="Number">1</a> <a id="17861" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17863" class="Number">2</a> <a id="17865" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17867" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="17869" class="Symbol">)</a>
  <a id="17873" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="17881" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17885" class="Number">0</a> <a id="17887" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="17889" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17893" class="Number">1</a> <a id="17895" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17897" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="17901" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17905" class="Symbol">(</a><a id="17906" class="Number">2</a> <a id="17908" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17910" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="17912" class="Symbol">)</a>
  <a id="17916" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="17924" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17928" class="Number">0</a> <a id="17930" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="17932" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17936" class="Number">1</a> <a id="17938" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17940" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17944" class="Number">2</a> <a id="17946" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17948" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="17952" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17956" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="17961" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="17969" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17973" class="Number">0</a> <a id="17975" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="17977" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17981" class="Number">1</a> <a id="17983" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17985" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="17989" class="Number">2</a> <a id="17991" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="17993" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="17998" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="18006" class="Number">1</a> <a id="18008" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#1340" class="InductiveConstructor Operator">∷</a> <a id="18010" class="Number">2</a> <a id="18012" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="18014" class="Number">3</a> <a id="18016" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="18018" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="18023" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Map requires time linear in the length of the list.
{:/}

映射需要关于列表长度线性的时间。

{::comment}
It is often convenient to exploit currying by applying
map to a function to yield a new function, and at a later
point applying the resulting function:
{:/}

我们常常可以利用柯里化，将映射作用于一个函数，获得另一个函数，然后在之后的时候应用获得的函数：

{% raw %}<pre class="Agda"><a id="sucs"></a><a id="18341" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#18341" class="Function">sucs</a> <a id="18346" class="Symbol">:</a> <a id="18348" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="18353" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a> <a id="18355" class="Symbol">→</a> <a id="18357" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="18362" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="18364" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#18341" class="Function">sucs</a> <a id="18369" class="Symbol">=</a> <a id="18371" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="18375" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a>

<a id="18380" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#18380" class="Function">_</a> <a id="18382" class="Symbol">:</a> <a id="18384" href="plfa.part1.Lists.html#18341" class="Function">sucs</a> <a id="18389" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="18391" class="Number">0</a> <a id="18393" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18395" class="Number">1</a> <a id="18397" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18399" class="Number">2</a> <a id="18401" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a> <a id="18403" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="18405" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="18407" class="Number">1</a> <a id="18409" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18411" class="Number">2</a> <a id="18413" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18415" class="Number">3</a> <a id="18417" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="18419" class="Symbol">_</a> <a id="18421" class="Symbol">=</a>
  <a id="18425" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="18435" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#18341" class="Function">sucs</a> <a id="18440" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="18442" class="Number">0</a> <a id="18444" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18446" class="Number">1</a> <a id="18448" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18450" class="Number">2</a> <a id="18452" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
  <a id="18456" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="18464" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#17187" class="Function">map</a> <a id="18468" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="18472" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="18474" class="Number">0</a> <a id="18476" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18478" class="Number">1</a> <a id="18480" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18482" class="Number">2</a> <a id="18484" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
  <a id="18488" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="18496" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#3884" class="InductiveConstructor Operator">[</a> <a id="18498" class="Number">1</a> <a id="18500" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18502" class="Number">2</a> <a id="18504" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="18506" class="Number">3</a> <a id="18508" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
  <a id="18512" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Any type that is parameterised on another type, such as lists, has a
corresponding map, which accepts a function and returns a function
from the type parameterised on the domain of the function to the type
parameterised on the range of the function. Further, a type that is
parameterised on _n_ types will have a map that is parameterised on
_n_ functions.
{:/}

任何对于另外一个类型参数化的类型，例如列表，都有对应的映射，其接受一个函数，并返回另一个从由给定函数定义域参数化的类型，到由给定函数值域参数化的函数。除此之外，一个对于 _n_ 个类型参数化的类型会有一个对于 _n_ 个函数参数化的映射。

{::comment}
#### Exercise `map-compose` (practice)
{:/}

#### 练习 `map-compose`（实践）

{::comment}
Prove that the map of a composition is equal to the composition of two maps:
{:/}

证明函数组合的映射是两个映射的组合：

    map (g ∘ f) ≡ map g ∘ map f

{::comment}
The last step of the proof requires extensionality.
{:/}

证明的最后一步需要外延性。

{% raw %}<pre class="Agda"><a id="19338" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
#### Exercise `map-++-distribute` (practice)
{:/}

#### 练习 `map-++-distribute`（实践）

{::comment}
Prove the following relationship between map and append:
{:/}

证明下列关于映射与附加的关系：

    map f (xs ++ ys) ≡ map f xs ++ map f ys

{% raw %}<pre class="Agda"><a id="19603" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
#### Exercise `map-Tree` (practice)
{:/}

#### 练习 `map-Tree`（实践）

{::comment}
Define a type of trees with leaves of type `A` and internal
nodes of type `B`:
{:/}

定义一个树数据类型，其叶节点类型为 `A`，内部节点类型为 `B`：

{% raw %}<pre class="Agda"><a id="19846" class="Keyword">data</a> <a id="Tree"></a><a id="19851" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#19851" class="Datatype">Tree</a> <a id="19856" class="Symbol">(</a><a id="19857" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19859" href="plfa.part1.Lists.html#19859" class="Bound">B</a> <a id="19861" class="Symbol">:</a> <a id="19863" class="PrimitiveType">Set</a><a id="19866" class="Symbol">)</a> <a id="19868" class="Symbol">:</a> <a id="19870" class="PrimitiveType">Set</a> <a id="19874" class="Keyword">where</a>
  <a id="Tree.leaf"></a><a id="19882" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#19882" class="InductiveConstructor">leaf</a> <a id="19887" class="Symbol">:</a> <a id="19889" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19891" class="Symbol">→</a> <a id="19893" href="plfa.part1.Lists.html#19851" class="Datatype">Tree</a> <a id="19898" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19900" href="plfa.part1.Lists.html#19859" class="Bound">B</a>
  <a id="Tree.node"></a><a id="19904" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#19904" class="InductiveConstructor">node</a> <a id="19909" class="Symbol">:</a> <a id="19911" href="plfa.part1.Lists.html#19851" class="Datatype">Tree</a> <a id="19916" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19918" href="plfa.part1.Lists.html#19859" class="Bound">B</a> <a id="19920" class="Symbol">→</a> <a id="19922" href="plfa.part1.Lists.html#19859" class="Bound">B</a> <a id="19924" class="Symbol">→</a> <a id="19926" href="plfa.part1.Lists.html#19851" class="Datatype">Tree</a> <a id="19931" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19933" href="plfa.part1.Lists.html#19859" class="Bound">B</a> <a id="19935" class="Symbol">→</a> <a id="19937" href="plfa.part1.Lists.html#19851" class="Datatype">Tree</a> <a id="19942" href="plfa.part1.Lists.html#19857" class="Bound">A</a> <a id="19944" href="plfa.part1.Lists.html#19859" class="Bound">B</a>
</pre>{% endraw %}
{::comment}
Define a suitable map operator over trees:
{:/}

定义一个对于树的映射运算符：

    map-Tree : ∀ {A B C D : Set} → (A → C) → (B → D) → Tree A B → Tree C D

{% raw %}<pre class="Agda"><a id="20108" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
## Fold {#Fold}
{:/}

## 折叠 {#Fold}

{::comment}
Fold takes an operator and a value, and uses the operator to combine
each of the elements of the list, taking the given value as the result
for the empty list:
{:/}

折叠取一个运算符和一个值，并使用运算符将列表中的元素合并至一个值，如果给定的列表为空，则使用给定的值：

{% raw %}<pre class="Agda"><a id="foldr"></a><a id="20421" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="20427" class="Symbol">:</a> <a id="20429" class="Symbol">∀</a> <a id="20431" class="Symbol">{</a><a id="20432" href="plfa.part1.Lists.html#20432" class="Bound">A</a> <a id="20434" href="plfa.part1.Lists.html#20434" class="Bound">B</a> <a id="20436" class="Symbol">:</a> <a id="20438" class="PrimitiveType">Set</a><a id="20441" class="Symbol">}</a> <a id="20443" class="Symbol">→</a> <a id="20445" class="Symbol">(</a><a id="20446" href="plfa.part1.Lists.html#20432" class="Bound">A</a> <a id="20448" class="Symbol">→</a> <a id="20450" href="plfa.part1.Lists.html#20434" class="Bound">B</a> <a id="20452" class="Symbol">→</a> <a id="20454" href="plfa.part1.Lists.html#20434" class="Bound">B</a><a id="20455" class="Symbol">)</a> <a id="20457" class="Symbol">→</a> <a id="20459" href="plfa.part1.Lists.html#20434" class="Bound">B</a> <a id="20461" class="Symbol">→</a> <a id="20463" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="20468" href="plfa.part1.Lists.html#20432" class="Bound">A</a> <a id="20470" class="Symbol">→</a> <a id="20472" href="plfa.part1.Lists.html#20434" class="Bound">B</a>
<a id="20474" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="20480" href="plfa.part1.Lists.html#20480" class="Bound Operator">_⊗_</a> <a id="20484" href="plfa.part1.Lists.html#20484" class="Bound">e</a> <a id="20486" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>        <a id="20496" class="Symbol">=</a>  <a id="20499" href="plfa.part1.Lists.html#20484" class="Bound">e</a>
<a id="20501" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="20507" href="plfa.part1.Lists.html#20507" class="Bound Operator">_⊗_</a> <a id="20511" href="plfa.part1.Lists.html#20511" class="Bound">e</a> <a id="20513" class="Symbol">(</a><a id="20514" href="plfa.part1.Lists.html#20514" class="Bound">x</a> <a id="20516" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="20518" href="plfa.part1.Lists.html#20518" class="Bound">xs</a><a id="20520" class="Symbol">)</a>  <a id="20523" class="Symbol">=</a>  <a id="20526" href="plfa.part1.Lists.html#20514" class="Bound">x</a> <a id="20528" href="plfa.part1.Lists.html#20507" class="Bound Operator">⊗</a> <a id="20530" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="20536" href="plfa.part1.Lists.html#20507" class="Bound Operator">_⊗_</a> <a id="20540" href="plfa.part1.Lists.html#20511" class="Bound">e</a> <a id="20542" href="plfa.part1.Lists.html#20518" class="Bound">xs</a>
</pre>{% endraw %}
{::comment}
Fold of the empty list is the given value.
Fold of a non-empty list uses the operator to combine
the head of the list and the fold of the tail of the list.
{:/}

空列表的折叠是给定的值。非空列表的折叠使用给定的运算符，将头元素和尾列表的折叠合并起来。

{::comment}
Here is an example showing how to use fold to find the sum of a list:
{:/}

下面的例子展示了如何使用折叠来对一个列表求和：

{% raw %}<pre class="Agda"><a id="20888" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20888" class="Function">_</a> <a id="20890" class="Symbol">:</a> <a id="20892" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="20898" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="20902" class="Number">0</a> <a id="20904" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="20906" class="Number">1</a> <a id="20908" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="20910" class="Number">2</a> <a id="20912" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="20914" class="Number">3</a> <a id="20916" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="20918" class="Number">4</a> <a id="20920" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a> <a id="20922" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="20924" class="Number">10</a>
<a id="20927" class="Symbol">_</a> <a id="20929" class="Symbol">=</a>
  <a id="20933" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="20943" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="20949" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="20953" class="Number">0</a> <a id="20955" class="Symbol">(</a><a id="20956" class="Number">1</a> <a id="20958" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="20960" class="Number">2</a> <a id="20962" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="20964" class="Number">3</a> <a id="20966" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="20968" class="Number">4</a> <a id="20970" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="20972" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="20974" class="Symbol">)</a>
  <a id="20978" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="20986" class="Number">1</a> <a id="20988" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="20990" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="20996" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21000" class="Number">0</a> <a id="21002" class="Symbol">(</a><a id="21003" class="Number">2</a> <a id="21005" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21007" class="Number">3</a> <a id="21009" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21011" class="Number">4</a> <a id="21013" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21015" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="21017" class="Symbol">)</a>
  <a id="21021" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21029" class="Number">1</a> <a id="21031" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21033" class="Symbol">(</a><a id="21034" class="Number">2</a> <a id="21036" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21038" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="21044" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21048" class="Number">0</a> <a id="21050" class="Symbol">(</a><a id="21051" class="Number">3</a> <a id="21053" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21055" class="Number">4</a> <a id="21057" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21059" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="21061" class="Symbol">))</a>
  <a id="21066" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21074" class="Number">1</a> <a id="21076" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21078" class="Symbol">(</a><a id="21079" class="Number">2</a> <a id="21081" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21083" class="Symbol">(</a><a id="21084" class="Number">3</a> <a id="21086" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21088" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="21094" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21098" class="Number">0</a> <a id="21100" class="Symbol">(</a><a id="21101" class="Number">4</a> <a id="21103" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="21105" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="21107" class="Symbol">)))</a>
  <a id="21113" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21121" class="Number">1</a> <a id="21123" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21125" class="Symbol">(</a><a id="21126" class="Number">2</a> <a id="21128" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21130" class="Symbol">(</a><a id="21131" class="Number">3</a> <a id="21133" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21135" class="Symbol">(</a><a id="21136" class="Number">4</a> <a id="21138" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21140" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="21146" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21150" class="Number">0</a> <a id="21152" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a><a id="21154" class="Symbol">)))</a>
  <a id="21160" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21168" class="Number">1</a> <a id="21170" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21172" class="Symbol">(</a><a id="21173" class="Number">2</a> <a id="21175" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21177" class="Symbol">(</a><a id="21178" class="Number">3</a> <a id="21180" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21182" class="Symbol">(</a><a id="21183" class="Number">4</a> <a id="21185" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21187" class="Number">0</a><a id="21188" class="Symbol">)))</a>
  <a id="21194" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Here we have an instance of `foldr` where `A` and `B` are both `ℕ`.
Fold requires time linear in the length of the list.
{:/}

折叠需要关于列表长度线性的时间。

{::comment}
It is often convenient to exploit currying by applying
fold to an operator and a value to yield a new function,
and at a later point applying the resulting function:
{:/}

我们常常可以利用柯里化，将折叠作用于一个运算符和一个值，获得另一个函数，然后在之后的时候应用获得的函数：

{% raw %}<pre class="Agda"><a id="sum"></a><a id="21601" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#21601" class="Function">sum</a> <a id="21605" class="Symbol">:</a> <a id="21607" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="21612" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a> <a id="21614" class="Symbol">→</a> <a id="21616" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="21618" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#21601" class="Function">sum</a> <a id="21622" class="Symbol">=</a> <a id="21624" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="21630" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21634" class="Number">0</a>

<a id="21637" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#21637" class="Function">_</a> <a id="21639" class="Symbol">:</a> <a id="21641" href="plfa.part1.Lists.html#21601" class="Function">sum</a> <a id="21645" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="21647" class="Number">1</a> <a id="21649" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21651" class="Number">2</a> <a id="21653" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21655" class="Number">3</a> <a id="21657" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21659" class="Number">4</a> <a id="21661" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a> <a id="21663" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="21665" class="Number">10</a>
<a id="21668" class="Symbol">_</a> <a id="21670" class="Symbol">=</a>
  <a id="21674" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="21684" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#21601" class="Function">sum</a> <a id="21688" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="21690" class="Number">1</a> <a id="21692" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21694" class="Number">2</a> <a id="21696" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21698" class="Number">3</a> <a id="21700" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21702" class="Number">4</a> <a id="21704" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
  <a id="21708" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21716" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="21722" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="21726" class="Number">0</a> <a id="21728" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="21730" class="Number">1</a> <a id="21732" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21734" class="Number">2</a> <a id="21736" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21738" class="Number">3</a> <a id="21740" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="21742" class="Number">4</a> <a id="21744" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
  <a id="21748" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="21756" class="Number">10</a>
  <a id="21761" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
Just as the list type has two constructors, `[]` and `_∷_`,
so the fold function takes two arguments, `e` and `_⊗_`
(in addition to the list argument).
In general, a data type with _n_ constructors will have
a corresponding fold function that takes _n_ arguments.
{:/}

正如列表由两个构造子 `[]` 和 `_∷_`，折叠函数取两个参数 `e` 和 `_⊗_`
（除去列表参数）。推广来说，一个有 _n_ 个构造子的数据类型，会有对应的取 _n_ 个参数的折叠函数。

{::comment}
As another example, observe that
{:/}

举另外一个例子，观察

    foldr _∷_ [] xs ≡ xs

{::comment}
Here, if `xs` is of type `List A`, then we see we have an instance of
`foldr` where `A` is `A` and `B` is `List A`.  It follows that
{:/}

若 `xs` 的类型为 `List A`，那么我们就会有一个 `foldr` 的实例，其中的
`A` 为 `A`，而 `B` 为 `List A`。它遵循

    xs ++ ys ≡ foldr _∷_ ys xs

{::comment}
Demonstrating both these equations is left as an exercise.
{:/}

二者相等的证明留作练习。


#### Exercise `product` (recommended)
{:/}

#### 练习 `product` （推荐）

{::comment}
Use fold to define a function to find the product of a list of numbers.
For example:
{:/}

使用折叠来定义一个计算列表数字之积的函数。例如：

    product [ 1 , 2 , 3 , 4 ] ≡ 24

{::comment}
{% raw %}<pre class="Agda"><a id="22843" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="22880" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `foldr-++` (recommended)
{:/}

#### 练习 `foldr-++` （推荐）

{::comment}
Show that fold and append are related as follows:
{:/}

证明折叠和附加有如下的关系：

    foldr _⊗_ e (xs ++ ys) ≡ foldr _⊗_ (foldr _⊗_ e ys) xs

{% raw %}<pre class="Agda"><a id="23128" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
#### Exercise `foldr-∷` (practice)

Show

    foldr _∷_ [] xs ≡ xs

Show as a consequence of `foldr-++` above that

    xs ++ ys ≡ foldr _∷_ ys xs

{::comment}
#### Exercise `map-is-foldr`
{:/}

#### 练习 `map-is-foldr`

{::comment}
Show that map can be defined using fold:
{:/}

证明映射可以用折叠定义：

{% raw %}<pre class="Agda"><a id="23452" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
This requires extensionality.
{:/}

此证明需要外延性。

{::comment}
#### Exercise `map-is-foldr` (practice)
{:/}

#### 练习 `map-is-foldr`（实践）

{::comment}
Show that map can be defined using fold:
{:/}

请证明 map 可使用 fold 来定义：

    map f ≡ foldr (λ x xs → f x ∷ xs) []

{::comment}
The proof requires extensionality.
{:/}

此证明需要外延性。

{% raw %}<pre class="Agda"><a id="23817" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
#### Exercise `fold-Tree` (practice)
{:/}

#### 练习 `fold-Tree`（实践）

{::comment}
Define a suitable fold function for the type of trees given earlier:
{:/}

请为预先给定的三个类型定义一个合适的折叠函数：

    fold-Tree : ∀ {A B C : Set} → (A → C) → (C → B → C → C) → Tree A B → C


{::comment}
{% raw %}<pre class="Agda"><a id="24130" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="24167" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `map-is-fold-Tree` (practice)
{:/}

#### 练习 `map-is-fold-Tree`（实践）

{::comment}
Demonstrate an analogue of `map-is-foldr` for the type of trees.
{:/}

对于树数据类型，证明与 `map-is-foldr` 相似的性质。

{::comment}
{% raw %}<pre class="Agda"><a id="24413" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="24450" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `sum-downFrom` (stretch)
{:/}

#### 证明 `sum-downFrom` （延伸）

{::comment}
Define a function that counts down as follows:
{:/}

定义一个向下数数的函数：

{% raw %}<pre class="Agda"><a id="downFrom"></a><a id="24637" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#24637" class="Function">downFrom</a> <a id="24646" class="Symbol">:</a> <a id="24648" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a> <a id="24650" class="Symbol">→</a> <a id="24652" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="24657" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a>
<a id="24659" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#24637" class="Function">downFrom</a> <a id="24668" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>     <a id="24677" class="Symbol">=</a>  <a id="24680" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="24683" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#24637" class="Function">downFrom</a> <a id="24692" class="Symbol">(</a><a id="24693" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="24697" href="plfa.part1.Lists.html#24697" class="Bound">n</a><a id="24698" class="Symbol">)</a>  <a id="24701" class="Symbol">=</a>  <a id="24704" href="plfa.part1.Lists.html#24697" class="Bound">n</a> <a id="24706" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="24708" href="plfa.part1.Lists.html#24637" class="Function">downFrom</a> <a id="24717" href="plfa.part1.Lists.html#24697" class="Bound">n</a>
</pre>{% endraw %}
{::comment}
For example:
{:/}

例如：

{% raw %}<pre class="Agda"><a id="24764" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#24764" class="Function">_</a> <a id="24766" class="Symbol">:</a> <a id="24768" href="plfa.part1.Lists.html#24637" class="Function">downFrom</a> <a id="24777" class="Number">3</a> <a id="24779" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="24781" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="24783" class="Number">2</a> <a id="24785" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="24787" class="Number">1</a> <a id="24789" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="24791" class="Number">0</a> <a id="24793" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="24795" class="Symbol">_</a> <a id="24797" class="Symbol">=</a> <a id="24799" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
</pre>{% endraw %}
{::comment}
Prove that the sum of the numbers `(n - 1) + ⋯ + 0` is
equal to `n * (n ∸ 1) / 2`:
{:/}

证明数列之和 `(n - 1) + ⋯ + 0` 等于 `n * (n ∸ 1) / 2`：

    sum (downFrom n) * 2 ≡ n * (n ∸ 1)


{::comment}
## Monoids
{:/}

## 幺半群

{::comment}
Typically when we use a fold the operator is associative and the
value is a left and right identity for the operator, meaning that the
operator and the value form a _monoid_.
{:/}

一般来说，我们会对于折叠函数使用一个满足结合律的运算符，和这个运算符的左右幺元。这意味着这个运算符和这个值形成了一个**幺半群（Monoid）**。

{::comment}
We can define a monoid as a suitable record type:
{:/}

我们可以用一个合适的记录类型来定义幺半群：

{% raw %}<pre class="Agda"><a id="25401" class="Keyword">record</a> <a id="IsMonoid"></a><a id="25408" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25408" class="Record">IsMonoid</a> <a id="25417" class="Symbol">{</a><a id="25418" href="plfa.part1.Lists.html#25418" class="Bound">A</a> <a id="25420" class="Symbol">:</a> <a id="25422" class="PrimitiveType">Set</a><a id="25425" class="Symbol">}</a> <a id="25427" class="Symbol">(</a><a id="25428" href="plfa.part1.Lists.html#25428" class="Bound Operator">_⊗_</a> <a id="25432" class="Symbol">:</a> <a id="25434" href="plfa.part1.Lists.html#25418" class="Bound">A</a> <a id="25436" class="Symbol">→</a> <a id="25438" href="plfa.part1.Lists.html#25418" class="Bound">A</a> <a id="25440" class="Symbol">→</a> <a id="25442" href="plfa.part1.Lists.html#25418" class="Bound">A</a><a id="25443" class="Symbol">)</a> <a id="25445" class="Symbol">(</a><a id="25446" href="plfa.part1.Lists.html#25446" class="Bound">e</a> <a id="25448" class="Symbol">:</a> <a id="25450" href="plfa.part1.Lists.html#25418" class="Bound">A</a><a id="25451" class="Symbol">)</a> <a id="25453" class="Symbol">:</a> <a id="25455" class="PrimitiveType">Set</a> <a id="25459" class="Keyword">where</a>
  <a id="25467" class="Keyword">field</a>
    <a id="IsMonoid.assoc"></a><a id="25477" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25477" class="Field">assoc</a> <a id="25483" class="Symbol">:</a> <a id="25485" class="Symbol">∀</a> <a id="25487" class="Symbol">(</a><a id="25488" href="plfa.part1.Lists.html#25488" class="Bound">x</a> <a id="25490" href="plfa.part1.Lists.html#25490" class="Bound">y</a> <a id="25492" href="plfa.part1.Lists.html#25492" class="Bound">z</a> <a id="25494" class="Symbol">:</a> <a id="25496" href="plfa.part1.Lists.html#25418" class="Bound">A</a><a id="25497" class="Symbol">)</a> <a id="25499" class="Symbol">→</a> <a id="25501" class="Symbol">(</a><a id="25502" href="plfa.part1.Lists.html#25488" class="Bound">x</a> <a id="25504" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25506" href="plfa.part1.Lists.html#25490" class="Bound">y</a><a id="25507" class="Symbol">)</a> <a id="25509" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25511" href="plfa.part1.Lists.html#25492" class="Bound">z</a> <a id="25513" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="25515" href="plfa.part1.Lists.html#25488" class="Bound">x</a> <a id="25517" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25519" class="Symbol">(</a><a id="25520" href="plfa.part1.Lists.html#25490" class="Bound">y</a> <a id="25522" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25524" href="plfa.part1.Lists.html#25492" class="Bound">z</a><a id="25525" class="Symbol">)</a>
    <a id="IsMonoid.identityˡ"></a><a id="25531" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25531" class="Field">identityˡ</a> <a id="25541" class="Symbol">:</a> <a id="25543" class="Symbol">∀</a> <a id="25545" class="Symbol">(</a><a id="25546" href="plfa.part1.Lists.html#25546" class="Bound">x</a> <a id="25548" class="Symbol">:</a> <a id="25550" href="plfa.part1.Lists.html#25418" class="Bound">A</a><a id="25551" class="Symbol">)</a> <a id="25553" class="Symbol">→</a> <a id="25555" href="plfa.part1.Lists.html#25446" class="Bound">e</a> <a id="25557" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25559" href="plfa.part1.Lists.html#25546" class="Bound">x</a> <a id="25561" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="25563" href="plfa.part1.Lists.html#25546" class="Bound">x</a>
    <a id="IsMonoid.identityʳ"></a><a id="25569" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25569" class="Field">identityʳ</a> <a id="25579" class="Symbol">:</a> <a id="25581" class="Symbol">∀</a> <a id="25583" class="Symbol">(</a><a id="25584" href="plfa.part1.Lists.html#25584" class="Bound">x</a> <a id="25586" class="Symbol">:</a> <a id="25588" href="plfa.part1.Lists.html#25418" class="Bound">A</a><a id="25589" class="Symbol">)</a> <a id="25591" class="Symbol">→</a> <a id="25593" href="plfa.part1.Lists.html#25584" class="Bound">x</a> <a id="25595" href="plfa.part1.Lists.html#25428" class="Bound Operator">⊗</a> <a id="25597" href="plfa.part1.Lists.html#25446" class="Bound">e</a> <a id="25599" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="25601" href="plfa.part1.Lists.html#25584" class="Bound">x</a>

<a id="25604" class="Keyword">open</a> <a id="25609" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25408" class="Module">IsMonoid</a>
</pre>{% endraw %}
{::comment}
As examples, sum and zero, multiplication and one, and append and the empty
list, are all examples of monoids:
{:/}

举例来说，加法和零，乘法和一，附加和空列表，都是幺半群：

{% raw %}<pre class="Agda"><a id="+-monoid"></a><a id="25786" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25786" class="Function">+-monoid</a> <a id="25795" class="Symbol">:</a> <a id="25797" href="plfa.part1.Lists.html#25408" class="Record">IsMonoid</a> <a id="25806" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a> <a id="25810" class="Number">0</a>
<a id="25812" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25786" class="Function">+-monoid</a> <a id="25821" class="Symbol">=</a>
  <a id="25825" class="Keyword">record</a>
    <a id="25836" class="Symbol">{</a> <a id="25838" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25477" class="Field">assoc</a> <a id="25844" class="Symbol">=</a> <a id="25846" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11578" class="Function">+-assoc</a>
    <a id="25858" class="Symbol">;</a> <a id="25860" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25531" class="Field">identityˡ</a> <a id="25870" class="Symbol">=</a> <a id="25872" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11679" class="Function">+-identityˡ</a>
    <a id="25888" class="Symbol">;</a> <a id="25890" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25569" class="Field">identityʳ</a> <a id="25900" class="Symbol">=</a> <a id="25902" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11734" class="Function">+-identityʳ</a>
    <a id="25918" class="Symbol">}</a>

<a id="*-monoid"></a><a id="25921" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25921" class="Function">*-monoid</a> <a id="25930" class="Symbol">:</a> <a id="25932" href="plfa.part1.Lists.html#25408" class="Record">IsMonoid</a> <a id="25941" href="Agda.Builtin.Nat.html#501" class="Primitive Operator">_*_</a> <a id="25945" class="Number">1</a>
<a id="25947" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25921" class="Function">*-monoid</a> <a id="25956" class="Symbol">=</a>
  <a id="25960" class="Keyword">record</a>
    <a id="25971" class="Symbol">{</a> <a id="25973" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25477" class="Field">assoc</a> <a id="25979" class="Symbol">=</a> <a id="25981" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#18464" class="Function">*-assoc</a>
    <a id="25993" class="Symbol">;</a> <a id="25995" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25531" class="Field">identityˡ</a> <a id="26005" class="Symbol">=</a> <a id="26007" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#17362" class="Function">*-identityˡ</a>
    <a id="26023" class="Symbol">;</a> <a id="26025" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25569" class="Field">identityʳ</a> <a id="26035" class="Symbol">=</a> <a id="26037" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#17426" class="Function">*-identityʳ</a>
    <a id="26053" class="Symbol">}</a>

<a id="++-monoid"></a><a id="26056" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26056" class="Function">++-monoid</a> <a id="26066" class="Symbol">:</a> <a id="26068" class="Symbol">∀</a> <a id="26070" class="Symbol">{</a><a id="26071" href="plfa.part1.Lists.html#26071" class="Bound">A</a> <a id="26073" class="Symbol">:</a> <a id="26075" class="PrimitiveType">Set</a><a id="26078" class="Symbol">}</a> <a id="26080" class="Symbol">→</a> <a id="26082" href="plfa.part1.Lists.html#25408" class="Record">IsMonoid</a> <a id="26091" class="Symbol">{</a><a id="26092" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="26097" href="plfa.part1.Lists.html#26071" class="Bound">A</a><a id="26098" class="Symbol">}</a> <a id="26100" href="plfa.part1.Lists.html#4663" class="Function Operator">_++_</a> <a id="26105" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
<a id="26108" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26056" class="Function">++-monoid</a> <a id="26118" class="Symbol">=</a>
  <a id="26122" class="Keyword">record</a>
    <a id="26133" class="Symbol">{</a> <a id="26135" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25477" class="Field">assoc</a> <a id="26141" class="Symbol">=</a> <a id="26143" href="plfa.part1.Lists.html#6401" class="Function">++-assoc</a>
    <a id="26156" class="Symbol">;</a> <a id="26158" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25531" class="Field">identityˡ</a> <a id="26168" class="Symbol">=</a> <a id="26170" href="plfa.part1.Lists.html#7977" class="Function">++-identityˡ</a>
    <a id="26187" class="Symbol">;</a> <a id="26189" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25569" class="Field">identityʳ</a> <a id="26199" class="Symbol">=</a> <a id="26201" href="plfa.part1.Lists.html#8191" class="Function">++-identityʳ</a>
    <a id="26218" class="Symbol">}</a>
</pre>{% endraw %}
{::comment}
If `_⊗_` and `e` form a monoid, then we can re-express fold on the
same operator and an arbitrary value:
{:/}

如果 `_⊗_` 和 `e` 构成一个幺半群，那么我们可以用相同的运算符和一个任意的值来表示折叠：

{% raw %}<pre class="Agda"><a id="foldr-monoid"></a><a id="26403" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26403" class="Function">foldr-monoid</a> <a id="26416" class="Symbol">:</a> <a id="26418" class="Symbol">∀</a> <a id="26420" class="Symbol">{</a><a id="26421" href="plfa.part1.Lists.html#26421" class="Bound">A</a> <a id="26423" class="Symbol">:</a> <a id="26425" class="PrimitiveType">Set</a><a id="26428" class="Symbol">}</a> <a id="26430" class="Symbol">(</a><a id="26431" href="plfa.part1.Lists.html#26431" class="Bound Operator">_⊗_</a> <a id="26435" class="Symbol">:</a> <a id="26437" href="plfa.part1.Lists.html#26421" class="Bound">A</a> <a id="26439" class="Symbol">→</a> <a id="26441" href="plfa.part1.Lists.html#26421" class="Bound">A</a> <a id="26443" class="Symbol">→</a> <a id="26445" href="plfa.part1.Lists.html#26421" class="Bound">A</a><a id="26446" class="Symbol">)</a> <a id="26448" class="Symbol">(</a><a id="26449" href="plfa.part1.Lists.html#26449" class="Bound">e</a> <a id="26451" class="Symbol">:</a> <a id="26453" href="plfa.part1.Lists.html#26421" class="Bound">A</a><a id="26454" class="Symbol">)</a> <a id="26456" class="Symbol">→</a> <a id="26458" href="plfa.part1.Lists.html#25408" class="Record">IsMonoid</a> <a id="26467" href="plfa.part1.Lists.html#26431" class="Bound Operator">_⊗_</a> <a id="26471" href="plfa.part1.Lists.html#26449" class="Bound">e</a> <a id="26473" class="Symbol">→</a>
  <a id="26477" class="Symbol">∀</a> <a id="26479" class="Symbol">(</a><a id="26480" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26480" class="Bound">xs</a> <a id="26483" class="Symbol">:</a> <a id="26485" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="26490" href="plfa.part1.Lists.html#26421" class="Bound">A</a><a id="26491" class="Symbol">)</a> <a id="26493" class="Symbol">(</a><a id="26494" href="plfa.part1.Lists.html#26494" class="Bound">y</a> <a id="26496" class="Symbol">:</a> <a id="26498" href="plfa.part1.Lists.html#26421" class="Bound">A</a><a id="26499" class="Symbol">)</a> <a id="26501" class="Symbol">→</a> <a id="26503" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26509" href="plfa.part1.Lists.html#26431" class="Bound Operator">_⊗_</a> <a id="26513" href="plfa.part1.Lists.html#26494" class="Bound">y</a> <a id="26515" href="plfa.part1.Lists.html#26480" class="Bound">xs</a> <a id="26518" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="26520" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26526" href="plfa.part1.Lists.html#26431" class="Bound Operator">_⊗_</a> <a id="26530" href="plfa.part1.Lists.html#26449" class="Bound">e</a> <a id="26532" href="plfa.part1.Lists.html#26480" class="Bound">xs</a> <a id="26535" href="plfa.part1.Lists.html#26431" class="Bound Operator">⊗</a> <a id="26537" href="plfa.part1.Lists.html#26494" class="Bound">y</a>
<a id="26539" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26403" class="Function">foldr-monoid</a> <a id="26552" href="plfa.part1.Lists.html#26552" class="Bound Operator">_⊗_</a> <a id="26556" href="plfa.part1.Lists.html#26556" class="Bound">e</a> <a id="26558" href="plfa.part1.Lists.html#26558" class="Bound">⊗-monoid</a> <a id="26567" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="26570" href="plfa.part1.Lists.html#26570" class="Bound">y</a> <a id="26572" class="Symbol">=</a>
  <a id="26576" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="26586" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="26592" href="plfa.part1.Lists.html#26552" class="Bound Operator">_⊗_</a> <a id="26596" href="plfa.part1.Lists.html#26570" class="Bound">y</a> <a id="26598" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="26603" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="26611" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26570" class="Bound">y</a>
  <a id="26615" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="26618" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a> <a id="26622" class="Symbol">(</a><a id="26623" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25531" class="Field">identityˡ</a> <a id="26633" href="plfa.part1.Lists.html#26558" class="Bound">⊗-monoid</a> <a id="26642" href="plfa.part1.Lists.html#26570" class="Bound">y</a><a id="26643" class="Symbol">)</a> <a id="26645" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="26651" class="Symbol">(</a><a id="26652" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26556" class="Bound">e</a> <a id="26654" href="plfa.part1.Lists.html#26552" class="Bound Operator">⊗</a> <a id="26656" href="plfa.part1.Lists.html#26570" class="Bound">y</a><a id="26657" class="Symbol">)</a>
  <a id="26661" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="26669" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="26675" href="plfa.part1.Lists.html#26552" class="Bound Operator">_⊗_</a> <a id="26679" href="plfa.part1.Lists.html#26556" class="Bound">e</a> <a id="26681" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="26684" href="plfa.part1.Lists.html#26552" class="Bound Operator">⊗</a> <a id="26686" href="plfa.part1.Lists.html#26570" class="Bound">y</a>
  <a id="26690" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="26692" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26403" class="Function">foldr-monoid</a> <a id="26705" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26709" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26711" href="plfa.part1.Lists.html#26711" class="Bound">⊗-monoid</a> <a id="26720" class="Symbol">(</a><a id="26721" href="plfa.part1.Lists.html#26721" class="Bound">x</a> <a id="26723" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="26725" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26727" class="Symbol">)</a> <a id="26729" href="plfa.part1.Lists.html#26729" class="Bound">y</a> <a id="26731" class="Symbol">=</a>
  <a id="26735" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="26745" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="26751" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26755" href="plfa.part1.Lists.html#26729" class="Bound">y</a> <a id="26757" class="Symbol">(</a><a id="26758" href="plfa.part1.Lists.html#26721" class="Bound">x</a> <a id="26760" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="26762" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26764" class="Symbol">)</a>
  <a id="26768" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="26776" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26721" class="Bound">x</a> <a id="26778" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26780" class="Symbol">(</a><a id="26781" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26787" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26791" href="plfa.part1.Lists.html#26729" class="Bound">y</a> <a id="26793" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26795" class="Symbol">)</a>
  <a id="26799" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="26802" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="26807" class="Symbol">(</a><a id="26808" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26721" class="Bound">x</a> <a id="26810" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗_</a><a id="26812" class="Symbol">)</a> <a id="26814" class="Symbol">(</a><a id="26815" href="plfa.part1.Lists.html#26403" class="Function">foldr-monoid</a> <a id="26828" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26832" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26834" href="plfa.part1.Lists.html#26711" class="Bound">⊗-monoid</a> <a id="26843" href="plfa.part1.Lists.html#26725" class="Bound">xs</a> <a id="26846" href="plfa.part1.Lists.html#26729" class="Bound">y</a><a id="26847" class="Symbol">)</a> <a id="26849" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="26855" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26721" class="Bound">x</a> <a id="26857" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26859" class="Symbol">(</a><a id="26860" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26866" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26870" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26872" href="plfa.part1.Lists.html#26725" class="Bound">xs</a> <a id="26875" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26877" href="plfa.part1.Lists.html#26729" class="Bound">y</a><a id="26878" class="Symbol">)</a>
  <a id="26882" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="26885" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a> <a id="26889" class="Symbol">(</a><a id="26890" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#25477" class="Field">assoc</a> <a id="26896" href="plfa.part1.Lists.html#26711" class="Bound">⊗-monoid</a> <a id="26905" href="plfa.part1.Lists.html#26721" class="Bound">x</a> <a id="26907" class="Symbol">(</a><a id="26908" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26914" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26918" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26920" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26922" class="Symbol">)</a> <a id="26924" href="plfa.part1.Lists.html#26729" class="Bound">y</a><a id="26925" class="Symbol">)</a> <a id="26927" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="26933" class="Symbol">(</a><a id="26934" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26721" class="Bound">x</a> <a id="26936" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26938" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="26944" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26948" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26950" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26952" class="Symbol">)</a> <a id="26954" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26956" href="plfa.part1.Lists.html#26729" class="Bound">y</a>
  <a id="26960" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="26968" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="26974" href="plfa.part1.Lists.html#26705" class="Bound Operator">_⊗_</a> <a id="26978" href="plfa.part1.Lists.html#26709" class="Bound">e</a> <a id="26980" class="Symbol">(</a><a id="26981" href="plfa.part1.Lists.html#26721" class="Bound">x</a> <a id="26983" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="26985" href="plfa.part1.Lists.html#26725" class="Bound">xs</a><a id="26987" class="Symbol">)</a> <a id="26989" href="plfa.part1.Lists.html#26705" class="Bound Operator">⊗</a> <a id="26991" href="plfa.part1.Lists.html#26729" class="Bound">y</a>
  <a id="26995" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
In a previous exercise we showed the following.
{:/}

在之前的练习中，我们证明了以下定理：

{% raw %}<pre class="Agda"><a id="27092" class="Keyword">postulate</a>
  <a id="foldr-++"></a><a id="27104" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#27104" class="Postulate">foldr-++</a> <a id="27113" class="Symbol">:</a> <a id="27115" class="Symbol">∀</a> <a id="27117" class="Symbol">{</a><a id="27118" href="plfa.part1.Lists.html#27118" class="Bound">A</a> <a id="27120" class="Symbol">:</a> <a id="27122" class="PrimitiveType">Set</a><a id="27125" class="Symbol">}</a> <a id="27127" class="Symbol">(</a><a id="27128" href="plfa.part1.Lists.html#27128" class="Bound Operator">_⊗_</a> <a id="27132" class="Symbol">:</a> <a id="27134" href="plfa.part1.Lists.html#27118" class="Bound">A</a> <a id="27136" class="Symbol">→</a> <a id="27138" href="plfa.part1.Lists.html#27118" class="Bound">A</a> <a id="27140" class="Symbol">→</a> <a id="27142" href="plfa.part1.Lists.html#27118" class="Bound">A</a><a id="27143" class="Symbol">)</a> <a id="27145" class="Symbol">(</a><a id="27146" href="plfa.part1.Lists.html#27146" class="Bound">e</a> <a id="27148" class="Symbol">:</a> <a id="27150" href="plfa.part1.Lists.html#27118" class="Bound">A</a><a id="27151" class="Symbol">)</a> <a id="27153" class="Symbol">(</a><a id="27154" href="plfa.part1.Lists.html#27154" class="Bound">xs</a> <a id="27157" href="plfa.part1.Lists.html#27157" class="Bound">ys</a> <a id="27160" class="Symbol">:</a> <a id="27162" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="27167" href="plfa.part1.Lists.html#27118" class="Bound">A</a><a id="27168" class="Symbol">)</a> <a id="27170" class="Symbol">→</a>
    <a id="27176" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="27182" href="plfa.part1.Lists.html#27128" class="Bound Operator">_⊗_</a> <a id="27186" href="plfa.part1.Lists.html#27146" class="Bound">e</a> <a id="27188" class="Symbol">(</a><a id="27189" href="plfa.part1.Lists.html#27154" class="Bound">xs</a> <a id="27192" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="27195" href="plfa.part1.Lists.html#27157" class="Bound">ys</a><a id="27197" class="Symbol">)</a> <a id="27199" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="27201" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27207" href="plfa.part1.Lists.html#27128" class="Bound Operator">_⊗_</a> <a id="27211" class="Symbol">(</a><a id="27212" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27218" href="plfa.part1.Lists.html#27128" class="Bound Operator">_⊗_</a> <a id="27222" href="plfa.part1.Lists.html#27146" class="Bound">e</a> <a id="27224" href="plfa.part1.Lists.html#27157" class="Bound">ys</a><a id="27226" class="Symbol">)</a> <a id="27228" href="plfa.part1.Lists.html#27154" class="Bound">xs</a>
</pre>{% endraw %}
{::comment}
As a consequence, using a previous exercise, we have the following:
{:/}

使用之前练习中的一个结论，我们可以得到如下推论：

{% raw %}<pre class="Agda"><a id="foldr-monoid-++"></a><a id="27352" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#27352" class="Function">foldr-monoid-++</a> <a id="27368" class="Symbol">:</a> <a id="27370" class="Symbol">∀</a> <a id="27372" class="Symbol">{</a><a id="27373" href="plfa.part1.Lists.html#27373" class="Bound">A</a> <a id="27375" class="Symbol">:</a> <a id="27377" class="PrimitiveType">Set</a><a id="27380" class="Symbol">}</a> <a id="27382" class="Symbol">(</a><a id="27383" href="plfa.part1.Lists.html#27383" class="Bound Operator">_⊗_</a> <a id="27387" class="Symbol">:</a> <a id="27389" href="plfa.part1.Lists.html#27373" class="Bound">A</a> <a id="27391" class="Symbol">→</a> <a id="27393" href="plfa.part1.Lists.html#27373" class="Bound">A</a> <a id="27395" class="Symbol">→</a> <a id="27397" href="plfa.part1.Lists.html#27373" class="Bound">A</a><a id="27398" class="Symbol">)</a> <a id="27400" class="Symbol">(</a><a id="27401" href="plfa.part1.Lists.html#27401" class="Bound">e</a> <a id="27403" class="Symbol">:</a> <a id="27405" href="plfa.part1.Lists.html#27373" class="Bound">A</a><a id="27406" class="Symbol">)</a> <a id="27408" class="Symbol">→</a> <a id="27410" href="plfa.part1.Lists.html#25408" class="Record">IsMonoid</a> <a id="27419" href="plfa.part1.Lists.html#27383" class="Bound Operator">_⊗_</a> <a id="27423" href="plfa.part1.Lists.html#27401" class="Bound">e</a> <a id="27425" class="Symbol">→</a>
  <a id="27429" class="Symbol">∀</a> <a id="27431" class="Symbol">(</a><a id="27432" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#27432" class="Bound">xs</a> <a id="27435" href="plfa.part1.Lists.html#27435" class="Bound">ys</a> <a id="27438" class="Symbol">:</a> <a id="27440" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="27445" href="plfa.part1.Lists.html#27373" class="Bound">A</a><a id="27446" class="Symbol">)</a> <a id="27448" class="Symbol">→</a> <a id="27450" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27456" href="plfa.part1.Lists.html#27383" class="Bound Operator">_⊗_</a> <a id="27460" href="plfa.part1.Lists.html#27401" class="Bound">e</a> <a id="27462" class="Symbol">(</a><a id="27463" href="plfa.part1.Lists.html#27432" class="Bound">xs</a> <a id="27466" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="27469" href="plfa.part1.Lists.html#27435" class="Bound">ys</a><a id="27471" class="Symbol">)</a> <a id="27473" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="27475" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27481" href="plfa.part1.Lists.html#27383" class="Bound Operator">_⊗_</a> <a id="27485" href="plfa.part1.Lists.html#27401" class="Bound">e</a> <a id="27487" href="plfa.part1.Lists.html#27432" class="Bound">xs</a> <a id="27490" href="plfa.part1.Lists.html#27383" class="Bound Operator">⊗</a> <a id="27492" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27498" href="plfa.part1.Lists.html#27383" class="Bound Operator">_⊗_</a> <a id="27502" href="plfa.part1.Lists.html#27401" class="Bound">e</a> <a id="27504" href="plfa.part1.Lists.html#27435" class="Bound">ys</a>
<a id="27507" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#27352" class="Function">foldr-monoid-++</a> <a id="27523" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27527" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27529" href="plfa.part1.Lists.html#27529" class="Bound">monoid-⊗</a> <a id="27538" href="plfa.part1.Lists.html#27538" class="Bound">xs</a> <a id="27541" href="plfa.part1.Lists.html#27541" class="Bound">ys</a> <a id="27544" class="Symbol">=</a>
  <a id="27548" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="27558" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="27564" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27568" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27570" class="Symbol">(</a><a id="27571" href="plfa.part1.Lists.html#27538" class="Bound">xs</a> <a id="27574" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="27577" href="plfa.part1.Lists.html#27541" class="Bound">ys</a><a id="27579" class="Symbol">)</a>
  <a id="27583" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="27586" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#27104" class="Postulate">foldr-++</a> <a id="27595" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27599" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27601" href="plfa.part1.Lists.html#27538" class="Bound">xs</a> <a id="27604" href="plfa.part1.Lists.html#27541" class="Bound">ys</a> <a id="27607" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="27613" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="27619" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27623" class="Symbol">(</a><a id="27624" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27630" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27634" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27636" href="plfa.part1.Lists.html#27541" class="Bound">ys</a><a id="27638" class="Symbol">)</a> <a id="27640" href="plfa.part1.Lists.html#27538" class="Bound">xs</a>
  <a id="27645" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="27648" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#26403" class="Function">foldr-monoid</a> <a id="27661" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27665" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27667" href="plfa.part1.Lists.html#27529" class="Bound">monoid-⊗</a> <a id="27676" href="plfa.part1.Lists.html#27538" class="Bound">xs</a> <a id="27679" class="Symbol">(</a><a id="27680" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27686" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27690" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27692" href="plfa.part1.Lists.html#27541" class="Bound">ys</a><a id="27694" class="Symbol">)</a> <a id="27696" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="27702" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#20421" class="Function">foldr</a> <a id="27708" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27712" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27714" href="plfa.part1.Lists.html#27538" class="Bound">xs</a> <a id="27717" href="plfa.part1.Lists.html#27523" class="Bound Operator">⊗</a> <a id="27719" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="27725" href="plfa.part1.Lists.html#27523" class="Bound Operator">_⊗_</a> <a id="27729" href="plfa.part1.Lists.html#27527" class="Bound">e</a> <a id="27731" href="plfa.part1.Lists.html#27541" class="Bound">ys</a>
  <a id="27736" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}
{::comment}
#### Exercise `foldl` (practice)
{:/}

#### 练习 `foldl`（实践）

{::comment}
Define a function `foldl` which is analogous to `foldr`, but where
operations associate to the left rather than the right.  For example:
{:/}

定义一个函数 `foldl`，与 `foldr` 相似，但是运算符向左结合，而不是向右。例如：

    foldr _⊗_ e [ x , y , z ]  =  x ⊗ (y ⊗ (z ⊗ e))
    foldl _⊗_ e [ x , y , z ]  =  ((e ⊗ x) ⊗ y) ⊗ z

{::comment}
{% raw %}<pre class="Agda"><a id="28140" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="28177" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}

{::comment}
#### Exercise `foldr-monoid-foldl`
{:/}

#### 练习 `foldr-monoid-foldl`（实践）

{::comment}
Show that if `_⊗_` and `e` form a monoid, then `foldr _⊗_ e` and
`foldl _⊗_ e` always compute the same result.
{:/}

证明如果 `_⊗_` 和 `e` 构成幺半群，那么 `foldr _⊗_ e` 和 `foldl _⊗_ e` 的结果永远是相同的。

{::comment}
{% raw %}<pre class="Agda"><a id="28497" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="28534" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}

{::comment}
## All {#All}
{:/}

## 所有 {#All}

{::comment}
We can also define predicates over lists. Two of the most important
are `All` and `Any`.
{:/}

我们也可以定义关于列表的谓词。最重要的两个谓词是 `All` 和 `Any`。

{::comment}
Predicate `All P` holds if predicate `P` is satisfied by every element of a list:
{:/}

谓词 `All P` 当列表里的所有元素满足 `P` 时成立：

{% raw %}<pre class="Agda"><a id="28884" class="Keyword">data</a> <a id="All"></a><a id="28889" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28889" class="Datatype">All</a> <a id="28893" class="Symbol">{</a><a id="28894" href="plfa.part1.Lists.html#28894" class="Bound">A</a> <a id="28896" class="Symbol">:</a> <a id="28898" class="PrimitiveType">Set</a><a id="28901" class="Symbol">}</a> <a id="28903" class="Symbol">(</a><a id="28904" href="plfa.part1.Lists.html#28904" class="Bound">P</a> <a id="28906" class="Symbol">:</a> <a id="28908" href="plfa.part1.Lists.html#28894" class="Bound">A</a> <a id="28910" class="Symbol">→</a> <a id="28912" class="PrimitiveType">Set</a><a id="28915" class="Symbol">)</a> <a id="28917" class="Symbol">:</a> <a id="28919" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="28924" href="plfa.part1.Lists.html#28894" class="Bound">A</a> <a id="28926" class="Symbol">→</a> <a id="28928" class="PrimitiveType">Set</a> <a id="28932" class="Keyword">where</a>
  <a id="All.[]"></a><a id="28940" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28940" class="InductiveConstructor">[]</a>  <a id="28944" class="Symbol">:</a> <a id="28946" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="28950" href="plfa.part1.Lists.html#28904" class="Bound">P</a> <a id="28952" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>
  <a id="All._∷_"></a><a id="28957" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28957" class="InductiveConstructor Operator">_∷_</a> <a id="28961" class="Symbol">:</a> <a id="28963" class="Symbol">∀</a> <a id="28965" class="Symbol">{</a><a id="28966" href="plfa.part1.Lists.html#28966" class="Bound">x</a> <a id="28968" class="Symbol">:</a> <a id="28970" href="plfa.part1.Lists.html#28894" class="Bound">A</a><a id="28971" class="Symbol">}</a> <a id="28973" class="Symbol">{</a><a id="28974" href="plfa.part1.Lists.html#28974" class="Bound">xs</a> <a id="28977" class="Symbol">:</a> <a id="28979" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="28984" href="plfa.part1.Lists.html#28894" class="Bound">A</a><a id="28985" class="Symbol">}</a> <a id="28987" class="Symbol">→</a> <a id="28989" href="plfa.part1.Lists.html#28904" class="Bound">P</a> <a id="28991" href="plfa.part1.Lists.html#28966" class="Bound">x</a> <a id="28993" class="Symbol">→</a> <a id="28995" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="28999" href="plfa.part1.Lists.html#28904" class="Bound">P</a> <a id="29001" href="plfa.part1.Lists.html#28974" class="Bound">xs</a> <a id="29004" class="Symbol">→</a> <a id="29006" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="29010" href="plfa.part1.Lists.html#28904" class="Bound">P</a> <a id="29012" class="Symbol">(</a><a id="29013" href="plfa.part1.Lists.html#28966" class="Bound">x</a> <a id="29015" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="29017" href="plfa.part1.Lists.html#28974" class="Bound">xs</a><a id="29019" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
The type has two constructors, reusing the names of the same constructors for lists.
The first asserts that `P` holds for every element of the empty list.
The second asserts that if `P` holds of the head of a list and for every
element of the tail of a list, then `P` holds for every element of the list.
Agda uses types to disambiguate whether the constructor is building
a list or evidence that `All P` holds.
{:/}

这个类型有两个构造子，使用了与列表构造子相同的名称。第一个断言了 `P` 对于空列表的任何元素成立。第二个断言了如果 `P` 对于列表的头元素和尾列表的所有元素成立，那么 `P` 对于这个列表的任何元素成立。
Agda 使用类型来区分构造子是用于构造一个列表，还是构造 `All P` 成立的证明。

{::comment}
For example, `All (_≤ 2)` holds of a list where every element is less
than or equal to two.  Recall that `z≤n` proves `zero ≤ n` for any
`n`, and that if `m≤n` proves `m ≤ n` then `s≤s m≤n` proves `suc m ≤
suc n`, for any `m` and `n`:
{:/}

比如说，`All (_≤ 2)` 对于一个每一个元素都小于等于二的列表成立。回忆 `z≤n` 证明了对于任意 `n`， `zero ≤ n` 成立；对于任意 `m` 和 `n`，如果 `m≤n` 证明了 `m ≤ n`，那么 `s≤s m≤n` 证明了 `suc m ≤
suc n`:

{% raw %}<pre class="Agda"><a id="30012" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#30012" class="Function">_</a> <a id="30014" class="Symbol">:</a> <a id="30016" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="30020" class="Symbol">(</a><a id="30021" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#895" class="Datatype Operator">_≤</a> <a id="30024" class="Number">2</a><a id="30025" class="Symbol">)</a> <a id="30027" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">[</a> <a id="30029" class="Number">0</a> <a id="30031" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="30033" class="Number">1</a> <a id="30035" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">,</a> <a id="30037" class="Number">2</a> <a id="30039" href="plfa.part1.Lists.html#3884" class="InductiveConstructor Operator">]</a>
<a id="30041" class="Symbol">_</a> <a id="30043" class="Symbol">=</a> <a id="30045" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#918" class="InductiveConstructor">z≤n</a> <a id="30049" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28957" class="InductiveConstructor Operator">∷</a> <a id="30051" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#960" class="InductiveConstructor">s≤s</a> <a id="30055" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#918" class="InductiveConstructor">z≤n</a> <a id="30059" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="30061" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#960" class="InductiveConstructor">s≤s</a> <a id="30065" class="Symbol">(</a><a id="30066" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#960" class="InductiveConstructor">s≤s</a> <a id="30070" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Base.html#918" class="InductiveConstructor">z≤n</a><a id="30073" class="Symbol">)</a> <a id="30075" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="30077" href="plfa.part1.Lists.html#28940" class="InductiveConstructor">[]</a>
</pre>{% endraw %}
{::comment}
Here `_∷_` and `[]` are the constructors of `All P` rather than of `List A`.
The three items are proofs of `0 ≤ 2`, `1 ≤ 2`, and `2 ≤ 2`, respectively.
{:/}

这里 `_∷_` 和 `[]` 是 `All P` 的构造子，而不是 `List A` 的。这三项分别是 `0 ≤ 2`、 `1 ≤ 2` 和 `2 ≤ 2` 的证明。

{::comment}
(One might wonder whether a pattern such as `[_,_,_]` can be used to
construct values of type `All` as well as type `List`, since both use
the same constructors. Indeed it can, so long as both types are in
scope when the pattern is declared.  That's not the case here, since
`List` is defined before `[_,_,_]`, but `All` is defined later.)
{:/}

（读者可能会思考诸如 `[_,_,_]` 的模式是否可以用于构造 `All` 类型的值，像构造 `List` 类型的一样，因为两者使用了相同的构造子。事实上这样做是可以的，只要两个类型在模式声明时在作用域内。然而现在不是这样的情况，因为 `List` 先于 `[_,_,_]` 定义，而 `All` 在之后定义。）

{::comment}
## Any
{:/}

## 任意

{::comment}
Predicate `Any P` holds if predicate `P` is satisfied by some element of a list:
{:/}

谓词 `Any P` 当列表里的一些元素满足 `P` 时成立：

{% raw %}<pre class="Agda"><a id="31030" class="Keyword">data</a> <a id="Any"></a><a id="31035" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31035" class="Datatype">Any</a> <a id="31039" class="Symbol">{</a><a id="31040" href="plfa.part1.Lists.html#31040" class="Bound">A</a> <a id="31042" class="Symbol">:</a> <a id="31044" class="PrimitiveType">Set</a><a id="31047" class="Symbol">}</a> <a id="31049" class="Symbol">(</a><a id="31050" href="plfa.part1.Lists.html#31050" class="Bound">P</a> <a id="31052" class="Symbol">:</a> <a id="31054" href="plfa.part1.Lists.html#31040" class="Bound">A</a> <a id="31056" class="Symbol">→</a> <a id="31058" class="PrimitiveType">Set</a><a id="31061" class="Symbol">)</a> <a id="31063" class="Symbol">:</a> <a id="31065" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="31070" href="plfa.part1.Lists.html#31040" class="Bound">A</a> <a id="31072" class="Symbol">→</a> <a id="31074" class="PrimitiveType">Set</a> <a id="31078" class="Keyword">where</a>
  <a id="Any.here"></a><a id="31086" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31086" class="InductiveConstructor">here</a>  <a id="31092" class="Symbol">:</a> <a id="31094" class="Symbol">∀</a> <a id="31096" class="Symbol">{</a><a id="31097" href="plfa.part1.Lists.html#31097" class="Bound">x</a> <a id="31099" class="Symbol">:</a> <a id="31101" href="plfa.part1.Lists.html#31040" class="Bound">A</a><a id="31102" class="Symbol">}</a> <a id="31104" class="Symbol">{</a><a id="31105" href="plfa.part1.Lists.html#31105" class="Bound">xs</a> <a id="31108" class="Symbol">:</a> <a id="31110" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="31115" href="plfa.part1.Lists.html#31040" class="Bound">A</a><a id="31116" class="Symbol">}</a> <a id="31118" class="Symbol">→</a> <a id="31120" href="plfa.part1.Lists.html#31050" class="Bound">P</a> <a id="31122" href="plfa.part1.Lists.html#31097" class="Bound">x</a> <a id="31124" class="Symbol">→</a> <a id="31126" href="plfa.part1.Lists.html#31035" class="Datatype">Any</a> <a id="31130" href="plfa.part1.Lists.html#31050" class="Bound">P</a> <a id="31132" class="Symbol">(</a><a id="31133" href="plfa.part1.Lists.html#31097" class="Bound">x</a> <a id="31135" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="31137" href="plfa.part1.Lists.html#31105" class="Bound">xs</a><a id="31139" class="Symbol">)</a>
  <a id="Any.there"></a><a id="31143" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31143" class="InductiveConstructor">there</a> <a id="31149" class="Symbol">:</a> <a id="31151" class="Symbol">∀</a> <a id="31153" class="Symbol">{</a><a id="31154" href="plfa.part1.Lists.html#31154" class="Bound">x</a> <a id="31156" class="Symbol">:</a> <a id="31158" href="plfa.part1.Lists.html#31040" class="Bound">A</a><a id="31159" class="Symbol">}</a> <a id="31161" class="Symbol">{</a><a id="31162" href="plfa.part1.Lists.html#31162" class="Bound">xs</a> <a id="31165" class="Symbol">:</a> <a id="31167" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="31172" href="plfa.part1.Lists.html#31040" class="Bound">A</a><a id="31173" class="Symbol">}</a> <a id="31175" class="Symbol">→</a> <a id="31177" href="plfa.part1.Lists.html#31035" class="Datatype">Any</a> <a id="31181" href="plfa.part1.Lists.html#31050" class="Bound">P</a> <a id="31183" href="plfa.part1.Lists.html#31162" class="Bound">xs</a> <a id="31186" class="Symbol">→</a> <a id="31188" href="plfa.part1.Lists.html#31035" class="Datatype">Any</a> <a id="31192" href="plfa.part1.Lists.html#31050" class="Bound">P</a> <a id="31194" class="Symbol">(</a><a id="31195" href="plfa.part1.Lists.html#31154" class="Bound">x</a> <a id="31197" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="31199" href="plfa.part1.Lists.html#31162" class="Bound">xs</a><a id="31201" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
The first constructor provides evidence that the head of the list
satisfies `P`, while the second provides evidence that some element of
the tail of the list satisfies `P`.  For example, we can define list
membership as follows:
{:/}

第一个构造子证明了列表的头元素满足 `P`，第二个构造子证明的列表的尾列表中的一些元素满足 `P`。举例来说，我们可以如下定义列表的成员关系：

{% raw %}<pre class="Agda"><a id="31533" class="Keyword">infix</a> <a id="31539" class="Number">4</a> <a id="31541" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31550" class="Function Operator">_∈_</a> <a id="31545" href="plfa.part1.Lists.html#31620" class="Function Operator">_∉_</a>

<a id="_∈_"></a><a id="31550" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31550" class="Function Operator">_∈_</a> <a id="31554" class="Symbol">:</a> <a id="31556" class="Symbol">∀</a> <a id="31558" class="Symbol">{</a><a id="31559" href="plfa.part1.Lists.html#31559" class="Bound">A</a> <a id="31561" class="Symbol">:</a> <a id="31563" class="PrimitiveType">Set</a><a id="31566" class="Symbol">}</a> <a id="31568" class="Symbol">(</a><a id="31569" href="plfa.part1.Lists.html#31569" class="Bound">x</a> <a id="31571" class="Symbol">:</a> <a id="31573" href="plfa.part1.Lists.html#31559" class="Bound">A</a><a id="31574" class="Symbol">)</a> <a id="31576" class="Symbol">(</a><a id="31577" href="plfa.part1.Lists.html#31577" class="Bound">xs</a> <a id="31580" class="Symbol">:</a> <a id="31582" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="31587" href="plfa.part1.Lists.html#31559" class="Bound">A</a><a id="31588" class="Symbol">)</a> <a id="31590" class="Symbol">→</a> <a id="31592" class="PrimitiveType">Set</a>
<a id="31596" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31596" class="Bound">x</a> <a id="31598" href="plfa.part1.Lists.html#31550" class="Function Operator">∈</a> <a id="31600" href="plfa.part1.Lists.html#31600" class="Bound">xs</a> <a id="31603" class="Symbol">=</a> <a id="31605" href="plfa.part1.Lists.html#31035" class="Datatype">Any</a> <a id="31609" class="Symbol">(</a><a id="31610" href="plfa.part1.Lists.html#31596" class="Bound">x</a> <a id="31612" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡_</a><a id="31614" class="Symbol">)</a> <a id="31616" href="plfa.part1.Lists.html#31600" class="Bound">xs</a>

<a id="_∉_"></a><a id="31620" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31620" class="Function Operator">_∉_</a> <a id="31624" class="Symbol">:</a> <a id="31626" class="Symbol">∀</a> <a id="31628" class="Symbol">{</a><a id="31629" href="plfa.part1.Lists.html#31629" class="Bound">A</a> <a id="31631" class="Symbol">:</a> <a id="31633" class="PrimitiveType">Set</a><a id="31636" class="Symbol">}</a> <a id="31638" class="Symbol">(</a><a id="31639" href="plfa.part1.Lists.html#31639" class="Bound">x</a> <a id="31641" class="Symbol">:</a> <a id="31643" href="plfa.part1.Lists.html#31629" class="Bound">A</a><a id="31644" class="Symbol">)</a> <a id="31646" class="Symbol">(</a><a id="31647" href="plfa.part1.Lists.html#31647" class="Bound">xs</a> <a id="31650" class="Symbol">:</a> <a id="31652" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="31657" href="plfa.part1.Lists.html#31629" class="Bound">A</a><a id="31658" class="Symbol">)</a> <a id="31660" class="Symbol">→</a> <a id="31662" class="PrimitiveType">Set</a>
<a id="31666" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31666" class="Bound">x</a> <a id="31668" href="plfa.part1.Lists.html#31620" class="Function Operator">∉</a> <a id="31670" href="plfa.part1.Lists.html#31670" class="Bound">xs</a> <a id="31673" class="Symbol">=</a> <a id="31675" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#535" class="Function Operator">¬</a> <a id="31677" class="Symbol">(</a><a id="31678" href="plfa.part1.Lists.html#31666" class="Bound">x</a> <a id="31680" href="plfa.part1.Lists.html#31550" class="Function Operator">∈</a> <a id="31682" href="plfa.part1.Lists.html#31670" class="Bound">xs</a><a id="31684" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
For example, zero is an element of the list `[ 0 , 1 , 0 , 2 ]`.  Indeed, we can demonstrate
this fact in two different ways, corresponding to the two different
occurrences of zero in the list, as the first element and as the third element:
{:/}

比如说，零是列表 `[ 0 , 1 , 0 , 2 ]` 中的一个元素。我们可以用两种方法来展示这个事实，对应零在列表中出现了两次：第一个元素和第三个元素：

{% raw %}<pre class="Agda"><a id="32035" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32035" class="Function">_</a> <a id="32037" class="Symbol">:</a> <a id="32039" class="Number">0</a> <a id="32041" href="plfa.part1.Lists.html#31550" class="Function Operator">∈</a> <a id="32043" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="32045" class="Number">0</a> <a id="32047" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32049" class="Number">1</a> <a id="32051" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32053" class="Number">0</a> <a id="32055" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32057" class="Number">2</a> <a id="32059" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
<a id="32061" class="Symbol">_</a> <a id="32063" class="Symbol">=</a> <a id="32065" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31086" class="InductiveConstructor">here</a> <a id="32070" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>

<a id="32076" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32076" class="Function">_</a> <a id="32078" class="Symbol">:</a> <a id="32080" class="Number">0</a> <a id="32082" href="plfa.part1.Lists.html#31550" class="Function Operator">∈</a> <a id="32084" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="32086" class="Number">0</a> <a id="32088" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32090" class="Number">1</a> <a id="32092" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32094" class="Number">0</a> <a id="32096" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32098" class="Number">2</a> <a id="32100" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
<a id="32102" class="Symbol">_</a> <a id="32104" class="Symbol">=</a> <a id="32106" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#31143" class="InductiveConstructor">there</a> <a id="32112" class="Symbol">(</a><a id="32113" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32119" class="Symbol">(</a><a id="32120" href="plfa.part1.Lists.html#31086" class="InductiveConstructor">here</a> <a id="32125" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a><a id="32129" class="Symbol">))</a>
</pre>{% endraw %}
{::comment}
Further, we can demonstrate that three is not in the list, because
any possible proof that it is in the list leads to contradiction:
{:/}

除此之外，我们可以展示三不在列表之中，因为任何它在列表中的证明会推导出矛盾：

{% raw %}<pre class="Agda"><a id="not-in"></a><a id="32332" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32339" class="Symbol">:</a> <a id="32341" class="Number">3</a> <a id="32343" href="plfa.part1.Lists.html#31620" class="Function Operator">∉</a> <a id="32345" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="32347" class="Number">0</a> <a id="32349" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32351" class="Number">1</a> <a id="32353" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32355" class="Number">0</a> <a id="32357" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="32359" class="Number">2</a> <a id="32361" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
<a id="32363" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32370" class="Symbol">(</a><a id="32371" href="plfa.part1.Lists.html#31086" class="InductiveConstructor">here</a> <a id="32376" class="Symbol">())</a>
<a id="32380" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32387" class="Symbol">(</a><a id="32388" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32394" class="Symbol">(</a><a id="32395" href="plfa.part1.Lists.html#31086" class="InductiveConstructor">here</a> <a id="32400" class="Symbol">()))</a>
<a id="32405" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32412" class="Symbol">(</a><a id="32413" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32419" class="Symbol">(</a><a id="32420" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32426" class="Symbol">(</a><a id="32427" href="plfa.part1.Lists.html#31086" class="InductiveConstructor">here</a> <a id="32432" class="Symbol">())))</a>
<a id="32438" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32445" class="Symbol">(</a><a id="32446" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32452" class="Symbol">(</a><a id="32453" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32459" class="Symbol">(</a><a id="32460" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32466" class="Symbol">(</a><a id="32467" href="plfa.part1.Lists.html#31086" class="InductiveConstructor">here</a> <a id="32472" class="Symbol">()))))</a>
<a id="32479" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#32332" class="Function">not-in</a> <a id="32486" class="Symbol">(</a><a id="32487" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32493" class="Symbol">(</a><a id="32494" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32500" class="Symbol">(</a><a id="32501" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32507" class="Symbol">(</a><a id="32508" href="plfa.part1.Lists.html#31143" class="InductiveConstructor">there</a> <a id="32514" class="Symbol">()))))</a>
</pre>{% endraw %}
{::comment}
The five occurrences of `()` attest to the fact that there is no
possible evidence for `3 ≡ 0`, `3 ≡ 1`, `3 ≡ 0`, `3 ≡ 2`, and
`3 ∈ []`, respectively.
{:/}

`()` 出现了五次，分别表示没有 `3 ≡ 0`、 `3 ≡ 1`、 `3 ≡ 0`、 `3 ≡ 2` 和
`3 ∈ []` 的证明。

{::comment}
## All and append
{:/}

## 所有和附加

{::comment}
A predicate holds for every element of one list appended to another if and
only if it holds for every element of both lists:
{:/}

一个谓词对两个附加在一起的列表的每个元素都成立，当且仅当这个谓词对两个列表的每个元素都成立：

{% raw %}<pre class="Agda"><a id="All-++-⇔"></a><a id="33006" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33006" class="Function">All-++-⇔</a> <a id="33015" class="Symbol">:</a> <a id="33017" class="Symbol">∀</a> <a id="33019" class="Symbol">{</a><a id="33020" href="plfa.part1.Lists.html#33020" class="Bound">A</a> <a id="33022" class="Symbol">:</a> <a id="33024" class="PrimitiveType">Set</a><a id="33027" class="Symbol">}</a> <a id="33029" class="Symbol">{</a><a id="33030" href="plfa.part1.Lists.html#33030" class="Bound">P</a> <a id="33032" class="Symbol">:</a> <a id="33034" href="plfa.part1.Lists.html#33020" class="Bound">A</a> <a id="33036" class="Symbol">→</a> <a id="33038" class="PrimitiveType">Set</a><a id="33041" class="Symbol">}</a> <a id="33043" class="Symbol">(</a><a id="33044" href="plfa.part1.Lists.html#33044" class="Bound">xs</a> <a id="33047" href="plfa.part1.Lists.html#33047" class="Bound">ys</a> <a id="33050" class="Symbol">:</a> <a id="33052" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="33057" href="plfa.part1.Lists.html#33020" class="Bound">A</a><a id="33058" class="Symbol">)</a> <a id="33060" class="Symbol">→</a>
  <a id="33064" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28889" class="Datatype">All</a> <a id="33068" href="plfa.part1.Lists.html#33030" class="Bound">P</a> <a id="33070" class="Symbol">(</a><a id="33071" href="plfa.part1.Lists.html#33044" class="Bound">xs</a> <a id="33074" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="33077" href="plfa.part1.Lists.html#33047" class="Bound">ys</a><a id="33079" class="Symbol">)</a> <a id="33081" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Isomorphism.md %}{% raw %}#15292" class="Record Operator">⇔</a> <a id="33083" class="Symbol">(</a><a id="33084" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33088" href="plfa.part1.Lists.html#33030" class="Bound">P</a> <a id="33090" href="plfa.part1.Lists.html#33044" class="Bound">xs</a> <a id="33093" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1162" class="Function Operator">×</a> <a id="33095" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33099" href="plfa.part1.Lists.html#33030" class="Bound">P</a> <a id="33101" href="plfa.part1.Lists.html#33047" class="Bound">ys</a><a id="33103" class="Symbol">)</a>
<a id="33105" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33006" class="Function">All-++-⇔</a> <a id="33114" href="plfa.part1.Lists.html#33114" class="Bound">xs</a> <a id="33117" href="plfa.part1.Lists.html#33117" class="Bound">ys</a> <a id="33120" class="Symbol">=</a>
  <a id="33124" class="Keyword">record</a>
    <a id="33135" class="Symbol">{</a> <a id="33137" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Isomorphism.md %}{% raw %}#15332" class="Field">to</a>       <a id="33146" class="Symbol">=</a>  <a id="33149" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33204" class="Function">to</a> <a id="33152" href="plfa.part1.Lists.html#33114" class="Bound">xs</a> <a id="33155" href="plfa.part1.Lists.html#33117" class="Bound">ys</a>
    <a id="33162" class="Symbol">;</a> <a id="33164" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Isomorphism.md %}{% raw %}#15349" class="Field">from</a>     <a id="33173" class="Symbol">=</a>  <a id="33176" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33429" class="Function">from</a> <a id="33181" href="plfa.part1.Lists.html#33114" class="Bound">xs</a> <a id="33184" href="plfa.part1.Lists.html#33117" class="Bound">ys</a>
    <a id="33191" class="Symbol">}</a>
  <a id="33195" class="Keyword">where</a>

  <a id="33204" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33204" class="Function">to</a> <a id="33207" class="Symbol">:</a> <a id="33209" class="Symbol">∀</a> <a id="33211" class="Symbol">{</a><a id="33212" href="plfa.part1.Lists.html#33212" class="Bound">A</a> <a id="33214" class="Symbol">:</a> <a id="33216" class="PrimitiveType">Set</a><a id="33219" class="Symbol">}</a> <a id="33221" class="Symbol">{</a><a id="33222" href="plfa.part1.Lists.html#33222" class="Bound">P</a> <a id="33224" class="Symbol">:</a> <a id="33226" href="plfa.part1.Lists.html#33212" class="Bound">A</a> <a id="33228" class="Symbol">→</a> <a id="33230" class="PrimitiveType">Set</a><a id="33233" class="Symbol">}</a> <a id="33235" class="Symbol">(</a><a id="33236" href="plfa.part1.Lists.html#33236" class="Bound">xs</a> <a id="33239" href="plfa.part1.Lists.html#33239" class="Bound">ys</a> <a id="33242" class="Symbol">:</a> <a id="33244" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="33249" href="plfa.part1.Lists.html#33212" class="Bound">A</a><a id="33250" class="Symbol">)</a> <a id="33252" class="Symbol">→</a>
    <a id="33258" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28889" class="Datatype">All</a> <a id="33262" href="plfa.part1.Lists.html#33222" class="Bound">P</a> <a id="33264" class="Symbol">(</a><a id="33265" href="plfa.part1.Lists.html#33236" class="Bound">xs</a> <a id="33268" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="33271" href="plfa.part1.Lists.html#33239" class="Bound">ys</a><a id="33273" class="Symbol">)</a> <a id="33275" class="Symbol">→</a> <a id="33277" class="Symbol">(</a><a id="33278" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33282" href="plfa.part1.Lists.html#33222" class="Bound">P</a> <a id="33284" href="plfa.part1.Lists.html#33236" class="Bound">xs</a> <a id="33287" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1162" class="Function Operator">×</a> <a id="33289" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33293" href="plfa.part1.Lists.html#33222" class="Bound">P</a> <a id="33295" href="plfa.part1.Lists.html#33239" class="Bound">ys</a><a id="33297" class="Symbol">)</a>
  <a id="33301" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33204" class="Function">to</a> <a id="33304" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="33307" href="plfa.part1.Lists.html#33307" class="Bound">ys</a> <a id="33310" href="plfa.part1.Lists.html#33310" class="Bound">Pys</a> <a id="33314" class="Symbol">=</a> <a id="33316" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33318" href="plfa.part1.Lists.html#28940" class="InductiveConstructor">[]</a> <a id="33321" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33323" href="plfa.part1.Lists.html#33310" class="Bound">Pys</a> <a id="33327" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a>
  <a id="33331" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33204" class="Function">to</a> <a id="33334" class="Symbol">(</a><a id="33335" href="plfa.part1.Lists.html#33335" class="Bound">x</a> <a id="33337" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="33339" href="plfa.part1.Lists.html#33339" class="Bound">xs</a><a id="33341" class="Symbol">)</a> <a id="33343" href="plfa.part1.Lists.html#33343" class="Bound">ys</a> <a id="33346" class="Symbol">(</a><a id="33347" href="plfa.part1.Lists.html#33347" class="Bound">Px</a> <a id="33350" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="33352" href="plfa.part1.Lists.html#33352" class="Bound">Pxs++ys</a><a id="33359" class="Symbol">)</a> <a id="33361" class="Keyword">with</a> <a id="33366" href="plfa.part1.Lists.html#33204" class="Function">to</a> <a id="33369" href="plfa.part1.Lists.html#33339" class="Bound">xs</a> <a id="33372" href="plfa.part1.Lists.html#33343" class="Bound">ys</a> <a id="33375" href="plfa.part1.Lists.html#33352" class="Bound">Pxs++ys</a>
  <a id="33385" class="Symbol">...</a> <a id="33389" class="Symbol">|</a> <a id="33391" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33393" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33393" class="Bound">Pxs</a> <a id="33397" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33399" href="plfa.part1.Lists.html#33399" class="Bound">Pys</a> <a id="33403" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a> <a id="33405" class="Symbol">=</a> <a id="33407" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33409" class="Bound">Px</a> <a id="33412" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="33414" href="plfa.part1.Lists.html#33393" class="Bound">Pxs</a> <a id="33418" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33420" href="plfa.part1.Lists.html#33399" class="Bound">Pys</a> <a id="33424" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a>

  <a id="33429" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33429" class="Function">from</a> <a id="33434" class="Symbol">:</a> <a id="33436" class="Symbol">∀</a> <a id="33438" class="Symbol">{</a> <a id="33440" href="plfa.part1.Lists.html#33440" class="Bound">A</a> <a id="33442" class="Symbol">:</a> <a id="33444" class="PrimitiveType">Set</a><a id="33447" class="Symbol">}</a> <a id="33449" class="Symbol">{</a><a id="33450" href="plfa.part1.Lists.html#33450" class="Bound">P</a> <a id="33452" class="Symbol">:</a> <a id="33454" href="plfa.part1.Lists.html#33440" class="Bound">A</a> <a id="33456" class="Symbol">→</a> <a id="33458" class="PrimitiveType">Set</a><a id="33461" class="Symbol">}</a> <a id="33463" class="Symbol">(</a><a id="33464" href="plfa.part1.Lists.html#33464" class="Bound">xs</a> <a id="33467" href="plfa.part1.Lists.html#33467" class="Bound">ys</a> <a id="33470" class="Symbol">:</a> <a id="33472" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="33477" href="plfa.part1.Lists.html#33440" class="Bound">A</a><a id="33478" class="Symbol">)</a> <a id="33480" class="Symbol">→</a>
    <a id="33486" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#28889" class="Datatype">All</a> <a id="33490" href="plfa.part1.Lists.html#33450" class="Bound">P</a> <a id="33492" href="plfa.part1.Lists.html#33464" class="Bound">xs</a> <a id="33495" href="https://agda.github.io/agda-stdlib/v1.1/Data.Product.html#1162" class="Function Operator">×</a> <a id="33497" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33501" href="plfa.part1.Lists.html#33450" class="Bound">P</a> <a id="33503" href="plfa.part1.Lists.html#33467" class="Bound">ys</a> <a id="33506" class="Symbol">→</a> <a id="33508" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="33512" href="plfa.part1.Lists.html#33450" class="Bound">P</a> <a id="33514" class="Symbol">(</a><a id="33515" href="plfa.part1.Lists.html#33464" class="Bound">xs</a> <a id="33518" href="plfa.part1.Lists.html#4663" class="Function Operator">++</a> <a id="33521" href="plfa.part1.Lists.html#33467" class="Bound">ys</a><a id="33523" class="Symbol">)</a>
  <a id="33527" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33429" class="Function">from</a> <a id="33532" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="33535" href="plfa.part1.Lists.html#33535" class="Bound">ys</a> <a id="33538" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33540" href="plfa.part1.Lists.html#28940" class="InductiveConstructor">[]</a> <a id="33543" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33545" href="plfa.part1.Lists.html#33545" class="Bound">Pys</a> <a id="33549" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a> <a id="33551" class="Symbol">=</a> <a id="33553" href="plfa.part1.Lists.html#33545" class="Bound">Pys</a>
  <a id="33559" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#33429" class="Function">from</a> <a id="33564" class="Symbol">(</a><a id="33565" href="plfa.part1.Lists.html#33565" class="Bound">x</a> <a id="33567" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="33569" href="plfa.part1.Lists.html#33569" class="Bound">xs</a><a id="33571" class="Symbol">)</a> <a id="33573" href="plfa.part1.Lists.html#33573" class="Bound">ys</a> <a id="33576" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33578" href="plfa.part1.Lists.html#33578" class="Bound">Px</a> <a id="33581" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="33583" href="plfa.part1.Lists.html#33583" class="Bound">Pxs</a> <a id="33587" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33589" href="plfa.part1.Lists.html#33589" class="Bound">Pys</a> <a id="33593" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a> <a id="33595" class="Symbol">=</a>  <a id="33598" href="plfa.part1.Lists.html#33578" class="Bound">Px</a> <a id="33601" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="33603" href="plfa.part1.Lists.html#33429" class="Function">from</a> <a id="33608" href="plfa.part1.Lists.html#33569" class="Bound">xs</a> <a id="33611" href="plfa.part1.Lists.html#33573" class="Bound">ys</a> <a id="33614" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟨</a> <a id="33616" href="plfa.part1.Lists.html#33583" class="Bound">Pxs</a> <a id="33620" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">,</a> <a id="33622" href="plfa.part1.Lists.html#33589" class="Bound">Pys</a> <a id="33626" href="Agda.Builtin.Sigma.html#209" class="InductiveConstructor Operator">⟩</a>
</pre>{% endraw %}
{::comment}
#### Exercise `Any-++-⇔` (recommended)
{:/}

#### 练习 `Any-++-⇔` （推荐）

{::comment}
Prove a result similar to `All-++-⇔`, but with `Any` in place of `All`, and a suitable
replacement for `_×_`.  As a consequence, demonstrate an equivalence relating
`_∈_` and `_++_`.
{:/}
使用 `Any` 代替 `All` 与一个合适的 `_×_` 的替代，证明一个类似于 `All-++-⇔` 的结果。作为结论，展示关联 `_∈_` 和 `_++_` 的一个等价关系。

{::comment}
{% raw %}<pre class="Agda"><a id="34025" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="34062" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `All-++-≃` (stretch)
{:/}

#### 练习 `All-++-≃` （延伸）

{::comment}
Show that the equivalence `All-++-⇔` can be extended to an isomorphism.
{:/}

证明 `All-++-⇔` 的等价关系可以被扩展至一个同构关系。

{::comment}
{% raw %}<pre class="Agda"><a id="34298" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="34335" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `¬Any⇔All¬` (recommended)
{:/}

#### 练习 `¬Any⇔All¬`（推荐）

{::comment}
Show that `Any` and `All` satisfy a version of De Morgan's Law:
{:/}

请证明 `Any` 和 `All` 满足一个版本的德摩根定律：

    (¬_ ∘ Any P) xs ⇔ All (¬_ ∘ P) xs

{::comment}
(Can you see why it is important that here `_∘_` is generalised
to arbitrary levels, as described in the section on
[universe polymorphism]({{ site.baseurl }}/Equality/#unipoly)?)
{:/}

（你能明白为什么这里的 `_∘_` 被泛化到任意层级很重要吗？如[全体多态]({{ site.baseurl }}/Equality/#unipoly)一节所述。）

{::comment}
Do we also have the following?
{:/}

以下定律是否也成立？

    (¬_ ∘ All P) xs ⇔ Any (¬_ ∘ P) xs

{::comment}
If so, prove; if not, explain why.
{:/}

若成立，请证明；否则请解释原因。


{% raw %}<pre class="Agda"><a id="35049" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
{::comment}
#### Exercise `¬Any≃All¬` (stretch)
{:/}

#### 练习 `¬Any≃All¬`（拓展）

{::comment}
Show that the equivalence `¬Any⇔All¬` can be extended to an isomorphism.
You will need to use extensionality.
{:/}

请证明等价的 `¬Any⇔All¬` 可以被扩展成一个同构。你需要使用外延性。

{::comment}
{% raw %}<pre class="Agda"><a id="35342" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="35379" class="Comment">-- 请将代码写在此处</a>
</pre>{% endraw %}
{::comment}
#### Exercise `All-∀` (practice)
{:/}

#### 练习 `All-∀`（实践）

{::comment}
Show that `All P xs` is isomorphic to `∀ {x} → x ∈ xs → P x`.
{:/}

请证明 `All P xs` 同构于 `∀ {x} → x ∈ xs → P x`.

{::comment}
{% raw %}<pre class="Agda"><a id="35608" class="Comment">-- You code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="35644" class="Comment">-- 请将代码写在此处</a>
</pre>{% endraw %}

{::comment}
#### Exercise `Any-∃` (practice)
{:/}

#### 练习 `Any-∃`（实践）

{::comment}
Show that `Any P xs` is isomorphic to `∃[ x ] (x ∈ xs × P x)`.
{:/}

请证明 `Any P xs` 同构于 `∃[ x ] (x ∈ xs × P x)`.

{::comment}
{% raw %}<pre class="Agda"><a id="35876" class="Comment">-- You code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="35912" class="Comment">-- 请将代码写在此处</a>
</pre>{% endraw %}
{::comment}
If so, prove; if not, explain why.
{:/}

如果成立，请证明；如果不成立，请解释原因。


{::comment}
## Decidability of All
{:/}

## 所有的可判定性

{::comment}
If we consider a predicate as a function that yields a boolean,
it is easy to define an analogue of `All`, which returns true if
a given predicate returns true for every element of a list:
{:/}

如果我们将一个谓词看作一个返回布尔值的函数，那么我们可以简单的定义一个类似于 `All`
的函数，其当给定谓词对于列表每个元素返回真时返回真：

{% raw %}<pre class="Agda"><a id="all"></a><a id="36343" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#36343" class="Function">all</a> <a id="36347" class="Symbol">:</a> <a id="36349" class="Symbol">∀</a> <a id="36351" class="Symbol">{</a><a id="36352" href="plfa.part1.Lists.html#36352" class="Bound">A</a> <a id="36354" class="Symbol">:</a> <a id="36356" class="PrimitiveType">Set</a><a id="36359" class="Symbol">}</a> <a id="36361" class="Symbol">→</a> <a id="36363" class="Symbol">(</a><a id="36364" href="plfa.part1.Lists.html#36352" class="Bound">A</a> <a id="36366" class="Symbol">→</a> <a id="36368" href="Agda.Builtin.Bool.html#135" class="Datatype">Bool</a><a id="36372" class="Symbol">)</a> <a id="36374" class="Symbol">→</a> <a id="36376" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="36381" href="plfa.part1.Lists.html#36352" class="Bound">A</a> <a id="36383" class="Symbol">→</a> <a id="36385" href="Agda.Builtin.Bool.html#135" class="Datatype">Bool</a>
<a id="36390" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#36343" class="Function">all</a> <a id="36394" href="plfa.part1.Lists.html#36394" class="Bound">p</a>  <a id="36397" class="Symbol">=</a>  <a id="36400" href="plfa.part1.Lists.html#20421" class="Function">foldr</a> <a id="36406" href="https://agda.github.io/agda-stdlib/v1.1/Data.Bool.Base.html#1015" class="Function Operator">_∧_</a> <a id="36410" href="Agda.Builtin.Bool.html#160" class="InductiveConstructor">true</a> <a id="36415" href="https://agda.github.io/agda-stdlib/v1.1/Function.html#1099" class="Function Operator">∘</a> <a id="36417" href="plfa.part1.Lists.html#17187" class="Function">map</a> <a id="36421" href="plfa.part1.Lists.html#36394" class="Bound">p</a>
</pre>{% endraw %}
{::comment}
The function can be written in a particularly compact style by
using the higher-order functions `map` and `foldr`.
{:/}

我们可以使用高阶函数 `map` 和 `foldr` 来简洁地写出这个函数。

{::comment}
As one would hope, if we replace booleans by decidables there is again
an analogue of `All`.  First, return to the notion of a predicate `P` as
a function of type `A → Set`, taking a value `x` of type `A` into evidence
`P x` that a property holds for `x`.  Say that a predicate `P` is _decidable_
if we have a function that for a given `x` can decide `P x`:
{:/}

正如所希望的那样，如果我们将布尔值替换成可判定值，这与 `All` 是相似的。首先，回到将 `P`
当作一个类型为 `A → Set` 的函数的概念，将一个类型为 `A` 的值 `x` 转换成 `P x` 对 `x` 成立的证明。我们成 `P` 为**可判定的**（Decidable），如果我们有一个函数，其在给定 `x` 时能够判定 `P x`：

{% raw %}<pre class="Agda"><a id="Decidable"></a><a id="37159" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37159" class="Function">Decidable</a> <a id="37169" class="Symbol">:</a> <a id="37171" class="Symbol">∀</a> <a id="37173" class="Symbol">{</a><a id="37174" href="plfa.part1.Lists.html#37174" class="Bound">A</a> <a id="37176" class="Symbol">:</a> <a id="37178" class="PrimitiveType">Set</a><a id="37181" class="Symbol">}</a> <a id="37183" class="Symbol">→</a> <a id="37185" class="Symbol">(</a><a id="37186" href="plfa.part1.Lists.html#37174" class="Bound">A</a> <a id="37188" class="Symbol">→</a> <a id="37190" class="PrimitiveType">Set</a><a id="37193" class="Symbol">)</a> <a id="37195" class="Symbol">→</a> <a id="37197" class="PrimitiveType">Set</a>
<a id="37201" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37159" class="Function">Decidable</a> <a id="37211" class="Symbol">{</a><a id="37212" href="plfa.part1.Lists.html#37212" class="Bound">A</a><a id="37213" class="Symbol">}</a> <a id="37215" href="plfa.part1.Lists.html#37215" class="Bound">P</a>  <a id="37218" class="Symbol">=</a>  <a id="37221" class="Symbol">∀</a> <a id="37223" class="Symbol">(</a><a id="37224" href="plfa.part1.Lists.html#37224" class="Bound">x</a> <a id="37226" class="Symbol">:</a> <a id="37228" href="plfa.part1.Lists.html#37212" class="Bound">A</a><a id="37229" class="Symbol">)</a> <a id="37231" class="Symbol">→</a> <a id="37233" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#605" class="Datatype">Dec</a> <a id="37237" class="Symbol">(</a><a id="37238" href="plfa.part1.Lists.html#37215" class="Bound">P</a> <a id="37240" href="plfa.part1.Lists.html#37224" class="Bound">x</a><a id="37241" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
Then if predicate `P` is decidable, it is also decidable whether every
element of a list satisfies the predicate:
{:/}

那么当谓词 `P` 可判定时，我们亦可判定列表中的每一个元素是否满足这个谓词：
{% raw %}<pre class="Agda"><a id="All?"></a><a id="37424" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37424" class="Function">All?</a> <a id="37429" class="Symbol">:</a> <a id="37431" class="Symbol">∀</a> <a id="37433" class="Symbol">{</a><a id="37434" href="plfa.part1.Lists.html#37434" class="Bound">A</a> <a id="37436" class="Symbol">:</a> <a id="37438" class="PrimitiveType">Set</a><a id="37441" class="Symbol">}</a> <a id="37443" class="Symbol">{</a><a id="37444" href="plfa.part1.Lists.html#37444" class="Bound">P</a> <a id="37446" class="Symbol">:</a> <a id="37448" href="plfa.part1.Lists.html#37434" class="Bound">A</a> <a id="37450" class="Symbol">→</a> <a id="37452" class="PrimitiveType">Set</a><a id="37455" class="Symbol">}</a> <a id="37457" class="Symbol">→</a> <a id="37459" href="plfa.part1.Lists.html#37159" class="Function">Decidable</a> <a id="37469" href="plfa.part1.Lists.html#37444" class="Bound">P</a> <a id="37471" class="Symbol">→</a> <a id="37473" href="plfa.part1.Lists.html#37159" class="Function">Decidable</a> <a id="37483" class="Symbol">(</a><a id="37484" href="plfa.part1.Lists.html#28889" class="Datatype">All</a> <a id="37488" href="plfa.part1.Lists.html#37444" class="Bound">P</a><a id="37489" class="Symbol">)</a>
<a id="37491" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37424" class="Function">All?</a> <a id="37496" href="plfa.part1.Lists.html#37496" class="Bound">P?</a> <a id="37499" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>                                 <a id="37534" class="Symbol">=</a>  <a id="37537" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#641" class="InductiveConstructor">yes</a> <a id="37541" href="plfa.part1.Lists.html#28940" class="InductiveConstructor">[]</a>
<a id="37544" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37424" class="Function">All?</a> <a id="37549" href="plfa.part1.Lists.html#37549" class="Bound">P?</a> <a id="37552" class="Symbol">(</a><a id="37553" href="plfa.part1.Lists.html#37553" class="Bound">x</a> <a id="37555" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="37557" href="plfa.part1.Lists.html#37557" class="Bound">xs</a><a id="37559" class="Symbol">)</a> <a id="37561" class="Keyword">with</a> <a id="37566" href="plfa.part1.Lists.html#37549" class="Bound">P?</a> <a id="37569" href="plfa.part1.Lists.html#37553" class="Bound">x</a>   <a id="37573" class="Symbol">|</a> <a id="37575" href="plfa.part1.Lists.html#37424" class="Function">All?</a> <a id="37580" href="plfa.part1.Lists.html#37549" class="Bound">P?</a> <a id="37583" href="plfa.part1.Lists.html#37557" class="Bound">xs</a>
<a id="37586" class="Symbol">...</a>                 <a id="37606" class="Symbol">|</a> <a id="37608" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#641" class="InductiveConstructor">yes</a> <a id="37612" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37612" class="Bound">Px</a> <a id="37615" class="Symbol">|</a> <a id="37617" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#641" class="InductiveConstructor">yes</a> <a id="37621" href="plfa.part1.Lists.html#37621" class="Bound">Pxs</a>     <a id="37629" class="Symbol">=</a>  <a id="37632" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#641" class="InductiveConstructor">yes</a> <a id="37636" class="Symbol">(</a><a id="37637" href="plfa.part1.Lists.html#37612" class="Bound">Px</a> <a id="37640" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="37642" href="plfa.part1.Lists.html#37621" class="Bound">Pxs</a><a id="37645" class="Symbol">)</a>
<a id="37647" class="Symbol">...</a>                 <a id="37667" class="Symbol">|</a> <a id="37669" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#668" class="InductiveConstructor">no</a> <a id="37672" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37672" class="Bound">¬Px</a> <a id="37676" class="Symbol">|</a> <a id="37678" class="Symbol">_</a>           <a id="37690" class="Symbol">=</a>  <a id="37693" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#668" class="InductiveConstructor">no</a> <a id="37696" class="Symbol">λ{</a> <a id="37699" class="Symbol">(</a><a id="37700" href="plfa.part1.Lists.html#37700" class="Bound">Px</a> <a id="37703" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="37705" href="plfa.part1.Lists.html#37705" class="Bound">Pxs</a><a id="37708" class="Symbol">)</a> <a id="37710" class="Symbol">→</a> <a id="37712" href="plfa.part1.Lists.html#37672" class="Bound">¬Px</a> <a id="37716" href="plfa.part1.Lists.html#37700" class="Bound">Px</a>   <a id="37721" class="Symbol">}</a>
<a id="37723" class="CatchallClause Symbol">...</a><a id="37726" class="CatchallClause">                 </a><a id="37743" class="CatchallClause Symbol">|</a><a id="37744" class="CatchallClause"> </a><a id="37745" class="CatchallClause Symbol">_</a><a id="37746" class="CatchallClause">      </a><a id="37752" class="CatchallClause Symbol">|</a><a id="37753" class="CatchallClause"> </a><a id="37754" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#668" class="CatchallClause InductiveConstructor">no</a><a id="37756" class="CatchallClause"> </a><a id="37757" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#37757" class="CatchallClause Bound">¬Pxs</a>     <a id="37766" class="Symbol">=</a>  <a id="37769" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Nullary.html#668" class="InductiveConstructor">no</a> <a id="37772" class="Symbol">λ{</a> <a id="37775" class="Symbol">(</a><a id="37776" href="plfa.part1.Lists.html#37776" class="Bound">Px</a> <a id="37779" href="plfa.part1.Lists.html#28957" class="InductiveConstructor Operator">∷</a> <a id="37781" href="plfa.part1.Lists.html#37781" class="Bound">Pxs</a><a id="37784" class="Symbol">)</a> <a id="37786" class="Symbol">→</a> <a id="37788" href="plfa.part1.Lists.html#37757" class="Bound">¬Pxs</a> <a id="37793" href="plfa.part1.Lists.html#37781" class="Bound">Pxs</a> <a id="37797" class="Symbol">}</a>
</pre>{% endraw %}
{::comment}
If the list is empty, then trivially `P` holds for every element of
the list.  Otherwise, the structure of the proof is similar to that
showing that the conjunction of two decidable propositions is itself
decidable, using `_∷_` rather than `⟨_,_⟩` to combine the evidence for
the head and tail of the list.
{:/}

如果列表为空，那么 `P` 显然对列表的每个元素成立。否则，证明的结构与两个可判定的命题是可判定的证明相似，不过我们使用 `_∷_` 而不是 `⟨_,_⟩`
来整合头元素和尾列表的证明。

{::comment}
#### Exercise `Any?` (stretch)
{:/}

#### 练习 `Any?`（扩展）

{::comment}
Just as `All` has analogues `all` and `All?` which determine whether a
predicate holds for every element of a list, so does `Any` have
analogues `any` and `Any?` which determine whether a predicate holds
for some element of a list.  Give their definitions.
{:/}

正如 `All` 有类似的 `all` 和 `All?` 形式，来判断列表的每个元素是否满足给定的谓词，那么 `Any` 也有类似的 `any` 和 `Any?` 形式，来判断列表的一些元素是否满足给定的谓词。给出它们的定义。

{::comment}
{% raw %}<pre class="Agda"><a id="38702" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="38739" class="Comment">-- 请将代码写在此处。</a>
</pre>{% endraw %}
{::comment}
#### Exercise `split` (stretch)
{:/}

#### 练习 `split`（扩展）

{::comment}
The relation `merge` holds when two lists merge to give a third list.
{:/}

关系 `merge` 在两个列表合并的结果为给定的第三个列表时成立。

{% raw %}<pre class="Agda"><a id="38956" class="Keyword">data</a> <a id="merge"></a><a id="38961" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="38967" class="Symbol">{</a><a id="38968" href="plfa.part1.Lists.html#38968" class="Bound">A</a> <a id="38970" class="Symbol">:</a> <a id="38972" class="PrimitiveType">Set</a><a id="38975" class="Symbol">}</a> <a id="38977" class="Symbol">:</a> <a id="38979" class="Symbol">(</a><a id="38980" href="plfa.part1.Lists.html#38980" class="Bound">xs</a> <a id="38983" href="plfa.part1.Lists.html#38983" class="Bound">ys</a> <a id="38986" href="plfa.part1.Lists.html#38986" class="Bound">zs</a> <a id="38989" class="Symbol">:</a> <a id="38991" href="plfa.part1.Lists.html#1296" class="Datatype">List</a> <a id="38996" href="plfa.part1.Lists.html#38968" class="Bound">A</a><a id="38997" class="Symbol">)</a> <a id="38999" class="Symbol">→</a> <a id="39001" class="PrimitiveType">Set</a> <a id="39005" class="Keyword">where</a>

  <a id="merge.[]"></a><a id="39014" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#39014" class="InductiveConstructor">[]</a> <a id="39017" class="Symbol">:</a>
      <a id="39025" class="Comment">--------------</a>
      <a id="39046" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="39052" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="39055" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a> <a id="39058" href="plfa.part1.Lists.html#1325" class="InductiveConstructor">[]</a>

  <a id="merge.left-∷"></a><a id="39064" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#39064" class="InductiveConstructor">left-∷</a> <a id="39071" class="Symbol">:</a> <a id="39073" class="Symbol">∀</a> <a id="39075" class="Symbol">{</a><a id="39076" href="plfa.part1.Lists.html#39076" class="Bound">x</a> <a id="39078" href="plfa.part1.Lists.html#39078" class="Bound">xs</a> <a id="39081" href="plfa.part1.Lists.html#39081" class="Bound">ys</a> <a id="39084" href="plfa.part1.Lists.html#39084" class="Bound">zs</a><a id="39086" class="Symbol">}</a>
    <a id="39092" class="Symbol">→</a> <a id="39094" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="39100" href="plfa.part1.Lists.html#39078" class="Bound">xs</a> <a id="39103" href="plfa.part1.Lists.html#39081" class="Bound">ys</a> <a id="39106" href="plfa.part1.Lists.html#39084" class="Bound">zs</a>
      <a id="39115" class="Comment">--------------------------</a>
    <a id="39146" class="Symbol">→</a> <a id="39148" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="39154" class="Symbol">(</a><a id="39155" href="plfa.part1.Lists.html#39076" class="Bound">x</a> <a id="39157" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="39159" href="plfa.part1.Lists.html#39078" class="Bound">xs</a><a id="39161" class="Symbol">)</a> <a id="39163" href="plfa.part1.Lists.html#39081" class="Bound">ys</a> <a id="39166" class="Symbol">(</a><a id="39167" href="plfa.part1.Lists.html#39076" class="Bound">x</a> <a id="39169" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="39171" href="plfa.part1.Lists.html#39084" class="Bound">zs</a><a id="39173" class="Symbol">)</a>

  <a id="merge.right-∷"></a><a id="39178" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#39178" class="InductiveConstructor">right-∷</a> <a id="39186" class="Symbol">:</a> <a id="39188" class="Symbol">∀</a> <a id="39190" class="Symbol">{</a><a id="39191" href="plfa.part1.Lists.html#39191" class="Bound">y</a> <a id="39193" href="plfa.part1.Lists.html#39193" class="Bound">xs</a> <a id="39196" href="plfa.part1.Lists.html#39196" class="Bound">ys</a> <a id="39199" href="plfa.part1.Lists.html#39199" class="Bound">zs</a><a id="39201" class="Symbol">}</a>
    <a id="39207" class="Symbol">→</a> <a id="39209" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="39215" href="plfa.part1.Lists.html#39193" class="Bound">xs</a> <a id="39218" href="plfa.part1.Lists.html#39196" class="Bound">ys</a> <a id="39221" href="plfa.part1.Lists.html#39199" class="Bound">zs</a>
      <a id="39230" class="Comment">--------------------------</a>
    <a id="39261" class="Symbol">→</a> <a id="39263" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#38961" class="Datatype">merge</a> <a id="39269" href="plfa.part1.Lists.html#39193" class="Bound">xs</a> <a id="39272" class="Symbol">(</a><a id="39273" href="plfa.part1.Lists.html#39191" class="Bound">y</a> <a id="39275" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="39277" href="plfa.part1.Lists.html#39196" class="Bound">ys</a><a id="39279" class="Symbol">)</a> <a id="39281" class="Symbol">(</a><a id="39282" href="plfa.part1.Lists.html#39191" class="Bound">y</a> <a id="39284" href="plfa.part1.Lists.html#1340" class="InductiveConstructor Operator">∷</a> <a id="39286" href="plfa.part1.Lists.html#39199" class="Bound">zs</a><a id="39288" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
For example,
{:/}

例如

{% raw %}<pre class="Agda"><a id="39334" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#39334" class="Function">_</a> <a id="39336" class="Symbol">:</a> <a id="39338" href="plfa.part1.Lists.html#38961" class="Datatype">merge</a> <a id="39344" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">[</a> <a id="39346" class="Number">1</a> <a id="39348" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">,</a> <a id="39350" class="Number">4</a> <a id="39352" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">]</a> <a id="39354" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">[</a> <a id="39356" class="Number">2</a> <a id="39358" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">,</a> <a id="39360" class="Number">3</a> <a id="39362" href="plfa.part1.Lists.html#3853" class="InductiveConstructor Operator">]</a> <a id="39364" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">[</a> <a id="39366" class="Number">1</a> <a id="39368" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="39370" class="Number">2</a> <a id="39372" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="39374" class="Number">3</a> <a id="39376" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">,</a> <a id="39378" class="Number">4</a> <a id="39380" href="plfa.part1.Lists.html#3923" class="InductiveConstructor Operator">]</a>
<a id="39382" class="Symbol">_</a> <a id="39384" class="Symbol">=</a> <a id="39386" href="{% endraw %}{{ site.baseurl }}{% link out/plfa/part1/Lists.md %}{% raw %}#39064" class="InductiveConstructor">left-∷</a> <a id="39393" class="Symbol">(</a><a id="39394" href="plfa.part1.Lists.html#39178" class="InductiveConstructor">right-∷</a> <a id="39402" class="Symbol">(</a><a id="39403" href="plfa.part1.Lists.html#39178" class="InductiveConstructor">right-∷</a> <a id="39411" class="Symbol">(</a><a id="39412" href="plfa.part1.Lists.html#39064" class="InductiveConstructor">left-∷</a> <a id="39419" href="plfa.part1.Lists.html#39014" class="InductiveConstructor">[]</a><a id="39421" class="Symbol">)))</a>
</pre>{% endraw %}
{::comment}
Given a decidable predicate and a list, we can split the list
into two lists that merge to give the original list, where all
elements of one list satisfy the predicate, and all elements of
the other do not satisfy the predicate.
{:/}

给定一个可判定谓词和一个列表，我们可以将该列表拆分成两个列表，二者可以合并成原列表，其中一个列表的所有元素都满足该谓词，而另一个列表中的所有元素都不满足该谓词。

{::comment}
Define the following variant of the traditional `filter` function on
lists, which given a decidable predicate and a list returns a list of
elements that satisfy the predicate and a list of elements that don't,
with their corresponding proofs.
{:/}

在列表上定义一个传统 `filter` 函数的变体，如下所示，它接受一个可判定谓词和一个列表，返回一个所有元素都满足该谓词的列表，和一个所有元素都不满足的列表，以及与它们相应的证明。

    split : ∀ {A : Set} {P : A → Set} (P? : Decidable P) (zs : List A)
      → ∃[ xs ] ∃[ ys ] ( merge xs ys zs × All P xs × All (¬_ ∘ P) ys )

{::comment}
{% raw %}<pre class="Agda"><a id="40277" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}{:/}

{% raw %}<pre class="Agda"><a id="40314" class="Comment">-- 请将代码写在此处</a>
</pre>{% endraw %}

{::comment}
## Standard Library
{:/}

## 标准库

{::comment}
Definitions similar to those in this chapter can be found in the standard library:
{:/}

标准库中可以找到与本章节中相似的定义：

{% raw %}<pre class="Agda"><a id="40504" class="Keyword">import</a> <a id="40511" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.html" class="Module">Data.List</a> <a id="40521" class="Keyword">using</a> <a id="40527" class="Symbol">(</a><a id="40528" href="Agda.Builtin.List.html#121" class="Datatype">List</a><a id="40532" class="Symbol">;</a> <a id="40534" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#1570" class="Function Operator">_++_</a><a id="40538" class="Symbol">;</a> <a id="40540" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#4104" class="Function">length</a><a id="40546" class="Symbol">;</a> <a id="40548" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#8564" class="Function">reverse</a><a id="40555" class="Symbol">;</a> <a id="40557" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#1304" class="Function">map</a><a id="40560" class="Symbol">;</a> <a id="40562" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#3432" class="Function">foldr</a><a id="40567" class="Symbol">;</a> <a id="40569" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Base.html#5510" class="Function">downFrom</a><a id="40577" class="Symbol">)</a>
<a id="40579" class="Keyword">import</a> <a id="40586" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.All.html" class="Module">Data.List.All</a> <a id="40600" class="Keyword">using</a> <a id="40606" class="Symbol">(</a><a id="40607" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.All.html#1176" class="Datatype">All</a><a id="40610" class="Symbol">;</a> <a id="40612" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.All.html#1239" class="InductiveConstructor">[]</a><a id="40614" class="Symbol">;</a> <a id="40616" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.All.html#1256" class="InductiveConstructor Operator">_∷_</a><a id="40619" class="Symbol">)</a>
<a id="40621" class="Keyword">import</a> <a id="40628" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Any.html" class="Module">Data.List.Any</a> <a id="40642" class="Keyword">using</a> <a id="40648" class="Symbol">(</a><a id="40649" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.Any.html#1052" class="Datatype">Any</a><a id="40652" class="Symbol">;</a> <a id="40654" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.Any.html#1115" class="InductiveConstructor">here</a><a id="40658" class="Symbol">;</a> <a id="40660" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Relation.Unary.Any.html#1168" class="InductiveConstructor">there</a><a id="40665" class="Symbol">)</a>
<a id="40667" class="Keyword">import</a> <a id="40674" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Membership.Propositional.html" class="Module">Data.List.Membership.Propositional</a> <a id="40709" class="Keyword">using</a> <a id="40715" class="Symbol">(</a><a id="40716" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Membership.Setoid.html#877" class="Function Operator">_∈_</a><a id="40719" class="Symbol">)</a>
<a id="40721" class="Keyword">import</a> <a id="40728" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html" class="Module">Data.List.Properties</a>
  <a id="40751" class="Keyword">using</a> <a id="40757" class="Symbol">(</a><a id="40758" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#28549" class="Function">reverse-++-commute</a><a id="40776" class="Symbol">;</a> <a id="40778" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#3412" class="Function">map-compose</a><a id="40789" class="Symbol">;</a> <a id="40791" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#2721" class="Function">map-++-commute</a><a id="40805" class="Symbol">;</a> <a id="40807" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#15835" class="Function">foldr-++</a><a id="40815" class="Symbol">)</a>
  <a id="40819" class="Keyword">renaming</a> <a id="40828" class="Symbol">(</a><a id="40829" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#35768" class="Function">mapIsFold</a> <a id="40839" class="Symbol">to</a> <a id="40842" href="https://agda.github.io/agda-stdlib/v1.1/Data.List.Properties.html#35768" class="Function">map-is-foldr</a><a id="40854" class="Symbol">)</a>
<a id="40856" class="Keyword">import</a> <a id="40863" href="https://agda.github.io/agda-stdlib/v1.1/Algebra.Structures.html" class="Module">Algebra.Structures</a> <a id="40882" class="Keyword">using</a> <a id="40888" class="Symbol">(</a><a id="40889" href="https://agda.github.io/agda-stdlib/v1.1/Algebra.Structures.html#2215" class="Record">IsMonoid</a><a id="40897" class="Symbol">)</a>
<a id="40899" class="Keyword">import</a> <a id="40906" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Unary.html" class="Module">Relation.Unary</a> <a id="40921" class="Keyword">using</a> <a id="40927" class="Symbol">(</a><a id="40928" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Unary.html#3474" class="Function">Decidable</a><a id="40937" class="Symbol">)</a>
<a id="40939" class="Keyword">import</a> <a id="40946" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.html" class="Module">Relation.Binary</a> <a id="40962" class="Keyword">using</a> <a id="40968" class="Symbol">(</a><a id="40969" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.Core.html#5557" class="Function">Decidable</a><a id="40978" class="Symbol">)</a>
</pre>{% endraw %}
{::comment}
The standard library version of `IsMonoid` differs from the
one given here, in that it is also parameterised on an equivalence relation.
{:/}

标准库中的 `IsMonoid` 与给出的定义不同，因为它可以针对特定的等价关系参数化。

{::comment}
Both `Relation.Unary` and `Relation.Binary` define a version of `Decidable`,
one for unary relations (as used in this chapter where `P` ranges over
unary predicates) and one for binary relations (as used earlier, where `_≤_`
ranges over a binary relation).
{:/}

`Relation.Unary` 和 `Relation.Binary` 都定义了 `Decidable` 的某个版本，一个用于单元关系（正如本章中的单元谓词 `P`），一个用于二元关系（正如之前使用的 `_≤_`）。

## Unicode

{::comment}
This chapter uses the following unicode:
{:/}

本章使用了下列 Unicode：

{::comment}
    ∷  U+2237  PROPORTION  (\::)
    ⊗  U+2297  CIRCLED TIMES  (\otimes, \ox)
    ∈  U+2208  ELEMENT OF  (\in)
    ∉  U+2209  NOT AN ELEMENT OF  (\inn, \notin)
{:/}

    ∷  U+2237  比例  (\::)
    ⊗  U+2297  带圈的乘号  (\otimes, \ox)
    ∈  U+2208  元素属于  (\in)
    ∉  U+2209  元素不属于  (\inn, \notin)