# Lecture 01 - Linear Support Vector Machine

## Large-Margin Separating Hyperplane

回顾一下我们之前介绍了 linear classification，对于线性可分的情况，我们可以使用 PLA/pocket 算法在平面或者超平面上把正负类分开。

![Linear Classification Revisited](http://ofqm89vhw.bkt.clouddn.com/9b86a22b9dbcb61b7d8bcffae5f7d72c.png)

例如对平面 ${2D}$ 这种情况，我们可以找到一条直线，能将正类和负类完全分开。但是，这样的直线通常不止一条，如下图所示。那么，下图中的三条分类线都能将数据分开，但是哪条线更好呢？

![Which Line Is Best?](http://ofqm89vhw.bkt.clouddn.com/6eda78e5e8d5289dd361e86e4cd7fad9.png)

这三条直线都是由 PLA/pocket 算法不断修正错误点而最终产生的，整个确定直线形状的过程是随机的。单从分类效果上看，这三条直线都满足要求，而且都满足 VC bound 要求，模型复杂度 ${\Omega(H)}$ 是一样的，即具有一定的泛化能力。但是，如果要选择的话，凭第一感觉，我们还是会选择第三条直线，感觉它的分类效果更好一些。那这又是为什么呢？

先给个简单解释，一般情况下，训练样本外的测量数据应该分布在训练样本附近，但与训练样本的位置有一些偏差。若要保证对未知的测量数据也能进行正确分类，最好让分类直线距离正类负类的点都有一定的距离。这样能让每个样本点附近的圆形区域是“安全”的。圆形区域越大，表示分类直线对测量数据误差的容忍性越高，越“安全”。

![Why Rightmost Hyperplane?](http://ofqm89vhw.bkt.clouddn.com/a682efd52717c22889d9e151be46b066.png)

如上图所示，左边的点距离分类直线的最小距离很小，它的圆形区域很小。那么，这种情况下，分类线对测量数据误差的容忍性就很差，测量数据与样本数据稍有偏差，很有可能就被误分。而右边的点距离分类直线的最小距离更大一些，其圆形区域也比较大。这种情况下，分类线对测量数据误差的容忍性就相对来说大很多，不容易误分。也就是说，左边分类线和右边分类线的最大区别是对这类测量误差的容忍度不同。

那么，如果每一笔训练资料距离分类线越远的话，就表示分类型可以忍受更多的测量误差（noise）。我们之前在《机器学习基石》中介绍过，noise是造成overfitting的主要原因，而测量误差也是一种noise。所以，如果分类线对测量误差的容忍性越好的话，表示这是一条不错的分类线。那么，我们的目标就是找到这样一条最“健壮”的线，即距离数据点越远越好。

![Fat Hyperplane](http://ofqm89vhw.bkt.clouddn.com/2513a64ae030e164b6cc53d30ecdeae7.png)

上面我们用圆形区域表示分类线能够容忍多少误差，也就相当于计算点到直线的距离。距离越大，表示直线越“胖”，越能容忍误差；距离越小，表示直线越“瘦”，越不能容忍误差，越胖越好。

如何定义分类线有多胖，就是看距离分类线最近的点与分类线的距离，我们把它用 margin 表示。分类线由权重 ${w}$ 决定，目的就是找到使margin最大时对应的 ${w}$ 值。整体来说，我们的目标就是找到这样的分类线并满足下列条件：

分类正确，即 ${y_n w^{T} x_n > 0}$。

![Large-Margin Separating Hyperplane](http://ofqm89vhw.bkt.clouddn.com/662844d53389d876cc57074f011e87e4.png)

## Standard Large-Margin Problem

要让margin最大，即让离分类线最近的点到分类线距离最大，我们先来看一下如何计算点到分类线的距离。

首先，我们将权重 ${w(w_0,w_1,\cdots,w_d)}$ 中的 ${w_0}$ 拿出来，用 ${b}$ 表示。同时省去 ${x_0}$ 项。这样，hypothesis就变成了 ${ h(x) = sign(w^T x + b)}$ 。

![Distance to Hyperplane: Preliminary](http://ofqm89vhw.bkt.clouddn.com/797ee17730042fe6a96e8acff7a29faf.png)

下面，利用图解的方式，详细推导如何计算点到分类平面的距离：

![Distance to Hyperplane](http://ofqm89vhw.bkt.clouddn.com/a931422bfcd827cdcc79af4cb88bdb02.png)

如上图所示，平面上有两个点：${x'}$ 和 ${x''}$ 。因为这两个点都在分类平面上，所以它们都满足：

$${w^{T} x' + b = 0}$$
$${w^{T} x'' + b = 0}$$

同时可以得到： ${w^{T}x'=-b, \ w^{T}x''=-b}$ ，则有：

$${w^{T}(x'' - x') = w^{T} x'' - w^{T} x'= -b -(-b) = 0}$$

${(x''-x')}$ 是平面上的任一向量，${(x''-x')}$ 与 ${w}$ 内积为 ${0}$ ，表示 ${(x''-x')}$ 垂直于 ${w}$，那么 ${w}$ 就是平面的法向量。

现在，若要计算平面外一点 ${x}$ 到该平面的距离，做法是只要将向量 ${(x-x')}$ 投影到垂直于该平面的方向（即w方向）上就可以了。那么，令 ${(x'' - x')}$ 与 ${w}$ 的夹角为 ${\theta}$ ，距离就可以表示为：

$${distance(x,b,w) = |(x-x') cos(\theta)|=|\ ||x-x'|| \cdot \frac{(x-x') w}{||x-x'|| \cdot ||w||}|= \frac{1}{||w||} |w^{T}x - w^{T}x'|}$$

代入 ${w^{T}x' = -b}$ ，可得：

$${distance(x,b,w)= \frac{1}{||w||}|w^{T}x+b|}$$

点到分类面（Separating Hyperplane）的距离已经算出来了。基于这个分类面，所有的点均满足： ${y_n(w^{T}x_n+b)>0}$ ，表示所有点都分类正确，则 distance 公式就可以变换成：

$${distance(x,b,w)= \frac{1}{||w||} y_n(w^{T}x_n+b)}$$

那么，我们的目标形式就转换为：

![Distance to Separating Hyperplane](http://ofqm89vhw.bkt.clouddn.com/1f8756e17577e696ae10373d631f14cf.png)

对上面的式子还不容易求解，我们继续对它进行简化。我们知道分类面 ${w^{T}x+b=0}$ 和 ${3w^{T}x+3b=0}$ 其实是一样的。也就是说，对 ${w}$ 和 ${b}$ 进行同样的缩放还会得到同一分类面。所以，为了简化计算，我们令距离分类满最近的点满足 ${y_n(w^{T}x_n+b)=1}$ 。那我们所要求的 margin 就变成了:

${margin(b,w)= \frac{1}{||w||}}$，这样，目标形式就简化为：

![Margin of Special Separating Hyperplane](http://ofqm89vhw.bkt.clouddn.com/689d1bdf757ec6be85f7ab24f9b19138.png)

这里可以省略条件： ${y_n(w^{T}x_n+b)>0}$ ，因为满足条件 ${y_n(w^{T}x_n+b)=1}$ 必然满足大于零的条件。我们的目标就是根据这个条件，计算 ${\frac{1}{||w||}}$ 的最大值。

刚刚我们讲的距离分类满最近的点满足 ${y_n(w^{T}x_n+b)=1}$ ，也就是说对所有的点满足 ${y_n(w^{T}x_n+b) \geq 1}$ 。另外，因为最小化问题我们最熟悉也最好解，所以可以把目标 ${\frac{1}{||w||}}$ 最大化转化为计算 ${\frac{1}{2}w^{T}w}$ 的最小化问题。

![Standard Large-Margin Hyperplane Problem](http://ofqm89vhw.bkt.clouddn.com/60e9600a24e27734d79b3a92e95e18fc.png)

如上图所示，最终的条件就是 ${y_n(w^{T}x_n+b) \geq 1}$ ，而我们的目标就是最小化 ${\frac{1}{2} w^{T}w}$ 值。

## Support Vector Machine

现在，条件和目标变成：

![Solving General SVM](http://ofqm89vhw.bkt.clouddn.com/d9a368d34ea7cf1b3b95eca501dab469.png)

现在，举个例子，假如平面上有四个点，两个正类，两个负类，不同点的坐标加上条件 ${y_n(w^{T}x_n+b) \geq 1}$ ，可以得到：

![Solving a Particular Standard Problem](http://ofqm89vhw.bkt.clouddn.com/f84d52eb33519cc8db19095a4b0dfcc6.png)

最终，我们得到的条件是：

$${w_1 \geq +1}$$
$${w_2 \leq -1}$$

而我们的目标是：

$${min\ \frac{1}{2}w^{T}w= \frac{1}{2}(w_1^2+w_2^2) \geq 1}$$

目标最小值为1，即 ${w_1=1}$ , ${w_2=-1}$, ${b=-1}$，那么这个例子就得到了最佳分类面的解，如图所示，且 ${margin(b,w)= \frac{1}{||w||} = \frac{1}{\sqrt{2}}}$。分类面的表达式为：

$${x_1 - x_2 - 1=0}$$

最终我们得到的矩的表达式为：

$${g_{SVM}(x)=sign(x_1 - x_2 - 1)}$$

Support Vector Machine(SVM) 这个名字从何而来？为什么把这种分类面解法称为支持向量机呢？这是因为分类面仅仅由分类面的两边距离它最近的几个点决定的，其它点对分类面没有影响。**决定分类面的几个点称之为支持向量（Support Vector），好比这些点“支撑”着分类面。而利用 Support Vector 得到最佳分类面的方法，称之为支持向量机（Support Vector Machine）。**

下面介绍SVM的一般求解方法。先写下我们的条件和目标：

![Solving General SVM](http://ofqm89vhw.bkt.clouddn.com/b79fe1404e793e1276f046bd278d119c.png)

这是一个典型的二次规划问题，即 Quadratic Programming（QP）。因为SVM 的目标是关于 ${w}$ 的二次函数，条件是关于 ${w}$ 和 ${b}$ 的一次函数，所以，它的求解过程还是比较容易的，可以使用一些软件（例如Matlab）自带的二次规划的库函数来求解。下图给出 SVM 与标准二次规划问题的参数对应关系：

![Quadratic Programming](http://ofqm89vhw.bkt.clouddn.com/d2f1fddbeca3567b913dbdcd60e5971f.png)

那么，线性 SVM 算法可以总结为三步：

- 计算对应的二次规划参数 ${Q}$，${p}$，${A}$，${c}$。
- 根据二次规划库函数，计算${b}$，${w}$。
- 将 ${b}$ 和 ${w}$ 代入 ${g_{SVM}}$ ，得到最佳分类面。

![SVM with QP Solver](http://ofqm89vhw.bkt.clouddn.com/7df75f11b973b38faf272ec561e9c9ed.png)

这种方法称为 Linear Hard-Margin SVM Algorithm。如果是非线性的，例如包含x的高阶项，那么可以使用我们之前在《机器学习基石》课程中介绍的特征转换的方法，先作 ${z_n=\Phi(x_n)}$ 的特征变换，从非线性的 ${x}$ 域映射到线性的 ${z}$ 域空间，再利用 Linear Hard-Margin SVM Algorithm 求解即可。

## Reasons behind Large-Margin Hyperplane

从视觉和直觉的角度，我们认为 Large-Margin Hyperplane 的分类效果更好。SVM 的这种思想其实与我们之前介绍的机器学习非常重要的正则化 regularization 思想很类似。regularization 的目标是将 ${E_{in}}$ 最小化，条件是 ${w^{T}w \leq C}$；SVM的目标是 ${w^{T}w}$ 最小化，条件是 ${y_n(w^{T}x_n+b) \geq 1}$ ，即保证了 ${E_{in}=0}$ 。有趣的是，regularization 与 SVM 的目标和限制条件分别对调了。其实，考虑的内容是类似的，效果也是相近的。SVM 也可以说是一种 weight-decay regularization，限制条件是 ${E_{in}=0}$ 。

![SVM and regularization](http://ofqm89vhw.bkt.clouddn.com/df6f105968ff93f055b601eae05ba892.png)

从另一方面来看，Large-Margin 会限制 Dichotomies 的个数。这从视觉上也很好理解，假如一条分类面越“胖”，即对应 Large-Margin，那么它可能 shtter 的点的个数就可能越少：

![Large-Margin Restricts Dichotomies](http://ofqm89vhw.bkt.clouddn.com/e6dc8e5fe09d2ec7b7484d88df848307.png)

之前的《机器学习基石》课程中介绍过，Dichotomies 与 VC Dimension 是紧密联系的。也就是说如果 Dichotomies 越少，那么复杂度就越低，即有效的 VC Dimension 就越小，得到 ${E_{out} \approx E_{in}}$，泛化能力强。

下面我们从概念的角度推导一下为什么 dichotomies 越少，VC Dimension 就越少。首先我们考虑一下 Large-Margin 演算法的 VC Dimension，记为 ${d_{vc}(A_\rho)}$ 。${d_{vc}(A_\rho)}$与数据有关，而我们之前介绍的 ${d_{vc}}$ 与数据无关。

假如平面上有 ${3}$ 个点分布在单位圆上，如果Margin为 ${0}$ ，即 ${\rho=0}$，这条细细的直线可以很容易将圆上任意三点分开（shatter），就能得到它的 ${d_{vc}=3}$。如果 ${\rho > \frac{\sqrt{3}}{2}}$ ，这条粗粗的线无论如何都不能将圆上的任一三点全完分开（no shatter），因为圆上必然至少存在两个点的距离小于 ${\sqrt{3}}$，那么其对应 ${d}$ 的${d_{vc}<3}$。

那么，一般地，在 ${d}$ 维空间，当数据点分布在半径为 ${R}$ 的超球体内时，得到的 ${d_{vc}(A_\rho)}$ 满足下列不等式：

![VC Dimension of Large-Margin Algorithm](http://ofqm89vhw.bkt.clouddn.com/27c326724a23f5c372925635ef267e4c.png)

之前介绍的 Perceptrons 的 VC Dimension 为 ${d+1}$，这里得到的结果是 Large-Margin 演算法的 ${d_{vc}(A_\rho) \leq d+1}$ 。所以，由于 Large-Margin，得到的 dichotomies 个数减少，从而 VC Dimension 也减少了。VC Dimension 减少降低了模型复杂度，提高了泛化能力。

## 总结

本节课主要介绍了线性支持向量机（Linear Support Vector Machine）。我们先从视觉角度出发，希望得到一个比较“胖”的分类面，即满足所有的点距离分类面都尽可能远。然后，我们通过一步步推导和简化，最终把这个问题转换为标准的二次规划（QP）问题。二次规划问题可以使用Matlab等软件来进行求解，得到我们要求的 ${w}$ 和 ${b}$ ，确定分类面。这种方法背后的原理其实就是减少了 dichotomies 的种类，减少了有效的 VC Dimension 数量，从而让机器学习的模型具有更好的泛化能力。

## 参考

1. [台湾大学林轩田机器学习技法课程学习笔记1 -- Linear Support Vector Machine](http://blog.csdn.net/red_stone1/article/details/73526457)