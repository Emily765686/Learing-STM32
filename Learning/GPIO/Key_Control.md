用于按键控制

`对于单个按键控制单个LED灯`
按键控制的基本流程是：  

1.使用 `RCC_APB2PeriphClockCmd()` 开启按键引脚所在 GPIO 的时钟。  
2.将该引脚配置为输入模式，通常可选择上拉输入 GPIO_Mode_IPU。  
3.在循环中使用 `GPIO_ReadInputDataBit()` 读取按键电平。  
4.根据按键值执行操作，例如控制 LED 点亮或熄灭。  
5.一般还需要消除按键抖动，例如延时后再次读取按键状态。  

```c
//****在初始化之后

//读取按键的键码
uint8_t Key_GetNum(void)
{
    uint8_t KeyNum=0;
    if (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0)
    {
        Delay_ms(20);                     // ① 延时20ms，消抖（按下瞬间机械抖动）
        while (GPIO_ReadInputDataBit(GPIOB, GPIO_Pin_1) == 0); // ② 等待松手！(不松则一直循环)
        Delay_ms(20);                     // ③ 松手之后再延时20ms，松手消抖
        KeyNum = 1;                       // ④ 确认是一次有效的按键，标记键值
    }
    return KeyNum;
}

uint8_t KeyNum;
int main(void)
{
    LED_Init();  //初始化LED
    Key_Init();  //初始化按键
    while(1)
    {
        KeyNum = Key_GetNum();  //读取按键
        if(KeyNum == 1)
        {
            GPIO_SetBits(GPIOA, GPIO_Pin_0);    // 处理按键按下事件(熄灭LED)
        }
    }
}
//注意：按键按下时，PA0输出高电平，LED熄灭
```




`对于按键按下,LED取反,亮变灭,灭变亮`
可通过这个函数
```c
void LED1_Turn(void)
{
    if (GPIO_ReadOutputDataBit(GPIOA, GPIO_Pin_1) == 0)//此时灯亮，读取的输出为0
    {
        GPIO_SetBits(GPIOA, GPIO_Pin_1);   // 输出高电平 → 灯灭
    }
    else
    {
        GPIO_ResetBits(GPIOA, GPIO_Pin_1); // 输出低电平 → 灯亮
    }
}

