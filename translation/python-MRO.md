# Python 新式类继承关系的 C3 算法（Python 2.3 的方法解析顺序，MRO）

作者：[Michele Simionato](http://www.phyast.pitt.edu/~micheles/).

翻译：[刘硕](https://www.cnblogs.com/shuoliuchina/)

原文链接：https://www.python.org/download/releases/2.3/mro/

摘要：***本文档面向于想要了解Python 2.3版本中 C3 方法解析顺序的 Python程序开发人员。尽管它对新手而言不是很友好，本文档里面还是提供了很多有助于理解的解决问题的例子。目前我还不知道有哪个公开的文档解决了类似的问题，因此本文档还是很有用的。***

免责声明：

> 本人（指作者而非翻译者，翻译者按）将此文档以Python 2.3 许可证赠送给Python软件基金会（Python Software Foundation）。照例，我需要提醒读者下面的内容*应该*是正确的，但是我不能给出任何保证。请擦亮眼睛，风险自负！

鸣谢：

> 感谢Python邮件列表中所有给我提供支持的人。感谢Paul Foley指出了很多微小瑕疵，并且建议我加入局部优先排序部分。感谢David Goodger帮忙排版了reStructuredText部分。感谢David Mertz帮忙编辑。感谢Joan G. Stark提供爬虫图片。最后，感谢Guido van Rossum热情地把本文档加入到Python2.3的官方主页中。 

------

> ```
>                       .-=-.          .--.
>           __        .'     '.       /  " )
>   _     .'  '.     /   .-.   \     /  .-'\
>  ( \   / .-.  \   /   /   \   \   /  /    ^
>   \ `-` /   \  `-'   /     \   `-`  /
> jgs`-.-`     '.____.'       `.____.'
> ```

## 我们开始吧

> *Felix qui potuit rerum cognoscere causas* -- Virgilius
>
> 兴趣是最好的老师 -- 维吉尔

一切要从 Samuele Pedroni 在 Python 开发邮件列表提交的文章说起[^1]。在他发布的文章中，Samuele指出Python 2.2 的方法解析顺序并不是单调的。他提出可以用 C3 方法解析顺序来替换 Python 2.2 中的方法。Guido 赞同他的见解，因此在 Python 2.3 中使用了C3 算法。C3 方法本身和 Python 一点关系也没有，因为它是一个 Dylan 开发者发明的，而且它用来让口吃患者用纸来描述[^2]。本文为想要理解Python中这些更改的原因的 Python 爱好者们提供了一个（但愿）通俗易懂的 C3 算法介绍。

首先，我要指出，我将要讨论的仅适用于在 Python 2.2 中引入的*新式类*：*经典类*保留了它们原来的方法解析顺序，深度优先，先左后右。因此，原有的经典类的代码不会收到影响；而且即便理论上来说 Python 2.2 的新式类的代码会被破坏，但是实际上 C3 解析顺序和 Python 2.2 中的方法解析顺序相差无几，所以基本也不会有什么影响。因此：

> *不必担心！*

此外，除非你重度使用多继承，并且你有使用了非凡的继承，你是不必理解 C3 算法的，你可以直接跳过这篇文章。不过话说回来，如果你真的想要知道多继承究竟是如何实现的，那么这篇文章就是为你而写。好消息是，正如你期待的那样，这些原理并不是非常复杂。

让我们从一些基础的定义开始。

1. 假设在一个负载的多继承关系中，有一个 C 类。有一个很重要的任务：我们要在这些复杂的继承关系中明确方法的执行顺序。例如，分辨出 C 类的继承顺序。
2. 包括它本身咋内的 C 类继承顺序列表，以继承关系由近到远排序，被称作 C 类的优先级列表，或者线性化列表。
3. *方法解析顺序*（MRO）就是构造线性化列表的一串规则。在 Python 圈中，习惯用语“C 的MRO”的意义等同于 C 类的线性化列表。
4. 例如，在单继承体系中，如果 C 类是 C1 类的子类，C1 类又是 C2 类的子类。那么 C 类的线性化列表就理所当然是 [C, C1 , C2]。然而，在多继承体系中，构造线性化列表的方式就要稍微复杂一点，因为构造一个同时满足 *局部优先* 和 *单调* 的线性化列表优点不容易。
5. 我过会儿会讨论局部优先顺序，不过我要先在这里给出单调的定义。MRO 是单调的可以这样理解：*在 C 类的线性化列表中，如果 C1 类排在 C2 类的前面，那么在任何一个 C 类的子类中， C1 类都要优先于 C2 类*。如果MRO不满足单调性，无意间的衍生新类的操作就可能改变方法的解析顺序。这些改变会潜移默化地引入一些难以预测的 bug。过会儿会有这种 bug 出现的例子。
6. 并不是所有的类都能实现线性化。在复杂的继承关系中会有很多不可能让派生出的新类的所有属性都满足线性化的情况。

我在这里给出一个这种情况例子。康康下面的继承：

> ```python
> >>> O = object
> >>> class X(O): pass
> >>> class Y(O): pass
> >>> class A(X,Y): pass
> >>> class B(Y,X): pass
> ```

上面的继承关系可以用下面的图示表示。我用 O 指代 `object` 类，它是一切新式类的始祖。

> ```
>  -----------
> |           |
> |    O      |
> |  /   \    |
>  - X    Y  /
>    |  / | /
>    | /  |/
>    A    B
>    \   /
>      ?
> ```

对于这种情况，不可能从 A 类和 B 类派生出一个新的 C 类。因为在 A 类中， X 类的优先级高于 Y 类，但是在 B 类中，Y 类的优先级高于 X 类。这样一来，C 类中的方法解析顺序就矛盾了。

对于这种情况，在 Python 2.3 中会抛出一个异常（TypeError: MRO conflict among bases Y, X，即：类型错误：基类 Y 和 X 方法解析顺序矛盾）来避免小白程序员制造矛盾的继承关系。然而 Python 2.2 不会抛出异常，而是选择一个 *专门的* 顺序（在这种情况下会是CABXYO）。

------

> ```
>     _                   .-=-.          .-==-.
>    { }      __        .' O o '.       /  -<' )
>    { }    .' O'.     / o .-. O \     /  .--v`
>    { }   / .-. o\   /O  /   \  o\   /O /
>     \ `-` /   \ O`-'o  /     \  O`-`o /
> jgs  `-.-`     '.____.'       `.____.'
> ```

## C3 方法解析顺序

我来介绍几个在接下来的讨论中很有用的简单符号。我会用这些简写符号

> C1 C2 ... CN

来指代类的列表 [C1, C2, ... , CN].

列表的 *head* （头部）是它的第一个元素：

> head = C1

它的 *tail* （尾部）是列表的其余部分：

> tail = C2 ... CN.

我也会用这些符号

> C + (C1 C2 ... CN) = C C1 C2 ... CN

来表示列表的加和 [C] + [C1, C2, ... ,CN].

现在我可以解释 Python 2.3 中，MRO 是如何运作的了。

设想一个多重继承体系的 C 类，继承自基类 B1, B2, ... , BN。我们要计算出 C 类的线性化列表 L[C]。其规则如下：

> *C 类的线性化列表是 C 类与其融合（merge）后的所有父类线性化列表再加上所有父类组成新列表的和。*

用符号来表示就是：

> L[C(B1 ... BN)] = C + merge(L[B1] ... L[BN], B1 ... BN)

特别地，如果 C 类是没有父类的 `object` 类，它的线性化列表就很简单了：

> L[object] = object.

然而，通常下还是要用下面的规则来计算融合（merge）的结果：

> *首先取出列表的头部（head），例如 L\[B1\]\[0\]；如果这个头部没有在其他任何列表的尾部（tail），就把它加入到 C 类的线性化列表中，并且把它从融合（merge）中全部移除。否则就查看并移除下一个列表的头部，如果它是一个符合条件的头部的话。重复操作直到所有的类都被移除或者不可能找到合适的头部了。在这种情况下，不可能进行融合，Python 2.3 会拒绝创建 C 类，并且抛出一个异常。*

这个规则确保了融合操作 *保留* 顺序，如果顺序可以被保留的话。另一方面，如果顺序无法被保留（就像前面的一串顺序矛盾的例子），融合将无法计算。

如果 C 类只有一个父类（单继承）的融合是非常容易计算的。在这种情况下：

> L[C(B)] = C + merge(L[B],B) = C + L[B]

然而，多继承的情况就变得稍微复杂一点。我觉得如果不举几个例子的话，你们是没办法理解的 ;-)

------

> ```
>           .-'-.
>         /'     `\
>       /' _.-.-._ `\
>      |  (|)   (|)  |
>      |   \__"__/   |
>      \    |v.v|    /
>       \   | | |   /
>        `\ |=^-| /'
>          `|=-=|'
>           | - |
>           |=  |
>           |-=-|
>     _.-=-=|= -|=-=-._
>    (      |___|      )
>   ( `-=-=-=-=-=-=-=-` )
>   (`-=-=-=-=-=-=-=-=-`)
>   (`-=-=-=-=-=-=-=-=-`)
>    (`-=-=-=-=-=-=-=-`)
>     (`-=-=-=-=-=-=-`)
> jgs  `-=-=-=-=-=-=-`
> ```

## 例子

第一个例子。思考一下下面的继承关系：

> ```python
> >>> O = object
> >>> class F(O): pass
> >>> class E(O): pass
> >>> class D(O): pass
> >>> class C(D,F): pass
> >>> class B(D,E): pass
> >>> class A(B,C): pass
> ```

这种情况，继承关系可以画成这样：

> ```
>                           6
>                          ---
> Level 3                 | O |                     (更加通用)
>                       /  ---  \
>                      /    |    \                      |
>                     /     |     \                     |
>                    /      |      \                    |
>                   ---    ---    ---                   |
> Level 2        3 | D | 4| E |  | F | 5                |
>                   ---    ---    ---                   |
>                    \  \ _ /       |                   |
>                     \    / \ _    |                   |
>                      \  /      \  |                   |
>                       ---      ---                    |
> Level 1            1 | B |    | C | 2                 |
>                       ---      ---                    |
>                         \      /                      |
>                          \    /                      \ /
>                            ---
> Level 0                 0 | A |                   (更加具体)
>                            ---
> ```

O、D、E 和 F 类的线性化列表就很简单了：

> ```
> L[O] = O
> L[D] = D O
> L[E] = E O
> L[F] = F O
> ```

B 类的线性化列表可以这样计算：

> ```
> L[B] = B + merge(DO, EO, DE)
> ```

我们看到，D 是一个合适的头部，这样我们就把它取出来，然后计算就简化为 `merge(O,EO,E)`。此时，O 不是一个合适的头部，因为他在序列 EO 的尾部中。对于这种情况，规则告诉我们要跳过他到下一个序列。然后我们发现 E 是一个合适的头部，我们将它取出，把计算简化为 `merge(O,O)` ，也就得到了 O。因此

> ```
> L[B] =  B D E O
> ```

使用同样的程序，我们就能计算出：

> ```
> L[C] = C + merge(DO,FO,DF)
>      = C + D + merge(O,FO,F)
>      = C + D + F + merge(O,O)
>      = C D F O
> ```

现在我们可以计算：

> ```
> L[A] = A + merge(BDEO,CDFO,BC)
>      = A + B + merge(DEO,CDFO,C)
>      = A + B + C + merge(DEO,DFO)
>      = A + B + C + D + merge(EO,FO)
>      = A + B + C + D + E + merge(O,FO)
>      = A + B + C + D + E + F + merge(O,O)
>      = A B C D E F O
> ```

在这个例子中，线性化列表按照继承层次很好地排序。我们可以看到，低等级的（例如更加具体的类，或者更靠下的类）有更高的优先级（参见继承关系图示）。然而，并不是所有情况都是这样的结果。

我的第二个例子可以给读者做个练习，请计算下面每个类的线性化列表

> ```python
> >>> O = object
> >>> class F(O): pass
> >>> class E(O): pass
> >>> class D(O): pass
> >>> class C(D,F): pass
> >>> class B(E,D): pass
> >>> class A(B,C): pass
> ```

与前面例子中唯一的改变是 B(D,E) --> B(E,D)。然而，就是这个如此微小的修改，完全颠覆了继承顺序

> ```
>                            6
>                           ---
> Level 3                  | O |
>                        /  ---  \
>                       /    |    \
>                      /     |     \
>                     /      |      \
>                   ---     ---    ---
> Level 2        2 | E | 4 | D |  | F | 5
>                   ---     ---    ---
>                    \      / \     /
>                     \    /   \   /
>                      \  /     \ /
>                       ---     ---
> Level 1            1 | B |   | C | 3
>                       ---     ---
>                        \       /
>                         \     /
>                           ---
> Level 0                0 | A |
>                           ---
> ```

请注意观察在继承关系第二层的 E 类，它的继承关系要高于 C 类。这个例子说明，E 类比 C 类更具体，即便它的等级更高。

懒懒的程序员可以从 Python 2.2 中直接过的 MRO，因为它恰好和 Python 2.3 的线性化列表相同。我们完全可以调用 A 类的 .mro() 方法：

> ```python
> >>> A.mro()
> (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.E'>,
> <class '__main__.C'>, <class '__main__.D'>, <class '__main__.F'>,
> <type 'object'>)
> ```

最后，让我像一个第一部分的例子，调用一系列顺序矛盾。在当时的例子中，我们可以很直接地得到O、X、Y、A 和 B 的线性化列表：

> ```
> L[O] = 0
> L[X] = X O
> L[Y] = Y O
> L[A] = A X Y O
> L[B] = B Y X O
> ```

然而，不可能计算出继承自 A 类和 B 类的 C 类的线性化列表：

> ```
> L[C] = C + merge(AXYO, BYXO, AB)
>      = C + A + merge(XYO, BYXO, B)
>      = C + A + B + merge(XYO, YXO)
> ```

到这时，我们就不能融合列表 XYO 和 YXO。因为 X 在 YXO 的尾部，而 Y 在 XYO 的尾部：因此我们找不到合适的头部，C3 算法终止。Python 2.3 会抛出异常，并拒绝创建 C 类。

------

> ```
>                       __
>     (\   .-.   .-.   /_")
>      \\_//^\\_//^\\_//
> jgs   `"`   `"`   `"`
> ```

## 不良的方法解析顺序

当一个方法解析顺序破坏了局部优先顺序和单调性，那它就是 *不良* 的。在这个部分，我将会把 Python 2.2 中经典类和新式类的不良 MRO 都介绍给你。

让我们以简单点的局部优先排序开始。思考下面的例子：

> ```python
> >>> F=type('Food',(),{'remember2buy':'spam'})
> >>> E=type('Eggs',(F,),{'remember2buy':'eggs'})
> >>> G=type('GoodFood',(F,E),{}) # 这在 Python 2.3 中是一个错误！
> ```

其继承关系图

> ```
>              O
>              |
> (buy spam)   F
>              | \
>              | E   (buy eggs)
>              | /
>              G
> 
>       (buy eggs or spam ?)
> ```

我们看到类 G 继承自 F 和 E，F 要先于 E：因此我们期待中的是属性 *G.remember2buy* 继承自 *F.remember2buy*，而不是 *E.remember2buy*：

而 Python 2.2 给出的结果是：

> ```python
> >>> G.remember2buy
> 'eggs'
> ```

这个例子破坏了局部优先顺序，因为在 Python 2.2 的局部优先列表中，比如 G 的父类，没有被 G 的线性化列表保护（译者按：P22 指代 Python 2.2，这里特指 Python 2.2 的新式类）：

> ```
> L[G,P22]= G E F object   # F *跟着* E
> ```

有些人可能辩解说，在 Python 2.2 的线性化列表中，F 被排在 E 的后面是因为 F 比 E 更不具体，因为 F 是 E 的超类。然而打破局部优先顺序会很不直观，而且容易引发错误。这绝对是正确的因为它跟经典类不同：

> ```python
> >>> class F: remember2buy='spam'
> >>> class E(F): remember2buy='eggs'
> >>> class G(F,E): pass
> >>> G.remember2buy
> 'spam'
> ```

这种情况下的 MRO 是 GFEF，而且局部优先顺序被保留了。

作为通用规则，像前面的那种继承方式应该被避免，因为不能清楚地知道到底是应该 F 凌驾于 E 之上还是相反。Python 2.3 会在创建 G 类的时候抛出异常，从而解决了这些矛盾，很有效地阻止程序员制造有歧义的继承关系。异常产生的原因是 C3 算法没能成功融合线性化列表

> ```
> merge(FO,EFO,FE)
> ```

这不能继续计算了，因为 F 在 EFO 的尾部，而 E 在 FE 的尾部。

这个问题正确的解决办法是设计一个不矛盾的继承关系，比如，让 G 继承 E 和 F（先继承更具体的）而不是继承自 F 和 E。这种情况的 MRO 无疑就是 GEF 了。

> ```
>            O
>            |
>            F (spam)
>          / |
> (eggs)   E |
>          \ |
>            G
>              (eggs, no doubt)
> ```

Python 2.3 强制要求程序员写合理的继承关系（或者，至少，不太可能犯错的继承关系）。

与此相关，我要指出的是，Python 2.3 的算法已经足够智能，能够识别明显的错误，例如在父类列表中出现重复继承的情况：

> ```python
> >>> class A(object): pass
> >>> class C(A,A): pass # error
> Traceback (most recent call last):
>   File "<stdin>", line 1, in ?
> TypeError: duplicate base class A
> ```

Python 2.2（经典类和新式类都是如此）遇到这种情况，不会抛出任何异常。

最后，我要指出我们能从这个例子中学到的两个技巧：

1. 不要理会名字，MRO 也会解析属性的顺序，而非仅仅解析方法顺序；
2. Python 爱好者默认的食物是午餐肉！（虽然你已经知道了 ;-）

------

> ```
>                       __
>     (\   .-.   .-.   /_")
>      \\_//^\\_//^\\_//
> jgs   `"`   `"`   `"`
> ```

我们已经讨论完局部优先顺序，现在让我们来研究单调性的问题吧。我的目标是告诉你们经典类和 Python 2.2 中的新式类都不是单调的。

要证明经典类的 MRO 不单调就很容易了，只要看看钻石继承关系图就足够：

> ```
>    C
>   / \
>  /   \
> A     B
>  \   /
>   \ /
>    D
> ```

大家很容易就能找到矛盾（译者按：P21 指代  ，即经典类）：

> ```
> L[B,P21] = B C        # B 优先于 C : B 的方法获胜！
> L[D,P21] = D A C B C  # B 落后于 C : C 的方法获胜！
> ```

另一方面，Python 2.2 和 2.3 的 MRO 解析起来都没问题，他们给出的结果都是

> ```
> L[D] = D A B C
> ```

Guido 在他的文章[^3]中指出，在实践中，经典类的 MRO 也没有那么糟糕，因为我们往往可以避免在经典类中使用钻石继承。但是新式类都继承自 `object`，因此所有多继承关系，都会产生钻石继承，总要出现矛盾。

Python 2.2 的 MRO 很难破坏单调性，但也不是完全不可能。下面的例子，由 Samuele Pedroni 提出，就展示出一个 Python 2.2 的 MRO 不单调的例子：

> ```python
> >>> class A(object): pass
> >>> class B(object): pass
> >>> class C(object): pass
> >>> class D(object): pass
> >>> class E(object): pass
> >>> class K1(A,B,C): pass
> >>> class K2(D,B,E): pass
> >>> class K3(D,A):   pass
> >>> class Z(K1,K2,K3): pass
> ```

这里是根据 C3 MRO 计算出来的线性化列表（读者可以练习着计算这些线性化列表，画出继承关系图 ;-）

> ```
> L[A] = A O
> L[B] = B O
> L[C] = C O
> L[D] = D O
> L[E] = E O
> L[K1]= K1 A B C O
> L[K2]= K2 D B E O
> L[K3]= K3 D A O
> L[Z] = Z K1 K2 K3 D A B C E O
> ```

Python 2.2 给出的 A、B、C、D、E、K1、K2 和 K3 的线性化列表跟上面是相同的，但是 Z 的线性化列表就有差别了：

> ```
> L[Z,P22] = Z K1 K3 A K2 D B C E O
> ```

很明显这个线性化列表是 *错误的*，因为在这个列表中 A 出现在了 D 的前面，然而 K3 的先领先表中，A 是排在了 D 的后面。换句话说，K3 继承自 D 类的方法应该优先于继承自 A 类的方法，但是在 K3 的子类 Z 中，继承自 A 类的方法要优先于继承自 D 类的方法！这违背了单调性原则。此外，Python 2.2 中 Z 的线性化列表也和局部优先顺序矛盾，因为 Z 类的局部优先顺序列表是 [K1, K2, K3]（K2 优先于 K3），然而在 Z 的线性化列表中，K2 *落后于* K3。这些麻烦解释了为什么 2.2 的规则会被 C3 规则取代。

------

> ```
>                                                          __
>    (\   .-.   .-.   .-.   .-.   .-.   .-.   .-.   .-.   /_")
>     \\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//
> jgs  `"`   `"`   `"`   `"`   `"`   `"`   `"`   `"`   `"`
> ```

## 写在最后

这部分写给那些没有耐心的，略过了前面所有内容，直接跳到最后的读者。这个部分也写给不想借此训练他/她头脑的懒懒的程序员。最后，谨以此献给有一点点高傲的程序员，不这样的话，他/她是不会读一篇关于多继承关系 C3 方法解析顺序的文章的 ;-) 集齐这三种美德（*单独* 有一种不算的哟）就可以召唤神龙啦：送给你一个简短的不需要牺牲你的脑细胞就可以计算 Python 2.3 MRO 列表的 Python 2.2 脚本。只需要稍微改一下最后一行代码就可以跟我在这篇文章中讨论的所有例子愉快地玩耍啦。

> ```python
> #<mro.py>
> 
> """Samuele Pedroni 开发的C3算法（Michele Simionato 让它变得通俗易懂，刘硕把它翻译成中文）"""
> 
> class __metaclass__(type):
>        "所有的类都在元类层面被修饰成很好打印的形式"
>        __repr__ = lambda cls: cls.__name__
> 
> class ex_2:
>        "严重的顺序矛盾" #From Guido
>        class O: pass
>        class X(O): pass
>        class Y(O): pass
>        class A(X,Y): pass
>        class B(Y,X): pass
>        try:
>            class Z(A,B): pass #在 Python 2.2 中会创建类 Z(A,B)
>        except TypeError:
>            pass # Z(A,B) 在 Python 2.3 中不会被创建
> 
> class ex_5:
>        "我的第一个例子"
>        class O: pass
>        class F(O): pass
>        class E(O): pass
>        class D(O): pass
>        class C(D,F): pass
>        class B(D,E): pass
>        class A(B,C): pass
> 
> class ex_6:
>        "我的第二个例子"
>        class O: pass
>        class F: pass
>        class E(O): pass
>        class D(O): pass
>        class C(D,F): pass
>        class B(E,D): pass
>        class A(B,C): pass
> 
> class ex_9:
>        "Python 2.2 的 MRO 和 C3 的区别" # Samuele 提出的例子
>        class O: pass
>        class A(O): pass
>        class B(O): pass
>        class C(O): pass
>        class D(O): pass
>        class E(O): pass
>        class K1(A,B,C): pass
>        class K2(D,B,E): pass
>        class K3(D,A): pass
>        class Z(K1,K2,K3): pass
> 
> def merge(seqs):
>        print '\n\nCPL[%s]=%s' % (seqs[0][0],seqs),
>        res = []; i=0
>        while 1:
>            nonemptyseqs=[seq for seq in seqs if seq]
>            if not nonemptyseqs: return res
>            i+=1; print '\n',i,'round: candidates...',
>            for seq in nonemptyseqs: # 在序列的头部找到合格的类
>                cand = seq[0]; print ' ',cand,
>                nothead=[s for s in nonemptyseqs if cand in s[1:]]
>                if nothead: cand=None # 不符合条件
>                else: break
>            if not cand: raise "矛盾的继承关系"
>            res.append(cand)
>            for seq in nonemptyseqs: # 把合适的类移除掉
>                if seq[0] == cand: del seq[0]
> 
> def mro(C):
>     "根据 C3 算法计算类的优先顺序列表（MRO）"
>     return merge([[C]]+map(mro,C.__bases__)+[list(C.__bases__)])
> 
> def print_mro(C):
>     print '\nMRO[%s]=%s' % (C,mro(C))
>     print '\nP22 MRO[%s]=%s' % (C,C.mro())
> 
> print_mro(ex_9.Z)
> 
> #</mro.py>
> ```

上面的代码需要用 Python 2 才能运行，译者把作者原来的代码运行了一遍，结果如下。读者也可以修改作者的代码，获得不同例子的结果。

> ```
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[A]=[[A], [O, <type 'object'>], [O]] 
> 1 round: candidates...   A 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[B]=[[B], [O, <type 'object'>], [O]] 
> 1 round: candidates...   B 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[C]=[[C], [O, <type 'object'>], [O]] 
> 1 round: candidates...   C 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[K1]=[[K1], [A, O, <type 'object'>], [B, O, <type 'object'>], [C, O, <type 'object'>], [A, B, C]] 
> 1 round: candidates...   K1 
> 2 round: candidates...   A 
> 3 round: candidates...   O   B 
> 4 round: candidates...   O   O   C 
> 5 round: candidates...   O 
> 6 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[D]=[[D], [O, <type 'object'>], [O]] 
> 1 round: candidates...   D 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[B]=[[B], [O, <type 'object'>], [O]] 
> 1 round: candidates...   B 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[E]=[[E], [O, <type 'object'>], [O]] 
> 1 round: candidates...   E 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[K2]=[[K2], [D, O, <type 'object'>], [B, O, <type 'object'>], [E, O, <type 'object'>], [D, B, E]] 
> 1 round: candidates...   K2 
> 2 round: candidates...   D 
> 3 round: candidates...   O   B 
> 4 round: candidates...   O   O   E 
> 5 round: candidates...   O 
> 6 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[D]=[[D], [O, <type 'object'>], [O]] 
> 1 round: candidates...   D 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[<type 'object'>]=[[<type 'object'>], []] 
> 1 round: candidates...   <type 'object'> 
> 
> CPL[O]=[[O], [<type 'object'>], [<type 'object'>]] 
> 1 round: candidates...   O 
> 2 round: candidates...   <type 'object'> 
> 
> CPL[A]=[[A], [O, <type 'object'>], [O]] 
> 1 round: candidates...   A 
> 2 round: candidates...   O 
> 3 round: candidates...   <type 'object'> 
> 
> CPL[K3]=[[K3], [D, O, <type 'object'>], [A, O, <type 'object'>], [D, A]] 
> 1 round: candidates...   K3 
> 2 round: candidates...   D 
> 3 round: candidates...   O   A 
> 4 round: candidates...   O 
> 5 round: candidates...   <type 'object'> 
> 
> CPL[Z]=[[Z], [K1, A, B, C, O, <type 'object'>], [K2, D, B, E, O, <type 'object'>], [K3, D, A, O, <type 'object'>], [K1, K2, K3]] 
> 1 round: candidates...   Z 
> 2 round: candidates...   K1 
> 3 round: candidates...   A   K2 
> 4 round: candidates...   A   D   K3 
> 5 round: candidates...   A   D 
> 6 round: candidates...   A 
> 7 round: candidates...   B 
> 8 round: candidates...   C 
> 9 round: candidates...   O   E 
> 10 round: candidates...   O 
> 11 round: candidates...   <type 'object'> 
> MRO[Z]=[Z, K1, K2, K3, D, A, B, C, E, O, <type 'object'>]
> 
> P22 MRO[Z]=[Z, K1, K2, K3, D, A, B, C, E, O, <type 'object'>]
> ```

就是这些了朋友们，

> 好好玩耍吧！

------

> ```
>     __
>    ("_\   .-.   .-.   .-.   .-.   .-.   .-.   .-.   .-.   /)
>       \\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//^\\_//
> jgs    `"`   `"`   `"`   `"`   `"`   `"`   `"`   `"`   `"`
> ```

## 引用资料

[^1]: The thread on python-dev started by Samuele Pedroni: http://mail.python.org/pipermail/python-dev/2002-October/029035.html
[^2]: The paper *A Monotonic Superclass Linearization for Dylan*: https://doi.org/10.1145/236337.236343
[^3]: Guido van Rossum's essay, *Unifying types and classes in Python 2.2*: http://www.python.org/2.2.2/descrintro.html