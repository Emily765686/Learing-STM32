主要作用  
GPIO：通用输入输出接口，可以控制 LED，也可以读取按键状态。  

主要流程  
```c
//1.使用RCC开启GPIO的时钟   

RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);  
//GPIOA指打开STM32上所有A引脚的时钟，如需打开B引脚，只需把A改成B，如下  
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);  

//2.给结构体成员赋值，配置参数    
GPIO_InitTypeDef GPIO_InitStructure; //定义GPIO初始化结构体变量  

GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;   // 推挽输出模式（选择输出模式）  
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;          // 选择PA0引脚,A或B参考上面开启的时钟  
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;  // 输出速度50MHz

// 3. 把配置写入寄存器，初始化生效
GPIO_Init(GPIOA, &GPIO_InitStructure);

//4.调用各个函数，来控制引脚
```
注意：为使main.c文件的简洁，123步骤通常封装在`void LED_Init()`中  
      使用时直接在main函数中调用`LED_Init()`使得该外设初始化完成  
      顺序不能改变  

主要函数
```c
GPIO_SetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);  
//作用：把指定引脚设置成高电平    
GPIO_SetBits(GPIOA, GPIO_Pin_0);    //→ PA0输出高   

GPIO_ResetBits(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);  
//作用：把指定引脚设置成低电平  
GPIO_ResetBits(GPIOA, GPIO_Pin_0);  // → PA0输出低  

GPIO_WriteBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, BitAction BitVal);  
//BitVal可以是 Bit_SET（置高电平）、Bit_RESET（置低电平）  
//相当于上面两个函数的合并版本(可高可低） 
//作用：灵活写单个引脚电平，高/低都可以传参  

GPIO_Write(GPIO_TypeDef* GPIOx, uint16_t PortVal);  
//作用：一次性给一整个GPIO端口（16个引脚）批量赋值  
//PortVal是16位数字，每一位对应一个引脚的电平，适合同时控制多个引脚  
```
