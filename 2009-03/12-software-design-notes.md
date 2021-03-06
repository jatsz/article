软件设计中的几点杂记
============

### 正交性

今天是TopLanguage上看到一则讨论，其中说到了软件设计中的正交性。上网查了些资料，总结一下。

所谓的正交性是从几何学中借鉴过来的。试想一个二标坐标系x，y。对于一个坐标点，当我们改变x值的时候，它在y轴的投影保持不变。反之亦然。

这在程序设计中就是所谓的解耦，将x和y解耦。即x和y互不干涉。

解耦合的问题我在blog中也常常提到，解耦合的办法是保持功能设计的原子性。提倡以功能性内聚为导向。这为我们的软件设计，实际写代码中的一些决策提供的指导。

### KISS原则

Keep It Simple,Stupid !

我个人觉得所谓的KISS原则是Donald Knuth所反复忠告的：“别过早优化！”的另一个说法。无论这种优化是处于设计的考虑还是处于性能的考虑。毕竟纠正糟糕的代码比纠正糟糕的设计要简单。

**代码的抽象**不等于**代码的重用**。当然有的时候代码的抽象目的是为了重用，例如类库的设计。但有的时候代码的抽象有别的目的，比如控制变化、保证软件的高度可用性、提高软件整体质量等等。

但无论是**代码的抽象**或**代码的重用**都是有代价的。当这些代价超过抽象或重用本身价值的时候，问题就出现了。

代码重用中有一个实际问题非常之重要：当你想重用别人一份代码，但需要稍作修改。问题是谁来修改这份代码来满足你的需求？

如果是代码的原始作者修改这份代码是比较好的，但问题是这有时不太现实。

但若是你自己修改这份代码，那么你需要先理解整个实现。这成本也太大了，有时超过了自己再写一份这样的代码。

所以代码重用时常因为成本太高而没有被采用，干脆自已再造一个相似的轮子。
