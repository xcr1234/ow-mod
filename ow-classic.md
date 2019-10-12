<https://owmod.net/413>


```
rule("--------------守望先锋怀旧服 V1.4   作者 misaka#51577 https://owmod.net/413--------------------------------")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	actions
	{
		Set Player Variable(Event Player, I, Empty Array);
	}
}

rule("dva飞行对周围敌人造成伤害")
{
	event
	{
		Ongoing - Each Player;
		All;
		D.Va;
	}

	conditions
	{
		Is Using Ability 1(Event Player) == True;
	}

	actions
	{
		Damage(Players Within Radius(Position Of(Event Player), 2, Opposite Team Of(Team Of(Event Player)), Surfaces And Enemy Barriers),
			Event Player, 20);
		Wait(0.500, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("DVA 按E技能 触发矩阵")
{
	event
	{
		Ongoing - Each Player;
		All;
		D.Va;
	}

	conditions
	{
		Is Button Held(Event Player, Ability 2) == True;
	}

	actions
	{
		Start Holding Button(Event Player, Secondary Fire);
		Wait(0.016, Ignore Condition);
		Loop If Condition Is True;
		Stop Holding Button(Event Player, Secondary Fire);
	}
}

rule("dva大招炸死自己")
{
	event
	{
		Ongoing - Each Player;
		All;
		D.Va;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Event Player, A, Ray Cast Hit Position(Eye Position(Event Player), Add(Eye Position(Event Player), Multiply(
			Multiply(2.500, Speed Of(Event Player)), Facing Direction Of(Event Player))), All Players(All Teams), Event Player, True));
		Wait(3, Ignore Condition);
		Abort If(Compare(Array Contains(Players Within Radius(Player Variable(Event Player, A), 20, All Teams, Surfaces And All Barriers),
			Event Player), ==, False));
		Damage(Event Player, Null, 1000);
	}
}

rule("DJ强音开 C=移速，D=治疗量")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Is Using Ability 2(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Event Player, C, 170);
		Set Player Variable(Event Player, D, 46.800);
	}
}

rule("DJ强音关")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Is Using Ability 2(Event Player) == False;
	}

	actions
	{
		Set Player Variable(Event Player, C, 130);
		Set Player Variable(Event Player, D, 16.250);
	}
}

rule("DJ切歌")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Is Button Held(Event Player, Ability 1) == True;
		Is Alive(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Event Player, Z, Modulo(Add(Player Variable(Event Player, Z), 1), 2));
	}
}

rule("DJ加速")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Player Variable(Event Player, Z) == 0;
	}

	actions
	{
		Set Move Speed(Players Within Radius(Position Of(Event Player), 30, Team Of(Event Player), Off), Player Variable(Event Player, C));
		Set Move Speed(Players Within Radius(Position Of(Event Player), 15, Team Of(Event Player), Off), Divide(Player Variable(
			Event Player, C), 1.200));
		Set Move Speed(Filtered Array(All Players(Team Of(Event Player)), Compare(Distance Between(Position Of(Current Array Element),
			Position Of(Event Player)), >, 30)), 100);
		Wait(0.500, Ignore Condition);
		Loop If Condition Is True;
		Set Move Speed(All Players(Team Of(Event Player)), 100);
	}
}

rule("DJ治疗")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Player Variable(Event Player, Z) == 1;
	}

	actions
	{
		Heal(Remove From Array(Players Within Radius(Position Of(Event Player), 30, Team Of(Event Player), Off), Players Within Radius(
			Position Of(Event Player), 15, Team Of(Event Player), Off)), Event Player, Divide(Player Variable(Event Player, D), 2));
		Wait(0.500, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("木有DJ时移速切回来")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Number of Heroes(Hero(Lúcio), Team Of(Event Player)) == 0;
		Player Variable(Event Player, S) == False;
	}

	actions
	{
		Set Move Speed(Event Player, 100);
	}
}

rule("卢西奥滑墙的移动速度")
{
	event
	{
		Ongoing - Each Player;
		All;
		Lúcio;
	}

	conditions
	{
		Is On Wall(Event Player) == True;
	}

	actions
	{
		Set Move Speed(Event Player, 85);
		Wait(0.100, Ignore Condition);
		Loop If Condition Is True;
		Set Move Speed(Event Player, 100);
	}
}

rule("------天使复活---------")
{
	event
	{
		Ongoing - Each Player;
		All;
		Mercy;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Resurrect(Players Within Radius(Event Player, 20, Team Of(Event Player), Off));
		Destroy All HUD Text;
	}
}

rule("框架")
{
	event
	{
		Ongoing - Each Player;
		All;
		Mercy;
	}

	conditions
	{
		Ultimate Charge Percent(Event Player) == 100;
	}

	actions
	{
		Set Global Variable(E, 0);
		Set Global Variable(F, Empty Array);
	}
}

rule("显示复活人数")
{
	event
	{
		Ongoing - Each Player;
		All;
		Mercy;
	}

	conditions
	{
		Ultimate Charge Percent(Event Player) == 100;
	}

	actions
	{
		Big Message(Event Player, String("{0}:{1}", String("Resurrect", Null, Null, Null), Global Variable(E), Null));
		Wait(0.250, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("复活范围")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Ultimate Charge Percent(Players On Hero(Hero(Mercy), Team Of(Event Player))) == 100;
		Distance Between(Players On Hero(Hero(Mercy), Team Of(Event Player)), Event Player) <= 20;
		Is Dead(Event Player) == True;
		Array Contains(Global Variable(F), Event Player) == False;
	}

	actions
	{
		Modify Global Variable(E, Add, 1);
		Modify Global Variable(F, Append To Array, Event Player);
	}
}

rule("若超出范围")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Array Contains(Global Variable(F), Event Player) == True;
		Distance Between(Players On Hero(Hero(Mercy), Team Of(Event Player)), Event Player) > 20;
	}

	actions
	{
		Modify Global Variable(E, Subtract, 1);
		Modify Global Variable(F, Remove From Array By Value, Event Player);
	}
}

rule("若在重生室复活")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Array Contains(Global Variable(F), Event Player) == True;
		Is Dead(Event Player) == False;
	}

	actions
	{
		Modify Global Variable(E, Subtract, 1);
		Modify Global Variable(F, Remove From Array By Value, Event Player);
	}
}

rule("复活范围2")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Ultimate Charge Percent(Players On Hero(Hero(Mercy), Team Of(Event Player))) == 100;
		Is Dead(Event Player) == True;
		Distance Between(Players On Hero(Hero(Mercy), Team Of(Event Player)), Event Player) <= 20;
		Array Contains(Global Variable(F), Event Player) == False;
	}

	actions
	{
		Modify Global Variable(E, Add, 1);
		Modify Global Variable(F, Append To Array, Event Player);
	}
}

rule("特效")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Is Using Ultimate(Players On Hero(Hero(Mercy), Team Of(Event Player))) == True;
		Array Contains(Global Variable(F), Event Player) == True;
	}

	actions
	{
		Set Status(Event Player, Null, Invincible, 2);
		Set Status(Event Player, Null, Rooted, 2);
		Create Effect(All Players(All Teams), Good Aura, Yellow, Event Player, 1, Visible To Position and Radius);
		Create Effect(All Players(All Teams), Good Aura, Yellow, Event Player, 1.500, Visible To Position and Radius);
		Create Effect(All Players(All Teams), Good Aura, Yellow, Event Player, 2, Visible To Position and Radius);
		Create Effect(All Players(All Teams), Light Shaft, Yellow, Event Player, 1, Visible To Position and Radius);
		Create Effect(All Players(All Teams), Ring, Yellow, Event Player, 1.600, Visible To Position and Radius);
		Create Effect(All Players(All Teams), Good Aura Sound, Yellow, Event Player, 1.400, Visible To Position and Radius);
		Wait(2, Ignore Condition);
		Destroy All Effects;
	}
}

rule("------天使复活---------女武神")
{
	event
	{
		Ongoing - Each Player;
		All;
		Mercy;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Damage(Event Player, Null, 100000);
		Wait(0.250, Ignore Condition);
		Resurrect(Event Player);
		Set Status(Event Player, Null, Rooted, 0.750);
		Set Status(Event Player, Null, Invincible, 0.750);
	}
}

rule("提高法鸡溅射伤害")
{
	event
	{
		Player Dealt Damage;
		All;
		Pharah;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
		Attacker != Victim;
		Array Contains(Player Variable(Event Player, I), Victim) == False;
	}

	actions
	{
		Modify Player Variable(Event Player, I, Append To Array, Victim);
		Damage(Victim, Event Player, 15);
		Wait(0.500, Ignore Condition);
		Modify Player Variable(Event Player, I, Remove From Array By Value, Victim);
	}
}

rule("狂鼠左键伤害修正")
{
	event
	{
		Ongoing - Each Player;
		All;
		Junkrat;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
	}

	actions
	{
		Set Damage Dealt(Event Player, 130);
		Wait(1, Ignore Condition);
		Set Damage Dealt(Event Player, 100);
	}
}

rule("狂鼠使用终极技能提高移速")
{
	event
	{
		Ongoing - Each Player;
		All;
		Junkrat;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Event Player, S, True);
		Set Move Speed(Event Player, 120);
	}
}

rule("狂鼠shift伤害")
{
	event
	{
		Player Dealt Damage;
		All;
		Junkrat;
	}

	conditions
	{
		Is Firing Secondary(Event Player) == True;
		Event Damage < 120;
		Player Variable(Event Player, U) == False;
	}

	actions
	{
		Set Player Variable(Event Player, U, True);
		Damage(Victim, Event Player, Divide(Subtract(120, Event Damage), 1.300));
		Wait(1, Ignore Condition);
		Set Player Variable(Event Player, U, False);
	}
}

rule("移速设置回来")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, S) == True;
		Is Using Ultimate(Event Player) == False;
	}

	actions
	{
		Set Move Speed(Event Player, 100);
		Set Player Variable(Event Player, S, False);
	}
}

rule("猎空 终极技能伤害")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Set Damage Dealt(Event Player, 114);
		Wait(1, Ignore Condition);
		Set Damage Dealt(Event Player, 100);
	}
}

rule("麦克雷射速限制")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
	}

	actions
	{
		Disallow Button(Event Player, Primary Fire);
		Wait(0.100, Ignore Condition);
		Allow Button(Event Player, Primary Fire);
	}
}

rule("提高麦克雷右键伤害")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Is Firing Secondary(Event Player) == True;
	}

	actions
	{
		Set Damage Dealt(Event Player, 113);
		Wait(0.500, Ignore Condition);
		Loop If Condition Is True;
		Set Damage Dealt(Event Player, 100);
	}
}

rule("源氏 支持超级跳")
{
	event
	{
		Ongoing - Each Player;
		All;
		Genji;
	}

	conditions
	{
		Is On Wall(Event Player) == True;
		Is Button Held(Event Player, Jump) == True;
		Is Using Ability 1(Event Player) == True;
	}

	actions
	{
		Apply Impulse(Event Player, Add(Up, Forward), 30, To Player, Incorporate Contrary Motion);
	}
}

rule("半藏 音检测")
{
	event
	{
		Ongoing - Each Player;
		All;
		Hanzo;
	}

	conditions
	{
		Is Using Ability 1(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Players Within Radius(Ray Cast Hit Position(Eye Position(Event Player), Add(Eye Position(Event Player),
			Multiply(1000, Facing Direction Of(Event Player))), All Players(All Teams), Event Player, True), 10, Opposite Team Of(Team Of(
			Event Player)), Off), O, True);
	}
}

rule("半藏音检测 显示")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, O) == True;
	}

	actions
	{
		Create Icon(All Players(Opposite Team Of(Team Of(Event Player))), Event Player, Exclamation Mark, Visible To and Position, Red,
			True);
		Set Player Variable(Event Player, P, Last Created Entity);
		Wait(10, Ignore Condition);
		Destroy Icon(Player Variable(Event Player, P));
	}
}

rule("---麦克雷50----数组初始化（a：不可被打断英雄数组，b：可被打断数组，c：延迟性不可打断大招数组，d：可被打断延迟性数组）")
{
	event
	{
		Ongoing - Global;
	}

	actions
	{
		Set Global Variable(A, Empty Array);
		Modify Global Variable(A, Append To Array, Hero(D.Va));
		Modify Global Variable(A, Append To Array, Hero(Bastion));
		Modify Global Variable(A, Append To Array, Hero(Soldier: 76));
		Modify Global Variable(A, Append To Array, Hero(Mercy));
		Modify Global Variable(A, Append To Array, Hero(Ana));
		Modify Global Variable(A, Append To Array, Hero(Baptiste));
		Modify Global Variable(A, Append To Array, Hero(Brigitte));
		Modify Global Variable(A, Append To Array, Hero(Doomfist));
		Modify Global Variable(A, Append To Array, Hero(Winston));
		Modify Global Variable(A, Append To Array, Hero(Genji));
		Modify Global Variable(A, Append To Array, Hero(Wrecking Ball));
		Modify Global Variable(A, Append To Array, Hero(Zenyatta));
		Modify Global Variable(A, Append To Array, Hero(Symmetra));
		Modify Global Variable(A, Append To Array, Hero(Mei));
		Modify Global Variable(A, Append To Array, Hero(Ashe));
		Set Global Variable(B, Empty Array);
		Modify Global Variable(B, Append To Array, Hero(Hanzo));
		Modify Global Variable(B, Append To Array, Hero(Reaper));
		Modify Global Variable(B, Append To Array, Hero(Pharah));
		Modify Global Variable(B, Append To Array, Hero(Lúcio));
		Set Global Variable(C, Empty Array);
		Modify Global Variable(C, Append To Array, Hero(Orisa));
		Modify Global Variable(C, Append To Array, Hero(Zarya));
		Modify Global Variable(C, Append To Array, Hero(Mei));
		Modify Global Variable(C, Append To Array, Hero(Tracer));
		Modify Global Variable(C, Append To Array, Hero(Widowmaker));
		Set Global Variable(D, Empty Array);
		Modify Global Variable(D, Append To Array, Hero(Roadhog));
		Modify Global Variable(D, Append To Array, Hero(Moira));
		Modify Global Variable(D, Append To Array, Hero(Junkrat));
		Modify Global Variable(D, Append To Array, Hero(Reinhardt));
		Modify Global Variable(D, Append To Array, Hero(Torbjörn));
		Modify Global Variable(D, Append To Array, Hero(Sombra));
	}
}

rule("麦克雷英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Wait(0.040, Ignore Condition);
		Set Player Variable(Event Player, B, 1);
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Set Ultimate Charge(Event Player, 98);
		Set Player Variable(Event Player, A, 1);
		Wait(0.020, Ignore Condition);
		Skip If(Compare(Player Variable(Event Player, C), ==, 1), 4);
		Set Ultimate Charge(Event Player, Subtract(Ultimate Charge Percent(Event Player), 2));
		Skip If(Compare(Ultimate Charge Percent(Event Player), <=, 50), 1);
		Loop;
		Set Ultimate Charge(Event Player, 50);
		Set Player Variable(Event Player, A, 0);
		Set Player Variable(Event Player, B, 0);
	}
}

rule("打断")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Or(Has Status(Event Player, Hacked), Or(Has Status(Event Player, Knocked Down), Or(Has Status(Event Player, Asleep), Or(Has Status(
			Event Player, Frozen), Has Status(Event Player, Stunned))))) == True;
	}

	actions
	{
		Set Player Variable(Event Player, C, 1);
	}
}

rule("打断2")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Has Status(Event Player, Asleep) != True;
		Has Status(Event Player, Hacked) != True;
		Has Status(Event Player, Knocked Down) != True;
		Has Status(Event Player, Frozen) != True;
		Has Status(Event Player, Stunned) != True;
	}

	actions
	{
		Set Player Variable(Event Player, C, 0);
	}
}

rule("不可被打断英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
		Array Contains(Global Variable(A), Hero Of(Event Player)) == True;
	}

	actions
	{
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Wait(0.040, Ignore Condition);
		Set Player Variable(Event Player, B, 1);
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Set Ultimate Charge(Event Player, 98);
		Set Player Variable(Event Player, A, 1);
		Wait(0.020, Ignore Condition);
		Set Ultimate Charge(Event Player, Subtract(Ultimate Charge Percent(Event Player), 4));
		Skip If(Compare(Ultimate Charge Percent(Event Player), ==, 0), 1);
		Loop;
		Set Player Variable(Event Player, A, 0);
		Set Player Variable(Event Player, B, 0);
	}
}

rule("不可被打延迟性断英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Array Contains(Global Variable(C), Hero Of(Event Player)) == True;
		Is Button Held(Event Player, Ultimate) == True;
		Ultimate Charge Percent(Event Player) == 0;
	}

	actions
	{
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Wait(0.040, Ignore Condition);
		Set Player Variable(Event Player, B, 1);
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Set Ultimate Charge(Event Player, 98);
		Set Player Variable(Event Player, A, 1);
		Wait(0.020, Ignore Condition);
		Set Ultimate Charge(Event Player, Subtract(Ultimate Charge Percent(Event Player), 4));
		Skip If(Compare(Ultimate Charge Percent(Event Player), ==, 0), 1);
		Loop;
		Set Player Variable(Event Player, A, 0);
		Set Player Variable(Event Player, B, 0);
	}
}

rule("可被打断英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
		Array Contains(Global Variable(B), Hero Of(Event Player)) == True;
	}

	actions
	{
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Wait(0.040, Ignore Condition);
		Set Player Variable(Event Player, B, 1);
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Set Ultimate Charge(Event Player, 98);
		Set Player Variable(Event Player, A, 1);
		Wait(0.020, Ignore Condition);
		Skip If(Compare(Player Variable(Event Player, C), ==, 1), 4);
		Set Ultimate Charge(Event Player, Subtract(Ultimate Charge Percent(Event Player), 4));
		Skip If(Compare(Ultimate Charge Percent(Event Player), ==, 0), 1);
		Loop;
		Set Player Variable(Event Player, A, 0);
		Set Player Variable(Event Player, B, 0);
	}
}

rule("---麦克雷50----可被打断延迟性英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		McCree;
	}

	conditions
	{
		Array Contains(Global Variable(D), Hero Of(Event Player)) == True;
		Is Button Held(Event Player, Ultimate) == True;
		Ultimate Charge Percent(Event Player) == 0;
	}

	actions
	{
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Wait(0.040, Ignore Condition);
		Set Player Variable(Event Player, B, 1);
		Skip If(Compare(Player Variable(Event Player, A), ==, 1), 2);
		Set Ultimate Charge(Event Player, 98);
		Set Player Variable(Event Player, A, 1);
		Wait(0.020, Ignore Condition);
		Skip If(Compare(Player Variable(Event Player, C), ==, 1), 4);
		Set Ultimate Charge(Event Player, Subtract(Ultimate Charge Percent(Event Player), 4));
		Skip If(Compare(Ultimate Charge Percent(Event Player), ==, 0), 1);
		Loop;
		Set Player Variable(Event Player, A, 0);
		Set Player Variable(Event Player, B, 0);
	}
}

rule("路霸回血不能移动 禁止减伤")
{
	event
	{
		Ongoing - Each Player;
		All;
		Roadhog;
	}

	conditions
	{
		Is Using Ability 2(Event Player) == True;
	}

	actions
	{
		Set Status(Event Player, Null, Rooted, 1.500);
		Set Damage Received(Event Player, 200);
		Wait(1.500, Ignore Condition);
		Set Damage Received(Event Player, 100);
	}
}

rule("检测和尚乱标记的目标")
{
	event
	{
		Ongoing - Each Player;
		All;
		Zenyatta;
	}

	conditions
	{
		Is Using Ability 2(Event Player) == True;
	}

	actions
	{
		Set Damage Received(Player Variable(Event Player, E), 100);
		Set Player Variable(Event Player, E, Value In Array(Sorted Array(All Living Players(Opposite Team Of(Team Of(Event Player))),
			Angle Between Vectors(Facing Direction Of(Event Player), Direction Towards(Position Of(Event Player), Position Of(
			Current Array Element)))), 0));
		Set Damage Received(Player Variable(Event Player, E), 125);
		Set Player Variable(Event Player, H, True);
		Set Player Variable(Event Player, G, True);
	}
}

rule("和尚阵亡")
{
	event
	{
		Player Died;
		All;
		Zenyatta;
	}

	conditions
	{
		Player Variable(Event Player, H) == True;
	}

	actions
	{
		Set Damage Received(Player Variable(Event Player, E), 100);
		Set Player Variable(Event Player, H, False);
	}
}

rule("和尚切换英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, H) == True;
		Hero Of(Event Player) != Hero(Zenyatta);
	}

	actions
	{
		Set Damage Received(Player Variable(Event Player, E), 100);
		Set Player Variable(Event Player, H, False);
	}
}

rule("距离太远")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, H) == True;
		Distance Between(Position Of(Event Player), Position Of(Player Variable(Event Player, E))) > 40;
	}

	actions
	{
		Set Damage Received(Player Variable(Event Player, E), 100);
		Set Player Variable(Event Player, H, False);
	}
}

rule("乱标记的玩家阵亡")
{
	event
	{
		Player Died;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, G) == True;
	}

	actions
	{
		Set Damage Received(Event Player, 100);
		Set Player Variable(Event Player, G, False);
	}
}

rule("乱标记离开3秒后撤回")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, H) == True;
		Is In Line of Sight(Event Player, Player Variable(Event Player, E), Barriers Do Not Block LOS) == False;
	}

	actions
	{
		Wait(3, Abort When False);
		Set Damage Received(Player Variable(Event Player, E), 100);
		Set Player Variable(Event Player, H, False);
	}
}

rule("秩序之光传送面板&自瞄")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	actions
	{
		Disallow Button(Event Player, Ultimate);
		Set Player Variable(Event Player, F, Empty Array);
		Set Player Variable At Index(Event Player, F, 0, 0);
		Set Player Variable At Index(Event Player, F, 7, False);
		Set Player Variable At Index(Event Player, F, 10, False);
		Set Player Variable At Index(Event Player, F, 11, 25);
	}
}

rule("秩序之光换英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Hero Of(Event Player) != Hero(Symmetra);
	}

	actions
	{
		Allow Button(Event Player, Ultimate);
	}
}

rule("秩序之光在重生室 记录位置")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Is In Spawn Room(Event Player) == True;
		Value In Array(Player Variable(Event Player, F), 0) == 0;
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 0, Nearest Walkable Position(Position Of(Event Player)));
	}
}

rule("秩序之光传送门 初始")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Is Button Held(Event Player, Ultimate) == True;
		Ultimate Charge Percent(Event Player) == 100;
		Is In Spawn Room(Event Player) == False;
		Value In Array(Player Variable(Event Player, F), 7) == False;
	}

	actions
	{
		Set Ultimate Charge(Event Player, 0);
		Communicate(Event Player, Group Up);
		Set Player Variable At Index(Event Player, F, 1, Nearest Walkable Position(Position Of(Event Player)));
		Set Player Variable At Index(Event Player, F, 2, 6);
		Create Effect(All Players(All Teams), Light Shaft, Blue, Value In Array(Player Variable(Event Player, F), 0), 1,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 3, Last Created Entity);
		Skip If(Compare(Team Of(Event Player), ==, Team 1), 6);
		Create Effect(All Players(All Teams), Good Aura, Team 2, Add(Value In Array(Player Variable(Event Player, F), 1), Vector(0, 1.500,
			0)), 0.750, Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 16, Last Created Entity);
		Create Effect(All Players(All Teams), Sphere, Team 2, Value In Array(Player Variable(Event Player, F), 1), 0.250,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 17, Last Created Entity);
		Create Effect(All Players(All Teams), Ring, Team 2, Value In Array(Player Variable(Event Player, F), 1), 1,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 18, Last Created Entity);
		Skip If(Compare(Team Of(Event Player), ==, Team 2), 6);
		Create Effect(All Players(All Teams), Good Aura, Team 1, Add(Value In Array(Player Variable(Event Player, F), 1), Vector(0, 1.500,
			0)), 0.750, Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 16, Last Created Entity);
		Create Effect(All Players(All Teams), Sphere, Team 1, Value In Array(Player Variable(Event Player, F), 1), 0.250,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 17, Last Created Entity);
		Create Effect(All Players(All Teams), Ring, Team 1, Value In Array(Player Variable(Event Player, F), 1), 1,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 18, Last Created Entity);
		Create HUD Text(Event Player, String("{0} - {1}", String("Remain", Null, Null, Null), Value In Array(Player Variable(Event Player,
			F), 2), Null), Null, Null, Right, 0, White, White, White, Visible To and String, Default Visibility);
		Set Player Variable At Index(Event Player, F, 5, Last Text ID);
		Create Icon(All Players(Team Of(Event Player)), Value In Array(Player Variable(Event Player, F), 0), Arrow: Down,
			Visible To and Position, Blue, False);
		Set Player Variable At Index(Event Player, F, 6, Last Created Entity);
		Set Player Variable At Index(Event Player, F, 7, True);
		Set Player Variable At Index(Event Player, F, 9, 5);
	}
}

rule("秩序之光传送门 修改位置")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Is Button Held(Event Player, Ultimate) == True;
		Ultimate Charge Percent(Event Player) == 100;
		Is In Spawn Room(Event Player) == False;
		Value In Array(Player Variable(Event Player, F), 7) == True;
	}

	actions
	{
		Set Ultimate Charge(Event Player, 0);
		Communicate(Event Player, Group Up);
		Set Player Variable At Index(Event Player, F, 1, Nearest Walkable Position(Position Of(Event Player)));
		Set Player Variable At Index(Event Player, F, 2, 6);
		Set Player Variable At Index(Event Player, F, 9, 5);
	}
}

rule("任意玩家传送")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Is In Spawn Room(Event Player) == True;
		Number of Heroes(Hero(Symmetra), Team Of(Event Player)) > 0;
	}

	actions
	{
		Set Player Variable(Event Player, T, Filtered Array(Players On Hero(Hero(Symmetra), Team Of(Event Player)), Compare(And(
			Value In Array(Player Variable(Current Array Element, F), 7), Compare(Distance Between(Value In Array(Player Variable(
			Current Array Element, F), 0), Position Of(Event Player)), <=, 1)), ==, True)));
		Skip If(Compare(Count Of(Player Variable(Event Player, T)), ==, 0), 3);
		Set Player Variable(Event Player, T, Value In Array(Player Variable(Event Player, T), 0));
		Teleport(Event Player, Nearest Walkable Position(Value In Array(Player Variable(Player Variable(Event Player, T), F), 1)));
		Modify Player Variable At Index(Player Variable(Event Player, T), F, 2, Subtract, 1);
		Wait(0.100, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("传送面板次数归0")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Value In Array(Player Variable(Event Player, F), 7) == True;
		Value In Array(Player Variable(Event Player, F), 2) <= 0;
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 8, True);
	}
}

rule("传送面板生命归0")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Value In Array(Player Variable(Event Player, F), 7) == True;
		Value In Array(Player Variable(Event Player, F), 9) <= 0;
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 8, True);
	}
}

rule("秩序之光切换英雄")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Value In Array(Player Variable(Event Player, F), 7) == True;
		Hero Of(Event Player) != Hero(Symmetra);
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 8, True);
		Set Player Variable At Index(Event Player, F, 10, False);
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 13));
		Set Player Variable At Index(Event Player, F, 12, False);
	}
}

rule("攻击传送面板")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
		Number of Heroes(Hero(Symmetra), Opposite Team Of(Team Of(Event Player))) > 0;
		Hero Of(Event Player) != Hero(Reinhardt);
		Hero Of(Event Player) != Hero(Doomfist);
		Hero Of(Event Player) != Hero(Brigitte);
	}

	actions
	{
		Set Player Variable(Event Player, T, Ray Cast Hit Position(Eye Position(Event Player), Add(Eye Position(Event Player), Multiply(
			1000, Facing Direction Of(Event Player))), All Players(All Teams), Event Player, True));
		Set Player Variable(Event Player, T, Filtered Array(Players On Hero(Hero(Symmetra), Opposite Team Of(Team Of(Event Player))),
			Compare(And(Value In Array(Player Variable(Current Array Element, F), 7), Compare(Distance Between(Value In Array(
			Player Variable(Current Array Element, F), 1), Player Variable(Event Player, T)), <=, 1)), ==, True)));
		Skip If(Compare(Count Of(Player Variable(Event Player, T)), ==, 0), 2);
		Set Player Variable(Event Player, T, Value In Array(Player Variable(Event Player, T), 0));
		Modify Player Variable At Index(Player Variable(Event Player, T), F, 9, Subtract, 1);
		Wait(0.100, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("消除传送面板")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Value In Array(Player Variable(Event Player, F), 8) == True;
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 7, False);
		Set Player Variable At Index(Event Player, F, 8, False);
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 3));
		Destroy Icon(Value In Array(Player Variable(Event Player, F), 6));
		Destroy HUD Text(Value In Array(Player Variable(Event Player, F), 5));
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 16));
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 17));
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 18));
	}
}

rule("其它伤害修正")
{
	event
	{
		Player Dealt Damage;
		All;
		Symmetra;
	}

	conditions
	{
		Victim != Value In Array(Player Variable(Event Player, F), 12);
		Array Contains(Player Variable(Event Player, I), Victim) == False;
	}

	actions
	{
		Modify Player Variable(Event Player, I, Append To Array, Victim);
		Damage(Victim, Event Player, Multiply(9, Event Damage));
		Wait(0.500, Ignore Condition);
		Modify Player Variable(Event Player, I, Remove From Array By Value, Victim);
	}
}

rule("锁定目标")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
		Value In Array(Player Variable(Event Player, F), 10) == False;
	}

	actions
	{
		Set Player Variable At Index(Event Player, F, 14, Sorted Array(Filtered Array(All Living Players(Opposite Team Of(Team Of(
			Event Player))), And(Compare(Distance Between(Position Of(Event Player), Position Of(Current Array Element)), <=, 5),
			Is In Line of Sight(Eye Position(Event Player), Position Of(Current Array Element), Barriers Do Not Block LOS))),
			Angle Between Vectors(Facing Direction Of(Event Player), Direction Towards(Event Player, Current Array Element))));
		Skip If(Compare(Count Of(Value In Array(Player Variable(Event Player, F), 14)), ==, 0), 5);
		Set Player Variable At Index(Event Player, F, 11, 25);
		Set Player Variable At Index(Event Player, F, 10, True);
		Set Player Variable At Index(Event Player, F, 12, First Of(Value In Array(Player Variable(Event Player, F), 14)));
		Create Beam Effect(All Players(All Teams), Bad Beam, Event Player, Value In Array(Player Variable(Event Player, F), 12), Blue,
			Visible To Position and Radius);
		Set Player Variable At Index(Event Player, F, 13, Last Created Entity);
		Wait(0.250, Ignore Condition);
		Loop If Condition Is True;
	}
}

rule("锁定目标后 持续伤害")
{
	event
	{
		Ongoing - Each Player;
		All;
		Symmetra;
	}

	conditions
	{
		Is Firing Primary(Event Player) == True;
		Value In Array(Player Variable(Event Player, F), 10) == True;
		Is Alive(Value In Array(Player Variable(Event Player, F), 12)) == True;
		Is In Line of Sight(Eye Position(Event Player), Position Of(Value In Array(Player Variable(Event Player, F), 12)),
			Barriers Do Not Block LOS) == True;
		Distance Between(Position Of(Event Player), Position Of(Value In Array(Player Variable(Event Player, F), 12))) <= 7;
	}

	actions
	{
		Damage(Value In Array(Player Variable(Event Player, F), 12), Event Player, Multiply(10, Value In Array(Player Variable(
			Event Player, F), 11)));
		Modify Player Variable At Index(Event Player, F, 11, Add, 25);
		Wait(0.500, Ignore Condition);
		Loop If Condition Is True;
		Set Player Variable At Index(Event Player, F, 10, False);
		Destroy Effect(Value In Array(Player Variable(Event Player, F), 13));
		Set Player Variable At Index(Event Player, F, 12, False);
	}
}

rule("安娜激素选择目标")
{
	event
	{
		Ongoing - Each Player;
		All;
		Ana;
	}

	conditions
	{
		Is Using Ultimate(Event Player) == True;
	}

	actions
	{
		Set Player Variable(Value In Array(Sorted Array(All Living Players(Team Of(Event Player)), Angle Between Vectors(
			Facing Direction Of(Event Player), Direction Towards(Position Of(Event Player), Position Of(Current Array Element)))), 0), H,
			True);
	}
}

rule("安娜激素加速")
{
	event
	{
		Ongoing - Each Player;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, I) == True;
	}

	actions
	{
		Set Move Speed(Event Player, 130);
		Wait(8, Abort When False);
		Set Move Speed(Event Player, 100);
		Set Player Variable(Event Player, H, False);
	}
}

rule("阵亡取消激素加速")
{
	event
	{
		Player Died;
		All;
		All;
	}

	conditions
	{
		Player Variable(Event Player, H) == True;
	}

	actions
	{
		Set Player Variable(Event Player, H, False);
		Set Move Speed(Event Player, 100);
	}
}

rule("--------------守望先锋怀旧服 V1.4   作者 misaka#51577 https://owmod.net/413--------------------------------")
{
	event
	{
		Ongoing - Global;
	}
}
```
