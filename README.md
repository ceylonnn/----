2开头的文件表示第二次作业。hw5和hw6虽然不知为何被标记为second hw但他们其实是第一次的附加题。
2hw1：定时器中断消抖+led亮灭/频闪切换；
2hw2：呼吸灯+舵机自转（PSC=719，ARR=199/1999（led/舵机），CCR=duty*（ARR+1），duty即占空比）；
2hw3：ADC电压测定；
2hw4：led亮度/呼吸灯切换；
2hw5：ADC控制电机（虽然占空比换算用的是算出来的电压而非原本的ADC值，不过效果是一致的；voltage = adc * 3.3 / 4095 ，duty = voltage / 3.3 ， ccr = duty * arr）。
