<h2>ФУНКЦИИ:</h2>

- 🎁 Открытие подарков
- 👆 Клики
- 💬 Отправку эмодзи
- 📦 Крафт из 10 дубликатов

<h2>УСТАНОВКА:</h2>

- Быстрая: 
1. Скачайте готовый файл Assembly-CSharp.dll.
2. Скопируйте его в папку .../BongoCat/BongoCat_Data/Managed/ с заменой.
3. Запустите игру.
<br>

- Ручная (с настройкой под себя):

> [!CAUTION]
>Данные действия нужно проделывать каждый раз, когда Steam обновляет игру (а именно сам файл Assembly-CSharp.dll).

1. Скачайте и распакуйте dnSpy.
2. Запустите dnSpy.exe.
3. Откройте в редакторе файл игры Assembly-CSharp.dll (находится в папке .../BongoCat/BongoCat_Data/Managed/).
4. В панели слева разверните дерево: Assembly-CSharp -> Assembly-CSharp.dll. Далее выберите то, что вам нужно отредактировать.
5. Чтобы изменить код: нажмите правой кнопкой мыши на нужный класс и выберите "Edit Class (C#)...".
6. После внесения изменений нажмите кнопку "Compile" (Скомпилировать).
7. Важно: Чтобы сохранить изменения в файл, нажмите в верхнем меню File -> Save Module... и подтвердите сохранение (OK).
8. Закройте dnSpy и запустите игру.

<br><br><br>
<h1 align="center">АВТОСУНДУК</h1>
<p>Добавить строку в BongoCat > Shop > TimerUpdate:</p>

```
this._shopItem.Buy();
```
<p>В коде:</p>

```
private IEnumerator TimerUpdate()
{
	for (;;)
	{
		if (this._outOfStockObj.activeSelf)
		{
			this.StockRefreshTimeLeft--;
			PlayerPrefs.SetInt(this._shopTimeKey, this.StockRefreshTimeLeft);
			this._stockRefreshText.text = string.Format("{0:mm':'ss}", TimeSpan.FromSeconds((double)this.StockRefreshTimeLeft));
			SteamItemDetails_t chestToken = this._isEmoteShop ? CatInventory.Instance.EmoteChestToken : CatInventory.Instance.ChestToken;
			if (this.StockRefreshTimeLeft <= 0)
			{
				if (chestToken.m_unQuantity == 0)
				{
					this.StockRefreshTimeLeft = 60;
				}
				else
				{
					this.StockRefreshTimeLeft = 0;
					this._shopItem.gameObject.SetActive(true);
					this._outOfStockObj.SetActive(false);
					this.ChestIsReady = true;
					if (this._showChestPopup.Value && this._shopItem.CanBuy())
					{
						if (!this._isEmoteShop)
						{
							SteamMultiplayer.Instance.SendChestReady(this.ChestIsReady);
						}
						this._shopVisuals.SetActive(true);
					}
					this._shopItem.Buy();
				}
			}
		}
		else if (this.StockRefreshTimeLeft <= 0 && !this._shopVisuals.activeInHierarchy && this._showChestPopup.Value && this._shopItem.CanBuy())
		{
			if (!this._isEmoteShop)
			{
				SteamMultiplayer.Instance.SendChestReady(this.ChestIsReady);
			}
			this._shopVisuals.SetActive(true);
		}
		yield return new WaitForSecondsRealtime(1f);
	}
	yield break;
}
```
<br><br><br>


			
<h1 align="center">АВТОКЛИК</h1>
<p>Добавить строки в GlobalKeyHook > WinKeyHook > ProcessInput:</p>

```
int num = UnityEngine.Random.Range(0, WinKeyHook.BUTTONS.Length);
WinKeyHook.IsDown[num] = true;
```
<p>В коде:</p>

```
public int ProcessInput(bool ignoreMouse)
{
	for (int i = 0; i < WinKeyHook.BUTTONS.Length; i++)
	{
		WinKeyHook.IsDown[i] = false;
		if (!ignoreMouse || (WinKeyHook.BUTTONS[i] != 1 && WinKeyHook.BUTTONS[i] != 2 && WinKeyHook.BUTTONS[i] != 4 && WinKeyHook.BUTTONS[i] != 6 && WinKeyHook.BUTTONS[i] != 5))
		{
			short val = WinKeyHook.GetAsyncKeyState(WinKeyHook.BUTTONS[i]);
			if (!WinKeyHook.WasPressed[i] && val == -32768)
			{
				WinKeyHook.WasPressed[i] = true;
				WinKeyHook.IsDown[i] = true;
			}
			else if (WinKeyHook.WasPressed[i] && val == 0)
			{
				WinKeyHook.WasPressed[i] = false;
			}
		}
	}
	int num = UnityEngine.Random.Range(0, WinKeyHook.BUTTONS.Length);
	WinKeyHook.IsDown[num] = true;
	if (!WinKeyHook.AnyButtonPressed())
	{
		return 0;
	}
	return WinKeyHook.IsDown.Count((bool x) => x);
}
```
<p align="center">╚═══ ТАЙМИНГ АВТОКЛИКА ═══╝</p>

	
>НЕОБЯЗАТЕЛЬНО. Если не добавлять эту часть, клики будут происходить с интервалом, с которым игра проверяет нажатия клавиш, а именно — 16 мс (что эквивалентно 225 000 кликов в час). Я рекомендую увеличить этот интервал и добавить немного рандома. В моём коде по умолчанию настроен случайный интервал от 110 до 1100 мс. Этого более чем достаточно, чтобы без проблем открывать по два подарка каждые полчаса. Вы можете изменить эти значения под себя.

	
<p>Добавить строки в BongoCat.OSSpecific > GlobalKeyHook > Process:</p>

```
int randomInterval = new System.Random().Next(110, 1100);
this._timer.Change(randomInterval, randomInterval);
```
<p>В коде:</p>

```
private void Process()
{
	this.ProcessControllerInput();
	this._keysDown += this._platformHook.ProcessInput(this._ignoreMouse.Value);
	int randomInterval = new System.Random().Next(110, 1100);
	this._timer.Change(randomInterval, randomInterval);
}
```
<br><br><br>



<h1 align="center">ЭМОДЗИ</h1>

>По умолчанию в коде задан случайный интервал от 30 до 300. Частота отправки напрямую зависит от вашего FPS в игре, так как проверка условий происходит каждый кадр.
Формула расчета: Число из интервала / Ваш FPS = Задержка в секундах  

>Пример (при 60 FPS):  
Минимум (30): $30 / 60 = 0.5$ сек.  
Максимум (300): $300 / 60 = 5$ сек.  

>Итог: Эмодзи будут отправляться рандомно каждые 0.5–5 секунд. Если ваш FPS ниже 60, реальное время изменится.  
Вы можете изменить эти значения под себя.


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

```
private void Update()
{
	bool shouldShow = false;
	Vector2 mousePos = Input.mousePosition;
	foreach (object obj in this.emoteEntryParent)
	{
		Transform child = (Transform)obj;
		Vector2 childScreenPos = RectTransformUtility.WorldToScreenPoint(null, child.position);
		if (Vector2.Distance(mousePos, childScreenPos) < this._interactionDistance * this._scaleSetting.GetScale())
		{
			shouldShow = true;
			break;
		}
	}
	if (shouldShow)
	{
		this.Open();
		return;
	}
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
			List<EmoteDonutEntry> entries = new List<EmoteDonutEntry>(this.IDtoEmoteEntry.Values);
			if (entries.Count > 0)
			{
				entries[UnityEngine.Random.Range(0, entries.Count)].SpawnEmoteParticle();
			}
		}
	}
	this.Close();
}
```
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

```
public void OnOpenExchange()
		{
			if (!SteamManager.s_EverInitialized)
			{
				return;
			}
			this.IsVisible = true;
			List<SteamItem> duplicates = this.GetDuplicates();
			QualityCategoryWithInfo slotDuplicateQuality = this.GetSlotDuplicateQuality(duplicates);
			this._fillDuplicatesFill.color = this._qualityColors.GetColor(slotDuplicateQuality.ToQuality());
			CatInventory.Instance.UpdateItemsUI();
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
		}
```
