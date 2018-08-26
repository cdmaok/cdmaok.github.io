本文用的主题		
 [Demo](http://webjeda.com/krishna),[github page](https://github.com/sharu725/krishna)		



 ## Todolist:		
 * 有时间可以把填补这个也加进来吧		
 * some code in java Concurrency		
 * 优化方法里面的Natural Gradient		
 * Java Interview的问题一个都没有回答！！！！		
 * xgboost 有空补一下gbm，rgt		
 * imbalanceml 把毕业论文里面的贴出来 （Done）		
 * CheatSpellspire 要补实验		
 * 基数估计里面有好多hash的函数可以看看，加到UsualHash		
 * 推荐系统也可以用毕业设计里面的东西		
 * 设计模式没写完		
 * Markdown的公式编辑什么时候学！！！		
 * LDA 大规模公式 （Done）		
 * http://jmlr.org/proceedings/papers/v37/kusnerb15.pdf 一个doc distance by word embedding		
 * DAO & MVC @ImportResource @Value		
 * 分布式神经网络 (Done)		
 * regex 那个好像有点忘记了		
 * word embedding (Done)		
 * rnn attention		
 * https://districtdatalabs.silvrback.com/getting-started-with-spark-in-python 先看这个！！！
 * 达摩流浪者 pdf
 * 我简单讲下2.加入节假日等时间因素的一个处理方法吧。把天气、节日做成连续变量或者0-1变量，做线性回归，用残差做状态空间模型（可以理解为超级版的ARIMA），R里{forecast}包中有个tbats函数，有心的话可以看下这个函数的帮助文档和论文。这样的思路可以用于处理节假日，天气，多周期，不平稳，各种类似的时序问题。 https://www.zhihu.com/question/37698412
 LSTM 只能用 sigmoid 和 tanh 的

 * 多元时间序列 https://github.com/davidhallac/TICC ​​​​

 * docker 教程 (Half Done)


* hidden debt in ml
  * 机器学习系统中各个特征并不独立，修改每个特征会影响整个系统的表现，一种可能的解决方案是采用集成方法
  * 还有一种级联的关系，一个model依赖另外一个model的结果，如果修改依赖model，会导致两外一个model的失效，解决方案是将被依赖model的特征直接放进来。


* 周志华 twice learning

* t-SNE 降维方法

* 百度云api工具 bypy

* vi 配色 set t_Co=256

* 时间序列异常检测


时序交叉验证有很多不同的方式，例如有或没有再拟合（refitting）而执行滚动式预测、或者如时序 bootstrap 重采样等更加详细的策略等

LOCF'ed下一个观测数据复制前面的


1.是不是可以考虑删掉值为0的部分

1.部分值不能及时到达，频率提高
2.时效性
3.新的特征
4.损失函数


bokeh python 交互式可视化库

kaggle 里面时序评价指标为SMAPE

mape = |预测 - 真实| / 真实

之所以不用mape，是应为这个东西不对称，当预测 = 100，真实 = 150，mape = 0.33
如果互换值的话，也就是预测 = 150，真实 = 100，mape = 0.5，为了保证对称，所以不再除以真实值，而是真实值和预测值的均值，同时为了保证真实值和预测值的和不会出现抵消或者等于零的情况，同时对二者加上绝对值。


学习一下argparse的用法

找到每个序列中非零或者非nan的开始索引和结束索引
过滤掉太短的时间序列
然后填充合格的时间序列的nan值为0
计算年度和季度的自相关系数
one hot 向量也应该标准化

feature包括；
有效时间序列，外部特征，总流量，年度自相关，季度自相关，日期向量，




银行有很多顾客排队办业务，有两个窗口，现在有两个选项。一是让顾客排成一队，哪个窗口空闲就接待下一位客户；二是让顾客分别在两个窗口前排队，共排两条队。这两个选项中，哪一个方案能使顾客的平均等待时间最短？ 这就是Poisson distribution的一个应用。
Google S2

简单记录一下arima模型

可以分解成三个部分，三个部分分别对应三个参数，p,d,q
AR --> p , 可以通过自相关系数图的大概人工确定一下
I --> d, 可以通过观察原来的时序图是否收敛来确定，如果d=0时不收敛，那么可以通过差分d=1,观察是否收敛，逐级递增
MA --> q, moving average,就是前面多少项的滑动平均



总结纲要
1.画一个大图
2.

spark 容错机制


tensorflow

进出神经元的称之为tensor，神经元应该是一种运算符号，tensor包括多种，一种是constant变量，一种是placeholder（一般是观察到的变量），还有一种是variable（多为权重，用tf.assign进行赋值）

import numba
@numba.jit

 ## Matchp ReadlingList		
 * http://getdango.com/emojianddeeplearning/ 基本是用RNN训练emoji		
 * 还有一个用Multimodal 的方法做的。		
 * Davidov, D., Tsur,O., & Rappoport, A. (2010, August). Enhanced sentiment learning usingtwitter hashtags and smileys. In Proceedingsof the 23rd international conference on computational linguistics: posters (pp. 241249). Association forComputational Linguistics.		
 * Ganster, T., Eimler,S. C., & Krämer, N. C. (2012). Same same but different!? The differentialinfluence of smilies and emoticons on person perception. Cyberpsychology, Behavior, andSocial Networking, 15(4),226230.		
 * Kralj Novak, P.,Smailović, J., Sluban, B., Mozetič, I., Liu, B., Liu, B., … Good, I. (2015).Sentiment of Emojis. PLOS ONE, 10(12), e0144296. https://doi.org/10.1371/journal.pone.0144296		

 * Tian, Y., Galery,T., Dulcinati, G., Molimpakis, E., & Sun, C. (2017). Facebook emotions:Reactions and Emojis. In Proceedings of the EACL 2017 Workshop on NaturalLanguage Processing for Social Media (SocialNLP).		

 * Zhao, J., Dong, L., Wu, J., & Xu, K.(2012). MoodLens: An EmoticonBased Sentiment Analysis System for ChineseTweets. Proceedings of the 18th ACM SIGKDD International Conference onKnowledge Discovery and Data Mining  KDD ’12, 1528–1531. https://doi.org/10.1145/2339530.2339772




 ## ML con & dis

 Name	| Methodology	| What’s right	|What’s wrong
 --| --| --|--
Bayesian Learning	|You specify a prior probability distribution over data-makers, P(datamaker) then use Bayes law to find a posterior P(datamaker_x). True Bayesians integrate over the posterior to make predictions while many simply use the world with largest posterior directly.	|Handles the small data limit. Very flexible. Interpolates to engineering.	| Information theoretically problematic. Explicitly specifying a reasonable prior is often hard. Computationally difficult problems are commonly encountered.Human intensive. Partly due to the difficulties above and partly because “first specify a prior” is built into framework this approach is not very automatable.
Graphical/generative Models	| Sometimes Bayesian and sometimes not. Data-makers are typically assumed to be IID samples of fixed or varying length data. Data-makers are represented graphically with conditional independencies encoded in the graph. For some graphs, fast algorithms for making (or approximately making) predictions exist.	| Relative to pure Bayesian systems, this approach is sometimes computationally tractable. More importantly, the graph language is natural, which aids prior elicitation.	|Often (still) fails to fix problems with the Bayesian approach.In real world applications, true conditional independence is rare, and results degrade rapidly with systematic misspecification of conditional independence.
Convex Loss Optimization | Specify a loss function related to the world-imposed loss fucntion which is convex on some parametric predictive system. Optimize the parametric predictive system to find the global optima.|	Mathematically clean solutions where computational tractability is partly taken into account. Relatively automatable.|	The temptation to forget that the world imposes nonconvex loss functions is sometimes overwhelming, and the mismatch is always dangerous.Limited models. Although switching to a convex loss means that some optimizations become convex, optimization on representations which aren’t single layer linear combinations is often difficult.
Gradient Descent	| Specify an architecture with free parameters and use gradient descent with respect to data to tune the parameters.|	Relatively computationally tractable due to (a) modularity of gradient descent (b) directly optimizing the quantity you want to predict.	|Finicky. There are issues with paremeter initialization, step size, and representation. It helps a great deal to have accumulated experience using this sort of system and there is little theoretical guidance. Overfitting is a significant issue.
Kernel-based learning|	You chose a kernel K(x,x’) between datapoints that satisfies certain conditions, and then use it as a measure of similarity when learning.|People often find the specification of a similarity function between objects a natural way to incorporate prior information for machine learning problems. Algorithms (like SVMs) for training are reasonably practical—O(n2) for instance.|	Specification of the kernel is not easy for some applications (this is another example of prior elicitation). O(n2) is not efficient enough when there is much data.
Boosting| You create a learning algorithm that may be imperfect but which has some predictive edge, then apply it repeatedly in various ways to make a final predictor.| A focus on getting something that works quickly is natural. This approach is relatively automated and (hence) easy to apply for beginners.| The boosting framework tells you nothing about how to build that initial algorithm. The weak learning assumption becomes violated at some point in the iterative process.
Online Learning with Experts| You make many base predictors and then a master algorithm automatically switches between the use of these predictors so as to minimize regret.	| This is an effective automated method to extract performance from a pool of predictors.	| Computational intractability can be a problem. This approach lives and dies on the effectiveness of the experts, but it provides little or no guidance in their construction.
Learning Reductions	| You solve complex machine learning problems by reducing them to well-studied base problems in a robust manner.|	The reductions approach can yield highly automated learning algorithms.	| The existence of an algorithm satisfying reduction guarantees is not sufficient to guarantee success. Reductions tell you little or nothing about the design of the base learning algorithm.
PAC Learning	| You assume that samples are drawn IID from an unknown distribution D. You think of learning as finding a near-best hypothesis amongst a given set of hypotheses in a computationally tractable manner.	| The focus on computation is pretty right-headed, because we are ultimately limited by what we can compute.	| There are not many substantial positive results, particularly when D is noisy. Data isn’t IID in practice anyways.
Statistical Learning Theory|	You assume that samples are drawn IID from an unknown distribution D. You think of learning as figuring out the number of samples required to distinguish a near-best hypothesis from a set of hypotheses.|	There are substantially more positive results than for PAC Learning, and there are a few examples of practical algorithms directly motivated by this analysis.	| The data is not IID. Ignorance of computational difficulties often results in difficulty of application. More importantly, the bounds are often loose (sometimes to the point of vacuousness).
Decision tree learning	| Learning is a process of cutting up the input space and assigning predictions to pieces of the space.	Decision tree algorithms are well automated and can be quite fast.	There are learning problems which can not be solved by decision trees, but which are solvable. It’s common to find that other approaches give you a bit more performance. A theoretical grounding for many choices in these algorithms is lacking.Algorithmic complexity	Learning is about finding a program which correctly predicts the outputs given the inputs.	| Any reasonable problem is learnable with a number of samples related to the description length of the program.	| The theory literally suggests solving halting problems to solve machine learning.
RL, MDP learning	| Learning is about finding and acting according to a near optimal policy in an unknown Markov Decision Process.| We can learn and act with an amount of summed regret related to O(SA) where S is the number of states and A is the number of actions per state.|	Has anyone counted the number of states in real world problems? We can’t afford to wait that long. Discretizing the states creates a POMDP (see below). In the real world, we often have to deal with a POMDP anyways.
RL, POMDP learning	| Learning is about finding and acting according to a near optimaly policy in a Partially Observed Markov Decision Process	| In a sense, we’ve made no assumptions, so algorithms have wide applicability.|	All known algorithms scale badly with the number of hidden states.

insert into Student values('02' , 'qiandian' , '1990-12-21' , 'female');
insert into Student values('03' , 'sunfeng' , '1990-05-20' , 'female');
insert into Student values('04' , 'liyun' , '1990-08-06' , 'female');
insert into Student values('05' , 'zhoumei' , '1991-12-01' , 'male');
insert into Student values('06' , 'wulan' , '1992-03-01' , 'male');
insert into Student values('07' , 'zhengzhu' , '1989-07-01' , 'male');
insert into Student values('09' , 'zhangsan' , '2017-12-20' , 'male');
insert into Student values('10' , 'lisi' , '2017-12-25' , 'male');
insert into Student values('11' , 'lisi' , '2017-12-30' , 'male');
insert into Student values('12' , 'zhaoliu' , '2017-01-01' , 'male');
insert into Student values('13' , 'sunqi' , '2018-01-01' , 'male');


create table Course(CId varchar(10),Cname nvarchar(10),TId varchar(10));
insert into Course values('01' , 'chinese' , '02');
insert into Course values('02' , 'maths' , '01');
insert into Course values('03' , 'english' , '03');


create table Teacher(TId varchar(10),Tname varchar(10));
insert into Teacher values('01' , 'zhangsan');
insert into Teacher values('02' , 'lisi');
insert into Teacher values('03' , 'wangwu');



create table SC(SId varchar(10),CId varchar(10),score decimal(18,1));
insert into SC values('01' , '01' , 80);
insert into SC values('01' , '02' , 90);
insert into SC values('01' , '03' , 99);
insert into SC values('02' , '01' , 70);
insert into SC values('02' , '02' , 60);
insert into SC values('02' , '03' , 80);
insert into SC values('03' , '01' , 80);
insert into SC values('03' , '02' , 80);
insert into SC values('03' , '03' , 80);
insert into SC values('04' , '01' , 50);
insert into SC values('04' , '02' , 30);
insert into SC values('04' , '03' , 20);
insert into SC values('05' , '01' , 76);
insert into SC values('05' , '02' , 87);
insert into SC values('06' , '01' , 31);
insert into SC values('06' , '03' , 34);
insert into SC values('07' , '02' , 89);
insert into SC values('07' , '03' , 98);



--1.学生表
Student(SId,Sname,Sage,Ssex)
--SId 学生编号,Sname 学生姓名,Sage 出生年月,Ssex 学生性别

--2.课程表
Course(CId,Cname,TId)
--CId 课程编号,Cname 课程名称,TId 教师编号

--3.教师表
Teacher(TId,Tname)
--TId 教师编号,Tname 教师姓名

--4.成绩表
SC(SId,CId,score)
--SId 学生编号,CId 课程编号,score 分数

