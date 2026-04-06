<h2>ФУНКЦИИ:</h2>

- 🎁 Открытие подарков
- 👆 Клики (225.000 клик/ч - 49.500.000 клик/ч)
- 💬 Отправку эмодзи
- 📦 Крафт из 10 дубликатов

<h2>УСТАНОВКА:</h2>

<h3>
	
- Быстрая:</h3>
1. Скачайте готовый файл Assembly-CSharp.dll.
2. Скопируйте его в папку .../BongoCat/BongoCat_Data/Managed/ с заменой.
3. Запустите игру.
<br>
<h3>
	
- Ручная (с настройкой под себя):</h3>

> [!CAUTION]
**>Данные действия нужно проделывать каждый раз, когда Steam обновляет игру (а именно сам файл Assembly-CSharp.dll).**

1. Скачайте и распакуйте dnSpy: https://github.com/dnSpyEx/dnSpy/releases/download/v6.5.1/dnSpy-net-win64.zip
2. Запустите dnSpy.exe.
3. Откройте в редакторе файл игры Assembly-CSharp.dll (находится в папке .../BongoCat/BongoCat_Data/Managed/).
4. В панели слева разверните дерево: Assembly-CSharp -> Assembly-CSharp.dll. Далее выберите то, что вам нужно отредактировать.
5. Чтобы изменить код: нажмите правой кнопкой мыши на нужный класс и выберите "Edit Class (C#)...".
6. После внесения изменений нажмите кнопку "Compile" (Скомпилировать).
7. Важно: Чтобы сохранить изменения в файл, нажмите в верхнем меню File -> Save Module... и подтвердите сохранение (OK).
8. Закройте dnSpy и запустите игру.

<br><br><br>
<h1 align="center">АВТОСУНДУК</h1>

>Механика: по нажатию Numpad 5 меняется режим клика: "одна рандомная клавиша" (225.000 клик/ч) или "220 клавиш" (все, которые знает игра - 49.500.000 клик/ч). Интервал между кликами настраивается в разделе «ТАЙМИНГ АВТОКЛИКА».
<p>Добавить строку в BongoCat > Shop > TimerUpdate:</p>

```
this._shopItem.Buy();
```
<p>В коде:</p>
<img width="1300" height="730" alt="image" src="https://github.com/user-attachments/assets/94e16db3-55fd-47a6-8415-0b14e737d84d" />
<br><br><br>
			
<h1 align="center">АВТОКЛИК</h1>
<p>Добавить строки в GlobalKeyHook > WinKeyHook > ProcessInput:</p>

```
public static bool ActivateAllMode = false;
```
```
if (GetAsyncKeyState(0x65) != 0)
{
	System.Threading.Thread.Sleep(150);
	ActivateAllMode = !ActivateAllMode;
}
```
```
if (ActivateAllMode)
{
	for (int i = 0; i < WinKeyHook.IsDown.Length; i++)
		WinKeyHook.IsDown[i] = true;
}
else
{
	int num = UnityEngine.Random.Range(0, WinKeyHook.BUTTONS.Length);
	WinKeyHook.IsDown[num] = true;
}
```
<p>В коде:</p>
<img width="1300" height="964" alt="image" src="https://github.com/user-attachments/assets/49e8e6fe-7f5e-42e5-853d-d894c567acbd" />
<p align="center">╚═══ ТАЙМИНГ АВТОКЛИКА ═══╝</p>

>НЕОБЯЗАТЕЛЬНО: по умолчанию клики идут с интервалом проверки игры в 16 мс. Если ваша цель не фарм достижений, а подарки - рекомендую увеличить интервал и добавить рандом (симуляция ввода). Ниже в коде настроен случайный интервал 110–1100 мс: этого достаточно, чтобы открывать по два подарка каждые полчаса в режиме "одна рандомная клавиша".
<p>Добавить строки в BongoCat.OSSpecific > GlobalKeyHook > Process:</p>

```
int randomInterval = new System.Random().Next(110, 1100);
this._timer.Change(randomInterval, randomInterval);
```
<p>В коде:</p>
<img width="1300" height="730" alt="image" src="https://github.com/user-attachments/assets/dfda5a8a-7708-4311-a4cf-51a2af67a116" />
<br><br><br>

<h1 align="center">ЭМОДЗИ</h1>

>По умолчанию в коде задан случайный интервал от 30 до 300. Частота отправки напрямую зависит от вашего FPS в игре, так как проверка условий происходит каждый кадр.
Формула расчета: Число из интервала / Ваш FPS = Задержка в секундах

>Пример (при 60 FPS):  
      Минимум (30): $30 / 60 = 0.5$ сек.  
      Максимум (300): $300 / 60 = 5$ сек.  
>Итог: Эмодзи будут отправляться рандомно каждые 0.5–5 секунд. Если ваш FPS ниже 60, реальное время изменится. Вы можете изменить эти значения под себя, а также значение FPS в самой игре.
<p>Добавить строки в - > EmoteDonut > Update:</p>

```
if (this.IDtoEmoteEntry.Count > 0)
{
    int counter = PlayerPrefs.GetInt("AutoClickCounter", -1);
    if (counter < 0)
    {
        counter = UnityEngine.Random.Range(30, 300);
    }
    counter--;
    PlayerPrefs.SetInt("AutoClickCounter", counter);
    
    if (counter < 0)
    {
        var entries = new System.Collections.Generic.List<EmoteDonutEntry>(this.IDtoEmoteEntry.Values);
        if (entries.Count > 0)
        {
            var randomEmote = entries[UnityEngine.Random.Range(0, entries.Count)];
            randomEmote.SpawnEmoteParticle();
        }
    }
}
```
<p>В коде:</p>
<img width="1300" height="730" alt="image" src="https://github.com/user-attachments/assets/94381c07-9ae6-421b-b3ae-32b85d8be374" />
<br><br><br>

<h1 align="center">АВТОКРАФТ</h1>

>Механика: процесс крафта активируется автоматически при открытии вкладки "Обмен". Вам не нужно нажимать дополнительные кнопки — скрипт сработает сам, как только вы перейдете в соответствующий раздел меню.
<p>Добавить строки в BongoCat > ItemExchange > OnOpenExchange:</p>

```
base.StartCoroutine(((Func<IEnumerator>)(() => {
    IEnumerator routine() {
        while (this.HasDuplicates()) {
            this.SlotDuplicates();
            this.UpdateInteractable();
            this.TryExchange();
            yield return new WaitUntil(() => this._exchangeCompleted);
            this._exchangeCompleted = false;
        }
    }
    return routine();
}))());
```
<p>В коде:</p>
<img width="1300" height="730" alt="image" src="https://github.com/user-attachments/assets/62f65ddc-1156-4cc4-9fcd-3609a730ad68" />

