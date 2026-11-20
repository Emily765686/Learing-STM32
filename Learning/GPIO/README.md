主要作用  
GPIO：通用输入输出接口，可以控制 LED，也可以读取按键状态。  

主要流程  
```c
//1.使用RCC开启GPIO的时钟   

RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA, ENABLE);  
//GPIOA指打开STM32上所有A引脚的时钟，如需打开B引脚，只需把A改成B，如下  
RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB, ENABLE);  

//2.给结构体成员赋值，配置参数  
//在确定是输入输出时，GPIO_Mode_**需要有所改变  eg：IPU为上拉输入  
GPIO_InitTypeDef GPIO_InitStructure; //定义GPIO初始化结构体变量  

GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;   // 推挽输出模式（选择输出模式）  
GPIO_InitStructure.GPIO_Pin = GPIO_Pin_0;          // 选择PA0引脚,A或B参考上面开启的时钟，如果有多个引脚，可用|，或者GPIO_Pin_All(所有A或B引脚）  
GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;  // 输出速度50MHz

// 3. 把配置写入寄存器，初始化生效
GPIO_Init(GPIOA, &GPIO_InitStructure);
`注意`:GPIO配置好后默认为低电平  


//4.调用各个函数，来控制引脚
```
注意：为使main.c文件的简洁，123步骤通常封装在`void LED_Init()`中  
      使用时直接在main函数中调用`LED_Init()`使得该外设初始化完成  
      顺序不能改变  

主要函数
1.（用于输出）
```c
//1.用于设置高低电平（可点灯）

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
//PortVal是16位数字，每一位对应一个引脚的电平，适合同时控制多个引脚  0或1


//2.用于读出程序命令引脚输出什么电平

uint8_t  GPIO_ReadOutputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
// 作用：读取单个输出引脚寄存器的电平

uint16_t GPIO_ReadOutputData(GPIO_TypeDef* GPIOx);
// 作用：读取整个端口16路输出寄存器的值


```

2.用于输入

```c
// 读取GPIO引脚相关函数（用于按键控制）

uint8_t  GPIO_ReadInputDataBit(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin);
// 作用：读取单个输入引脚电平，返回0或1

uint16_t GPIO_ReadInputData(GPIO_TypeDef* GPIOx);
// 作用：读取整个端口16个输入引脚，返回16位数据

```