# Clean Code (Meaningful Names)
[《Clean Code》](http://book.douban.com/subject/3032825/) Book Notes. Chapter *Meaningful
Names*

  看到Quora上的一个问题[What is the hardest thing you do as a software engineer?](https://www.quora.com/What-is-the-hardest-thing-you-do-as-a-software-engineer?srid=OnXH&share=1)
这个[答案](http://www.quora.com/What-is-the-hardest-thing-you-do-as-a-software-engineer/answer/Aditya-Muraletharan?srid=OnXH&share=1)
拿到最高票
> Coming up with meaningful variable names.

还有这条答案的评论
> Almost as difficult: coming up with meaningful commit messages.

哈哈，神回复简直了。我个人会选择评估项目的时间吧。下面是*Clean Code*这本书里关于命名的章节笔记。

**任何时候，一旦发现有更好的名称，就换掉旧的，THIS IS IMPORTANT!!!**

## 名副其实(Use Intention-Revealing Names)
BAD NAMES:

```
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x:theList) {         //没有意义的名称theList
        if (x[0] == 4) {            //莫名其妙的常量4
            list1.add(x);
        }
    }
    return list1;
}
```
GOOD NAMES:
```
public List<Cell> getFlaggedCells() {
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for (Cell cell: gameBoard) {
        if (cell.isFlagged()) {     //常量使用STATUS_VALUE或者用isFlagged()代替
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```
只要简单改一下名称，就能轻易了解代码逻辑

## 避免误导(Avoid DisInformation)
BAD NAMES:
```
accountList
XYZControllerForEfficientHandlingOfStrings, XYZControllerForEfficientStorageOfStrings
```
GOOD NAMES:
```
accountGroup, accounts //避免在名字中带有具体类型(List, Map...), 产生**误导**
XYZ.. //名字太像容易导致混淆
```

## 有意义的区分(Make Meaningful Distinctions)
`Product`类和`ProductInfo`以及`ProductData`类名称虽不同，意思却无区别。
`accountData`与`account`, `customerInfo`与`customer`都无区别。

## 使用可读、可搜索的名字(Use Pronounceable Searchable Name)
final static int WORK_DAYS_PER_WEEK = 5;

## 类名、方法名
Class Names should usually be Nouns or None Phrases.  
Method names should usually be Verbs or Verb Phrases.  
Use domain names.

附：[Google Java Style#Naming](http://google.github.io/styleguide/javaguide.html#s5-naming)


