使用时请禁用dva，并且设置 dva终极技能重生可用

* 每个人都能使用DVA的核爆！
* 使用技能就会触发DVA的核爆 这太 imba了！！（上方HUD会显示冷却时间）

2M3X2

```
rule("设置全局参数")
{
	event
	{
		Ongoing - Global;
	}

	actions
	{
		Set Global Variable(A, 12);
		Set Global Variable(B, 0.500);
	}
}

rule("触发DVA大招")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Or(Is Button Held(Event Player, Ability 1), Is Button Held(Event Player, Ability 2)) == True;
		Hero Of(Event Player) != Hero(D.Va);
		Player Variable(Event Player, B) == 0;
	}

	actions
	{
		Set Player Variable(Event Player, B, Global Variable(A));
		Wait(Global Variable(B), Ignore Condition);
		Set Player Variable(Event Player, A, Hero Of(Event Player));
		Start Forcing Player To Be Hero(Event Player, Hero(D.Va));
		Stop Forcing Player To Be Hero(Event Player);
		Wait(1, Ignore Condition);
		Press Button(Event Player, Ultimate);
		Wait(3.300, Ignore Condition);
		Start Forcing Player To Be Hero(Event Player, Player Variable(Event Player, A));
		Stop Forcing Player To Be Hero(Event Player);
	}
}

rule("显示CD")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	actions
	{
		Create HUD Text(Event Player, Null, Null, String("{0} {1}", String("Cooldown", Null, Null, Null), String("{0} sec",
			Player Variable(Event Player, B), Null, Null), Null), Top, 0, White, Blue, Blue, Visible To and String);
	}
}

rule("处理CD减少")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, B) > 0;
	}

	actions
	{
		Modify Player Variable(Event Player, B, Subtract, 1);
		Wait(1, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("重生刷新CD")
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
		Set Player Variable(Event Player, B, 0);
	}
}
```
