代码转换及重构完成
========

下午将VB的代码测试通过，经过一个月的代码转化（C#->VB）以及重构(Refactoring)工作已基本完成。

本身该项目一半是用C#写的，另一半是用VB写的。到新的公司后接手的第一个项目就遇到两种交插的代码,这着实让我头疼了老长一段时间。加上SAP的MM模块的业务逻辑我也不是很熟悉，赶鸭子上架般地推进着项目。程序员的悲哀之一——不停的敲代码，却不知道自己的代码是用来做什么的。

通过这次的重构，也渐渐发觉面象对象也并不是万能的。对于有些项目，按模块，采取过程化编程着实比面向对象更加实用。对于模块化分，按功能内聚进行组合，这是提高内聚，减少耦合的一个好方式。

对于重构，减少原有代码设计部分的耦合是其重点和难点，有些代码耦合度高到令人发指。一个变量放的位置稍有不同，将带来完全不同的结果。而这些耦合大都不是软件最初设计以及编程时决定的，而是后期由于需求的变更，而对代码修改时引进的。拿手头上这个项目来说，前后经手的三，四个人。每一个修改一点，对于全局变量，全局计数引入更是当时怎么爽就怎么用。直到后来当我接手这个项目时，发现已经到了死胡同。还好有重构（Refactoring），感谢Martin Fowler。

本周就要去付首付了，很快也将成为蜗牛了。不知道前方的压力对于我来说是好还是坏，但若有一天我发现自己因此而偏离了方向的时候，我有两件事要做。第一是买掉房子。第二是回家陪父母。

喜怒哀乐，皆见诸于文章。好事坏事不求博一笑，但求经历者以及即将经历者作一参考。足矣!