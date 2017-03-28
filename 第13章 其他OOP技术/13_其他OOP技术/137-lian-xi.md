##练习

（1）编写事件处理程序的代码，这些代码使用了通用语法（object sender, EventArgs e），该语法将接受本章前面的Timer.Elapsed或Connection.MessageArrived事件。处理程序应输出一个表示接收了什么类型事件的字符串，并根据引发的事件，输出MessageArrivedEventArgs参数的Message属性或ElapsedEventArgs参数的SignalTime属性。

>答案：

>```csharp
    public void ProcessEvent(object source, EventArgs e)
    {
        if(e is MessageArrivedEventArgs)
        {
            Console.WriteLine("Connection.MessageArrived event received.");
            Console.WriteLine("Message: {0}", (e as MessageArrivedEventArgs).Message);
        }
        if(e is ElapsedEventArgs)
        {
            Console.WriteLine("Timer.Elapsed event received.");
            Console.WriteLine("SignalTime: {0}", (e as ElapsedEventArgs).SignalTime);
        }
    }
```

（2）修改扑克牌游戏示例，设置流行拉米扑克牌的更有趣的取胜条件。即一个玩家要取胜，手中的牌必须包含两套牌，一套由3张牌组成，另一套由4张牌组成。一套牌应是连续的同花色的牌（例如，3H、4H、5H、6H）或者几张同点的牌（例如，2H、2D、2S）。


🔚