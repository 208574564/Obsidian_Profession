控制系统分类：
- 开环系统：
	- 一般开环控制系统:
	
	  ```mermaid
	  flowchart LR
	  	期望输出--X-->控制器--U_c-->执行器--U_a-->对象-->实际输出Y
	  ```
	
	- 前馈控制系统:
	
- 闭环系统：

  - 单闭环
  - 双闭环

- 复合系统：

  - 前馈-反馈复合控制系统


### PID
	PID是Proportional(比例)、Integral(积分)、Differential(微分)的首字母缩写，是一种结合比例、积分、微分三种环节于一体的闭环控制算法。
PID的连续控制：
$$
u(t)=K_p(e(t)+\frac{1}{(T_t)}\int_0^te(t)dt+T_D\frac{de(t)}{dt}
$$
- Kp--比例增益，Kp与比例成倒数关系；
- Tt--积分时间常数，?；
- Td--微分时间常数，一般就等于采样时间间隔；
- u(t)--PID控制器的输出信号；
- e(t)--给定值r(t)与测量值误差；

比例（P）：
	比例控制器的输出与输入成正比关系，能够迅速地反应偏差，偏差减小的速度取决于比例系数Kp，Kp越大偏差减小得越快，但是极易引起震荡；Kp减小的时候发生震荡的可能性减小，但是调节的速度变慢。单纯的比例控制存在不能消除静态误差，需要积分来控制；
积分（I）：
	在积分环节，主要用于消除静态误差，提高系统的无差度。积分作用的强度取决于积分时间常数Ti，Ti越大积分作用越弱，反之则越强。
	积分控制作用的存在与偏差e(t)的存在时间有关，只要系统存在偏差，积分作用就会不断起作用，对输入偏差进行积分，使控制器的输出及执行器的开度不断变化，产生控制作用以减小偏差。
	在积分时间足够的情况下，可以完全消除静态误差，这时积分控制作用将维持不变，Ti越小，积分速度越快，积分作用越强，积分作用太强会使系统超调加大，甚至使系统出现振荡。
微分（D）：
	微分环节的作用是反应系统偏差的变化趋势，可以使系统在误差来临之前提前引入一个有效的修正信号，有利于提高输出响应的快速性，减小被控量的超调和增加系统的稳定性，虽然积分环节可以消除静态误差但是降低了系统的响应速度，所以引入微分控制器就有必要，尤其是具有较大惯性的被控对象使用了PI控制器就难以得到很好的动态调节品质，系统会产生较大的超调和振荡，这时可以引入微分作用。
	微分环节有助于系统减小超调，克服振荡，加快系统的响应速度，减小调节时间，从而改善了系统的动态性能，但微分时间常数过大，会使系统出现不稳定。
	微分控制作用一个很大的缺陷就是容易引入高频噪声，所有在干扰信号比较严重的流量控制系统中不宜引入微分控制作用。
静态误差：
	静态误差又称稳态误差，即当系统趋于稳定时，PID始终无法使输出结果到达预定目标。
	举例为：有一个水缸，初始水位为0.2m，与缸顶的误差为0.8m(error)，放水速度为0.1m/s，控制律为$y = K_pe$ 。假定$K_p$定为0.5，在缸内水位达到0.8m后，水位将一直维持在这个数值上，而这与预定目标的误差则被称为稳态误差。
积分时间：$(T_i)$
	增大积分时间有利于减小超调，使系统在初期的震荡变小，但是会增长消除静态误差的时间。

#### PID的离散化表达
$$u(k)=K_p(err(t) + \frac{1}{T}\int err(t)dt+\frac{T_Dderr(t)}{dt})$$
- T：采集数据的时间间隔；
- 第K个周期时刻的误差等于输入(目标)值减输出(实际);
- 积分环节为所有时刻的误差和，则有：err(k)+err(k+1)+err(k+2)+……；
- 微分环节为第k时刻误差的变化率，则有：（err(k)-err(k-1)）/T;
$u(x)$可以表示为：（位置式或者全量式）
$$u(k) = K_p(err(k)+\frac{T}{T_i}\sum err(k)+\frac{T_D}{T}(err(k)-err(k-1)))$$
即

$$u(k) = K_p(err(k))+K_i\sum err(k)+K_d(err(k)-err(k-1))$$
其中：

- $K_i = Kp*\frac{T}{T_i}$
- $K_d=K_p*\frac{T_d}{T}$

$u(x)$可以表示为：(增量式)
$$\Delta u(k) = K_p(err(k)-err(k-1)) +K_i err(K) + K_d(err(k)-2err(k-1)+err(k-2))$$
最终的输出$u(k)=u(k-1)+\Delta u(k)$

#### 全量式和增量式的对比
对比区别：
- 增量式算法不需要堆积分项累加，控制量增量只与近几次的误差有关，计算误差堆控制量计算的影响较小，而位置式算法要对近几次的偏差进行积分累加,容易产生较大的累计误差；
- 增量式算法得出控制量的增量，例如在阀门控制中，只输出阀门开度的变化部分，误动作影响小，必要时还可通过逻辑判断限制或禁止本次输出，不会严重影响系统的工作；而位置式的输出直接对应对象的输出，因此对系统影响较大；
- 增量式算法控制输出的控制量增量，并无积分作用，因此该方法适合执行机构带积分部件的对象，如步进电机等，而位置式算法适用于执行机构不带积分部件的对象，如电液伺服阀；
- 在进行PID控制时，位置式PID需要有积分限幅和输出限幅，而增量式PID只需输出限幅；

位置式PID优缺点：
- 优点：在执行结构不带积分部件的对象中可以得到很好的应用；
- 缺点：每次输出都与过去的状态有关，计算时要对e(k)进行累加，运算工作量大。
增量式PID优缺点：
- 优点：
	- 误动作时影响小，必要时可用逻辑判断的方法去掉出错数据；
	- 手动\自动切换时冲击小，便于实现无扰动切换；
	- 算式中不需要累加，在速度闭环控制中有很好的实时性；
- 缺点：
	- 积分阶段效应大，有稳态误差；
	- 溢出的影响大；

#### 参考资料：

[从不懂到会用！PID从理论到实践~](https://www.bilibili.com/video/BV1B54y1V7hp?p=4&vd_source=4989143aa5589a07b1d5b2115b0f3ba8):控制系统分类及PID公式与原理
[PID解释与离散化算法公式 - 代码天地 (codetd.com)](https://www.codetd.com/article/10879296#:~:text=1%E3%80%81PID%E7%A6%BB%E6%95%A3%E5%8C%96%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%8F%EF%BC%9A%E5%88%86%E6%9C%89%E4%BD%8D%E7%BD%AE%E5%BC%8FPID%E7%AE%97%E6%B3%95%E4%B8%8E%E5%A2%9E%E9%87%8F%E5%BC%8FPID%E7%AE%97%E6%B3%95%202%E3%80%81%E4%BD%8D%E7%BD%AE%E5%BC%8FPID%E7%AE%97%E6%B3%95%EF%BC%9A%20%E5%81%87%E8%AE%BE%E9%87%87%E6%A0%B7%E6%97%B6%E9%97%B4%E9%97%B4%E9%9A%94%E4%B8%BA%20T%20%EF%BC%8C%E5%88%99%E5%9C%A8k%E6%97%B6%E5%88%BB%EF%BC%9A%E5%81%8F%E5%B7%AE%E4%B8%BA%20e%20%28k%29%20%EF%BC%9B%E7%A7%AF%E5%88%86%E4%B8%BA,%28k-1%29%2Be%20%28k-2%29%2B%E2%80%A6%2Be%20%280%29%20%EF%BC%9B%E5%BE%AE%E5%88%86%E4%B8%BA%20%28e%20%28k%29-e%20%28k-1%29%29%2FT%20%EF%BC%8C%E7%A6%BB%E6%95%A3%E5%8C%96%E5%90%8E%E7%9A%84%E5%85%AC%E5%BC%8F%E5%A6%82%E5%9B%BE%E4%B8%80%E4%B8%8E%E4%BB%8E%E7%A6%BB%E6%95%A3%E5%8C%96%E5%90%8E%E7%9A%84%E5%85%AC%E5%BC%8F%E5%88%86%E8%A7%A3%E5%90%8E%E6%89%80%E5%BE%97%E5%85%AC%E5%BC%8F%E5%A6%82%E5%9B%BE%E4%BA%8C%E3%80%82)
