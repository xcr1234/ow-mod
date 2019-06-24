单纯恶心人的模式，按ESC无法退出，除非ALT+f4

b站视频地址：<https://www.bilibili.com/video/av52947382/>

代码更新：H8MVW

仅仅4条规则实现：

```
rule("让麦克雷永远有大招")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Ultimate Charge Percent(Event Player) < 100;
	}

	actions
	{
		Set Ultimate Charge(Event Player, 100);
	}
}

rule("当麦克雷重生时开始使用大招")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Has Spawned(Event Player) == True;
	}

	actions
	{
		Start Holding Button(Event Player, Ultimate);
	}
}

rule("当大招取消时，立刻重生（新增 防止坠崖身亡）")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == False;
	}

	actions
	{
		Kill(Event Player, Null);
		Skip If(Compare(Y Component Of(Position Of(Event Player)), <, Y Component Of(Nearest Walkable Position(Position Of(
			Event Player)))), 1);
		Resurrect(Event Player);
		Skip If(Compare(Y Component Of(Position Of(Event Player)), >=, Y Component Of(Nearest Walkable Position(Position Of(
			Event Player)))), 1);
		Respawn(Event Player);
		Start Holding Button(Event Player, Ultimate);
	}
}

rule("设置麦克雷移速")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Has Spawned(Event Player) == True;
	}

	actions
	{
		Set Move Speed(Event Player, 20);
		Start Forcing Throttle(Event Player, 0.500, 1, 0, 1, 0, 1);
	}
}
```
