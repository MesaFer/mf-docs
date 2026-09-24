# Changelog — MF_SimpleVisual / MF_SimpleVisualEditor

Формат: [Keep a Changelog](https://keepachangelog.com/ru/1.1.0/), версии — [semver](https://semver.org/lang/ru/). Правила — как у MF_Core (`docs/MF_Core/CHANGELOG.md`).

## Правила

Каждое изменение публичного API **и формата `data/SimpleVisual.json`** записывается сюда в том же коммите.

| Метка | Значение | Где допустимо |
|---|---|---|
| **Added** | Новое API / свойство / тип действия / тип элемента | MINOR |
| **Changed** | Совместимое изменение поведения | MINOR |
| **Deprecated** | Старое имя работает, перенаправляется на новое, одно предупреждение; указаны `since` и `removeIn` | MINOR |
| **Removed** ⚠ BREAKING | Удаление того, что было Deprecated | MAJOR (до 1.0.0 — только в 1.0.0) |
| **Breaking** ⚠ BREAKING | Несовместимое изменение | MAJOR |
| **Experimental change** | Изменение API с пометкой `[experimental]` | MINOR |
| **Fixed** | Исправление без изменения API | PATCH |

Стабильное API: `reload`, `applyScene`, `layoutFor`, `registerAction`, `Settings`, `Hud`, `openCustomScene`, `setPreset`, `PresetIO.exportPreset/readPreset`, `Deprecated`, `VERSION`, `FORMAT_VERSION`, а также все свойства файла, действия и типы элементов из документации. Остальные члены `MF.SimpleVisual` (`LayoutStore`, `WindowPatcher`, `ElementFactory`, `Template`, `Dyn`…) — `[experimental]`, их использует редактор.

### Жизненный цикл устаревшего

1. **Замена** (MINOR): добавлено новое имя, старое работает через `MF.SimpleVisual.Deprecated`:
   ```js
   Deprecated.method(MF.SimpleVisual, "oldName", { since: "0.9.0", removeIn: "1.0.0", replacement: "MF.SimpleVisual.newName", fn: MF.SimpleVisual.newName });
   Deprecated.renameProp("element", "oldKey", "newKey", { since: "0.9.0", removeIn: "1.0.0" });
   Deprecated.aliasAction("oldType", "newType", { since: "0.9.0", removeIn: "1.0.0" });
   Deprecated.aliasElementType("oldType", "newType", { since: "0.9.0", removeIn: "1.0.0" });
   ```
   Свойства файла переименовываются при загрузке; редактор сохраняет файл уже с новыми именами.
2. **Объявление**: запись в **Deprecated** с `since` / `removeIn`.
3. **Удаление**: только в указанной версии, запись в **Removed**. Всё устаревшее в 0.x живёт минимум до 1.0.0.

Формат файла (`"version"`): несовместимое изменение структуры поднимает `FORMAT_VERSION` и добавляет миграцию `vN → vN+1` (`MF.Migration`). Файл более нового формата загружается с предупреждением.

### Зависимые плагины

```js
MF.Core.register("MF_MyPlugin", "1.0.0", { requires: { MF_Core: "1.1.0", MF_SimpleVisual: "0.8.0" } });
```
В `@help`: `Requires: MF_SimpleVisual 0.8.0+ (written against 0.8.0)`, `@base MF_SimpleVisual`, `@orderAfter MF_SimpleVisual`.

---

## [Unreleased]

### Added
- Лицензия `LICENSE.md` (MF Plugins License) и заголовки лицензии в `MF_Core`, `MF_SimpleVisual`, `MF_SimpleVisualEditor` (комментарий + раздел License в `@help`).
- Editor: вне тест-игры и вне проекта (нет `game.rmmzproject`, NW.js) файл редактора заменяет себя пустой заглушкой. `tools/SimpleVisual/strip_editor.js <папка сборки> [--dry]` — заглушка + `status: false` в `js/plugins.js` сборки.
- Политика версий и устаревания как у MF_Core (`@help`, раздел «Versioning policy»); разделение API на стабильное и `[experimental]`.
- `MF.SimpleVisual.Deprecated`: `warn`, `method`, `property`, `renameProp`, `aliasAction`, `aliasElementType`, `upgradeLayout`, `list`, `rules`. Переименованные свойства обновляются при загрузке файла и импорте пресетов; устаревшие типы действий выполняются под новым именем.
- HTML-документация как у MF_Core: `docs/SimpleVisual/index.html` (справочник формата и API, поиск), `getting-started.html` (быстрый старт и пример сцены).

- Элемент `particles` — частицы в прямоугольнике элемента: пресеты `rain`, `snow`, `embers`, `fireflies`, `stars`; свои `count`, `shape` (`dot` / `streak`) или `image`, `color`, `size*`, `speed*`, `angle`, `spread`, `opacity*`, `spawn` (`top` / `bottom` / `area`), `life*`, `sway`, `wind`, `gravity`, `spin`, `flicker`, `pulse`, `fade`, `alignToMotion`, `blend: "add"`, `mouseRepel` (радиус, частицы разлетаются от курсора). Числа — формулы. В редакторе частицы замирают. Демо: звёзды, падающие звёзды и светлячки на титуле, искры в меню, дождь в «Витрине» при включённом переключателе №2.
- Картинки с подстановками грузятся вне кэша MZ: нет файла — пустой элемент, а не экран «Failed to load». Поле героя `levelExp`.
- Токены карты `{player.…}`, `{event.<id>.…}`, `{tile.<x>.<y>.…}` (`MapTokens`): клетка, плавные координаты, центр на экране (`uiX/uiY` — как у элементов, `screenX/Y` — холст), `dist`, `angle` от игрока, `onScreen`, `regionId`, `passable`. Пересчёт каждый кадр. Editor: раздел «Карта: игрок, события, клетки» в «Вставить значение».
- Демо: компас в HUD на событие 1, маркер над событием и стрелка на краю экрана, когда событие за кадром.
- Демо: windowskin `img/system/SV_Window.png` (`tools/SimpleVisual/make_windowskin.py`), параллакс титула, стрелка к курсору.
- `picture.image` элементов принимает значения игры: `"pictures/{hero.faceName}_{hero.faceNumber}"` (перезагрузка при смене).
- Демо-сцены (главное меню, `tools/SimpleVisual/add_demo_scenes.js`):
  - «Отряд» (`Custom_Party`) — карточки героев с портретом (насыщенность = % HP), лист персонажа: большой портрет, EXP, столбцы параметров шириной по значению.
  - «Сумка» (`Custom_Bag`) — сетка 6×N иконок со счётчиком, мигающая выбранная ячейка, крупная иконка через обрезку `IconSet` по `iconIndex`.
  - «Задания» (`Custom_Quests`) — статический список `data`, прогресс в строках и полоса в панели, печать «выполнено».
  - «Витрина» (`Custom_Showcase`) — цветокоррекция по времени и при наведении, стрелка за мышью, переменная с кнопками ± и `enabled`, переключатель с двумя состояниями по `condition`, живые значения, орбитальная анимация формулами.
  - «Герои» (`Custom_Hero`) — экран выбора героя для превью: параллакс, плавающие карточки с подъёмом и зумом при наведении, вращающиеся гербы, «дышащие» полосы параметров, искры, светлячки от курсора, золотая кнопка.
- `windows.<поле>.commands.<ключ>.action` — действие вместо стандартного обработчика команды (любой `Window_Command`: титул, главное меню, опции…), как у кнопок. `commands.<ключ>.enabled` — условие доступности команды.
- Editor: «Редактировать команды» главного меню — «+ пункт» создаёт пункт `menuCommands` (после выбранного), у своих пунктов — название, действие (⚡), `enabled`, удаление; у стандартных — `action` и `enabled`. Список окна перестраивается сразу.
- Editor: раздел «Проект» — карточки вместо JSON: пункты главного меню (название, symbol, позиция после/перед, действие, условия, порядок, удаление), темы (id с переименованием и обновлением `extends`, название, `extends`, windowskin/шрифт через выбор, стиль, дублирование, удаление), пресеты (переименование, дублирование, удаление). Сырой JSON — в свёрнутом блоке.

### Changed
- Обработчики `menuCommands` читают действие в момент выбора: изменённое или добавленное в редакторе действие работает без переоткрытия меню.

### Docs
- GIF перенесены в `docs/SimpleVisual/assets/gifs/` (пережаты ffmpeg, 1–4 МБ), список записей — `assets/gifs/README.md`; `media/` и `image/GIFS/` удалены. `publish_docs.js` публикует `assets/` без `.md`.

### Deprecated
- `MF.UIEditor` → `MF.SimpleVisual`, `$dataUILayouts` → `$dataSimpleVisual` (since 0.1.0, removeIn 1.0.0).
- Корень файла `"layouts"` → `"scenes"` (миграция формата v0, теперь с предупреждением; removeIn 1.0.0).

## [0.8.0] + Editor [0.7.0] — 2026-09-23

Этап 7: совместимость и полировка. Формат файла остаётся v1. Редактор 0.7.0 требует `MF_SimpleVisual 0.8.0`.

### Added
- `scenes.<X>.skip` / `force`, параметры `skipScenes` и `autoCompat` (модуль `Compat`, `KNOWN_PLUGINS`: VisuMZ, CGMZ, AltMenuScreen, AltSaveScreen). Пропуск сцены — сообщение в консоли один раз.
- `scaleMode: "scale"` и `scaleFonts` — масштаб пиксельных значений от `baseResolution` к текущему размеру (модуль `Resolution`).
- Миграция v0 → v1 (`layouts` → `scenes`).
- Файлы пресетов `data/SimpleVisualPresets/<name>.json` (`PresetIO`); редактор: «Проект → Пресеты» — включить в игре, экспорт, импорт, JSON.
- Пресеты `altMenu`, `altSave` в демо-файле.
- Документация: `USER_GUIDE.md`, `USER_GUIDE.en.md`, `COMPATIBILITY.md`.
- Демо: новый титульный экран, меню, бестиарий, HUD карты, бой; картинки `img/pictures/SV_*.png` из SVG (`tools/SimpleVisual/make_demo_art.py`, исходники в `docs/SimpleVisual/demo_art/`).

## [0.7.0] + Editor [0.6.0] — 2026-09-23

Этап 6: боевой экран и HUD. Формат файла остаётся v1. Редактор 0.6.0 требует `MF_SimpleVisual 0.7.0`.

### Added
- Бой: `_statusWindow` с раскладкой «отъезжает» (пока команда не выбрана) относительно новой позиции, а не к стандартной — алиас `Scene_Battle.statusWindowX`. `"slide": false` — окно не двигается. В редакторе поле `slide` у `_statusWindow` в `Scene_Battle`.
- Бой: `_actorWindow` и `_enemyWindow` без своей геометрии берут геометрию `_statusWindow` (`BattleLayout.derive`) — окна выбора цели остаются поверх статуса.
- Адаптер `Window_BattleStatus` / `Window_BattleActor`: `template` строк с полями героя; шаблон по умолчанию повторяет вид MZ (лицо, состояние, имя, шкалы). Стандартные спрайты MZ скрываются при заданном `template`.
- Дочерний тип шаблона `actorState` — анимированная иконка состояний MZ (`Sprite_StateIcon`); в превью — первая иконка статично.
- Вложенные элементы (`parent: "w:…"`) появляются и исчезают вместе с анимацией `open` / `close` окна (прозрачность ∝ `openness`), а не только после полного открытия; учитывается и цепочка родителей-элементов.
- Спрайты `_cancelButton` (бой) и `_menuButton` (карта) в `sprites`; `visible: false` удерживается, хотя сцена показывает кнопки каждый кадр.
- HUD (`"hud": true`): `hideOnMessage` (по умолчанию `true` на карте), `hideOnEvent`, `fadeFrames` (плавное появление / скрытие, 12 кадров), `fadeUnderPlayer` (прозрачность, пока игрок за элементом). HUD не попадает в снимок карты для фона меню и боя, скрыт во время эффекта встречи. Команды плагина `ShowHud` / `HideHud` (весь HUD или один `id`, хранится в сохранении), API `MF.SimpleVisual.Hud`.
- Общая раскладка карты и боя — `scenes.Scene_Message` (родительский класс обеих сцен).
- Пример: HUD на `Scene_Map` (`hud: true`, `fadeUnderPlayer`), шаблон `Scene_Battle._statusWindow` и подсказка над `_actorCommandWindow`.
- Состояния элементов: `hover`, `pressed` (поверх `hover`), `disabled` — объекты с переопределениями собственных свойств элемента (`textColor`, `fill`, `opacity`, `brightness`, `zoom`…). Проверяются по схеме типа; структурные ключи (`id`, `items`, `action`…) игнорируются. Модуль `States` (кэш по объекту состояния), применение только при смене состояния.
- `enabled` (условие) у всех элементов: недоступная кнопка не выполняет действие и играет `disabledSound` (по умолчанию «ошибка»), без своего `disabled` — затемняется как пункты MZ; недоступные `command` / `list` не получают фокус и снимаются с активности.
- `blink` (секунд на цикл) и `blinkMin` (наименьшая непрозрачность) — мигание элемента; удобно внутри состояния. `blinkTarget`: `background` (по умолчанию у окон / кнопок — текст не мигает), `content`, `all` (по умолчанию у картинок / фигур). Множитель накладывается на `opacity` / `contentsOpacity` MZ, а не заменяет их.
- Звуки: `hoverSound`, `clickSound`, `disabledSound` (`"Cursor1"`, `{ name, volume, pitch, pan }`, `"none"` — тишина). У `command` / `list` — звук курсора, OK и «ошибки».
- Свой фон окон `window` / `text` / `button` / `command` / `list`: `fill`, `fill2`, `gradient`, `borderColor`, `borderWidth`, `radius` — вместо рамки windowskin (можно менять в состояниях).
- `command` / `list`: `rowHover` — выбранная строка (фон-фигура, `opacity`, `blink`, `cursor: true` — оставить курсор окна), `rowDisabled` — недоступные строки (`opacity`, свой фон, `background: false`); у `list` — `rowEnabled` (условие или выражение с полями строки: `"{count} > 0"`). В шаблонах строк — поля `{selected}` и `{enabled}` (строки перерисовываются при смене выбора).
- Editor: окно «🎛 Состояния…» — вкладки состояний и строк, пресеты, добавление / удаление свойств, «Показать на сцене» (принудительное состояние для превью), конструктор условия `enabled`. Поля звуков с подсказками файлов из `audio/se` и кнопкой «♪».

## [0.6.0] + Editor [0.5.0] — 2026-09-23

Картинки внутри рамки, цветокоррекция, мышь и сам объект в выражениях. Формат файла остаётся v1.

### Changed
- **Требуется MF_Core 1.1.0+** (функции и условия в выражениях). `MF_SimpleVisual` 0.6.0: `requires: { MF_Core: "1.1.0" }`; редактор 0.5.0: `MF_Core 1.1.0`, `MF_SimpleVisual 0.6.0`. С MF_Core 1.0.0 `MF.Core.register` выдаст понятную ошибку.

### Added
- Выражения (`MF.Units`, MF_Core 1.1.0): функции `min max abs sign floor ceil round trunc sqrt pow exp log mod clamp lerp step between if hypot dist sin cos tan asin acos atan atan2 sind cosd tand atan2d angle deg rad`, константы `pi`, `e`, операторы `^`, `< > <= >= == !=`, `&& || !`, `? :`. По-прежнему без eval. В «Вставить значение» — раздел «Функции и операторы».
- Токены объекта: `{self.…}` — сам элемент / окно, чьё свойство считается; `{el.<id>.…}` — любой элемент сцены. Поля: `x`, `y`, `width`, `height`, `centerX/Y`, `right`, `bottom`, `rotation`, `opacity`, `visible`, `hover`, `down`, `relX/Y`, `clicks`, `hoverTime`, `time`, `parentWidth/Height`. Геометрия — с прошлой раскладки. Свойства с ними пересчитываются каждый кадр. В «Вставить значение» — разделы «Этот элемент / окно» и «Элементы сцены»; живой результат в панели считается для выбранного элемента.
- Токены мыши `{mouse.…}`: `x`, `y`, `uiX`, `uiY`, `left`, `right`, `middle`, `pressed`, `dragging`, `dragX/Y`, `dragStartX/Y`, `clickX/Y`, `clicks`, `rightClicks`, `wheel`, `idle`; по элементу — `over.<id>`, `down.<id>`, `relX.<id>`, `relY.<id>` (0–100 %). Свойства с `{mouse.…}` пересчитываются каждый кадр (остальные — раз в 10). В «Вставить значение» — раздел «Мышь».
- picture: цветокоррекция — `brightness`, `contrast`, `saturation`, `grayscale`, `sepia`, `hue`, `invert`, `blur` (CSS filter) и `tint` + `tintAmount` (тонирование только по картинке, через отдельный canvas). Все числа — формулы, `{…}`, `\V[n]`, в шаблонах строк ещё и поля строки (`{hpRate}`). Работает и у элементов, и у картинок шаблонов списков. В окне настройки — группа «Цветокоррекция» с ползунками и пресетами (Ч/Б, сепия, негатив, ночь…).
- picture (элементы и дети шаблонов строк): картинка внутри рамки — `offsetX/Y`, `zoom`, `imageRotation` (внутренний поворот с обрезкой по рамке), `tile` (`repeat`/`repeatX`/`repeatY`), `crop*`, `flipX/Y`, `smooth`, `scrollX/Y` (элементы), `fit: cover/none`. Все числовые поля принимают формулы и `{…}` / `\V[n]`. Общий рисовальщик `PictureDraw`, запекание в Bitmap размера рамки (`svBakeImage`); без новых свойств используется прежний быстрый путь.
- Editor: окно «🖼 Настройка изображения…» (`PictureEditor`) — интерактивное: превью с «призраком» обрезанной части и контуром картинки; тянуть — сдвиг (Shift — по оси), круглая ручка — поворот (Shift — по 15°), колесо — масштаб, двойной клик — центр; ползунки и «перетаскиваемые» названия полей; кнопки вписывания/тайлинга, сетка выравнивания 3×3, повороты ±90°, отражения; обрезка рамкой на исходной картинке. У каждого поля `{…}` и живой результат формул; поля с формулами мышью не меняются. Один жест = один шаг undo.
- Editor: кнопка «✎ Переименовать» в свойствах элемента (`Editor.renameElement`). Меняет `id`, обновляет `parent` вложенных элементов и `initialFocus` сцены; один шаг undo. Унаследованные из базовых раскладок элементы переименовываются только там. Ссылки в выражениях `{id.field}` не переписываются.

### Fixed
- `picture.radius` у неквадратных картинок, растянутых в квадрат, обрезал не по форме (углы снизу оставались почти прямыми). PIXI-маска заменена «запеканием»: картинка рисуется масштабированной и обрезанной скруглённым контуром в отдельный Bitmap размера на экране (`svBakeRounded`). `radius` теперь совпадает с `shape.radius` при тех же размерах.
- `opacity` у picture/shape/gauge не работал: `svUpdateVisibility` каждый раз сбрасывал `alpha = 1` (а `Sprite.opacity` — это и есть `alpha`). Теперь итоговая альфа = opacity × затемнение скрытых элементов в редакторе (`svSetOpacity` / `svUpdateAlpha`).
- `rotation` родителя не применялся к дочерним элементам (`parent`). Теперь элемент хранит матрицу `_svXform`, и дочерние элементы поворачиваются вместе с родителем вокруг его центра, включая вложенность в несколько уровней. Поворот родителя входит в `svParentSig`, поэтому дети перестраиваются при его изменении.
- Editor: при перетаскивании родителя он «дёргался» к исходной позиции — привязка (snap) цеплялась за края его же дочерних элементов, которые догоняют родителя с задержкой в кадр. Потомки перетаскиваемого элемента исключены из целей привязки (`isDescendant`).
- Editor: перетаскивание дочернего элемента повёрнутого родителя — смещение мыши переводится в систему координат родителя, элемент идёт за курсором.

## [0.5.0] + Editor [0.4.0] — 2026-09-23

Этапы 4.5 (вложенные элементы, содержимое стандартных окон) и 5 (темы и настройки игрока). Формат файла остаётся v1.

### Added
- `parent` у элементов: `"w:<поле окна>"` или id элемента. Позиция и проценты — от внутренней области родителя; элемент следует за ним и скрывается вместе с ним (окно закрыто/скрыто).
- `windows.<поле>`: `template`, `rowHeight`, `cols`, `rowBackground`. Адаптеры строк: `Window_MenuStatus` (поля героя), `Window_ItemList` (+ `count`), `Window_SkillList` (+ `mpCost`/`tpCost`), `Window_Command` (`name`, `symbol`, `status`, `iconIndex`). Стандартная отрисовка заменяется только при заданном `template`; шаблон по умолчанию повторяет вид MZ.
- `windows.<поле>.commands` для `Window_Command` (титул, главное меню, опции, магазин…): `text`, `visible` (bool / условие), `order`, `icon`, `align`, `color`, `background`; ключ — символ или `#<индекс>`.
- Тип строки шаблона `actorGauge` — анимированная шкала MZ (`Sprite_Gauge`) с настраиваемым размером.
- `scenes.<Scene_X>.sprites.<поле>`: `_backSprite1/2`, `_gameTitleSprite`, `_backgroundSprite` — `x`, `y`, `scaleX`, `scaleY`, `opacity`, `visible`, `image`; заголовок — `titleText`, `titleFontFace`, `titleFontSize`, `titleColor`, `titleOutlineColor`, `titleOutlineWidth`.
- `ThemeManager`; `themes.<имя>.label` — название темы для игрока.
- Настройки игрока в `Scene_Options` (тема, прозрачность окон, размер шрифта, положение HUD), хранятся в `config.rmmzsave` (`MF.Config`, ключ `MF_SimpleVisual`), применяются поверх разметки. Параметры плагина — белый список: `optTheme`, `themeList`, `optWindowOpacity`, `optFontSize`, `optHudPosition`. Элементы с `"hud": true` получают выбранный игроком `anchor`.
- API: `ListAdapter`, `CommandAdapter`, `SpritePatcher`, `Parents`, `ThemeManager`, `Settings`, `SPRITE_PROPS`, `SPRITE_FIELDS`, `COMMAND_CONF_PROPS`, `ElementFactory.follow()`; `layoutFor()` возвращает `sprites`, `player`.
- Редактор: поле `parent` (фиолетовая рамка — внутренняя область родителя, координаты в его системе); спрайты сцены (оранжевые) — перемещение, масштаб хэндлами, свойства; «Редактировать шаблон строки» для стандартных списков; «Редактировать пункты» для `Window_Command` и элементов `command` (объекты вместо JSON, ↑/↓, скрытие/удаление); `actorGauge` в шаблоне; темы как JSON в разделе «Проект».
- Стиль текста (окна, `window`/`text`/`button`/`command`/`list`, темы): `textColor` (индекс палитры windowskin 0–31 или CSS-цвет), `outlineColor`, `outlineWidth`, `fontBold`, `fontItalic`. Дети шаблона `text`: `fontFace`, `color`, `outlineColor`, `outlineWidth`, `fontBold`, `fontItalic`.
- Шрифты из папки проекта `fonts/`: `"fontFace": "MyFont.ttf"` (также `.otf`, `.woff`, `.woff2`, `titleFontFace`) — загружаются при запуске (`Scene_Boot` ждёт их) или по требованию с перерисовкой после загрузки; `MF.SimpleVisual.Fonts`.
- Выражения со значениями игры в `x`, `y`, `width`, `height`, `rotation`, `opacity` элементов (`"50%-{game.gold}/10"`, `"\\V[3]*2"`); запасное значение `{token|значение}` / `\V[n|значение]` — и в тексте (объекты / пустые значения больше не превращаются в `[object Object]`). Пересчёт с «грязным» флагом: раз в 10 кадров, layout — только при изменении подставленных значений; кэш проверок `MF.SimpleVisual.Dyn`. Ошибки выражений (синтаксис, деление на 0) — стандартное значение + одно предупреждение.
- Стандартные окна (`windows.<поле>`): выражения в `x`, `y`, `width`, `height`, `opacity`, `backOpacity`, `contentsOpacity`; проверка раз в 10 кадров в `Window_Base.update`, применение только при изменении значений. В редакторе — те же «ƒx», живое значение, тест-значения и защита от перетаскивания.
- `rotation` у всех элементов (градусы, вокруг центра). Элемент `shape` (заливка, градиент, рамка, скругление); `picture.radius`.
- Редактор: метка «ƒx» у полей со значениями игры (подсказка с примерами), кнопка «{…}» рядом; под полем — живое значение (`= 124`) и разбор токенов: текущее значение, «⚠ героя нет → 100 (запасное)», «добавьте |значение», ошибки понятным текстом («деление на ноль»). Поле «тест» у каждого токена — тестовое значение только для превью (не сохраняется, сбрасывается при закрытии). Перетаскивание не затирает выражения (сообщение). Кнопка «+ shape».
- Подстановки героев и предметов: `{actor.<id>.hp}`, `{member.<n>.exp}`, `{leader.level}`, `{item.<id>.count}` и т. д.; строки героя (`party`, `Window_MenuStatus`) дополнены параметрами (`atk`…`luk`, `hit`/`eva`/`cri`, `exp`, `nextExp`, `hpRate`, `weapon`, `equips`, `states`, `profile`…). В «Вставить значение» — ветки «Герои (параметры)», «Группа (параметры)» с лидером, «Предметы», «Оружие», «Броня».
- Редактор: «{…} Вставить значение» под текстовыми полями (`text`, `label`, `format`, `emptyText`, `titleText`) — каскадный список с ленивой загрузкой веток: поля строки шаблона, `{game.*}`, выбранная строка списков/меню сцены `{id.поле}`, переменные `\V[n]` (по 50, с именами из БД), герои `\N[n]`, участники `\P[n]`, цвета `\C[n]` с образцами, иконки `\I[n]` с картинками, прочие коды. Известные поля подписаны (RU/EN), остальные — по ключу; показывается текущее значение.
- Редактор: выбор шрифта с образцом текста (MZ `rmmz-mainfont`, `rmmz-numberfont` и файлы `fonts/`), выбор цвета — палитра windowskin (`\C[n]`) и любой цвет; образец цвета рядом с полем.
- Редактор: иерархия объектов — дерево по `parent`, сворачивание/разворачивание («Развернуть всё» / «Свернуть всё»), подэлементы: пункты `Window_Command` и `command`, дети шаблона строки (клик открывает нужный режим и выделяет объект). Перетаскивание элемента на окно/элемент — вложить, на полосу сверху — вынуть; позиция на экране сохраняется, одно действие в истории, защита от циклов.
- Связанные копии шаблонов: `{ "prefab": "<шаблон>", "prefabId": "<id в шаблоне>" }` + только локальные изменения; правка шаблона меняет все копии. Редактор: флажок «связать» при вставке своего шаблона, «↺ Как в шаблоне», «⇪ Применить в шаблон», «⛓ Отвязать».
- Редактор: шаблоны окон — встроенный набор стандартных окон MZ (Gold, Help, Message + NameBox, MapName, MenuCommand, MenuStatus, ItemList + Help, SkillList, TitleCommand, время/шаги, шкалы лидера, кнопка «Назад») и свои шаблоны («★ Сохранить как шаблон», хранятся в `windowTemplates`); вставка с уникальными `id` и сохранением `parent`.
- Подстановки `{game.gold}`, `{game.currency}`, `{game.steps}`, `{game.playtime}`, `{game.map}`, `{game.leader}` и др. в тексте элементов.
- Редактор: визуальный конструктор условий для `condition`, `enabled`, `visible` — «Всегда / Все (И) / Любое (ИЛИ)», вложенные группы, «НЕ»; переключатели, переменные (сравнение с числом или другой переменной), золото, предмет/оружие/броня в инвентаре, герой в группе, скрипт; выбор из списков БД с именами. Сырой JSON — в свёрнутом блоке «JSON».
- Редактор: выбор картинок из `img/<папка>` с миниатюрами и поиском (кнопка «…» у `image`, `windowskin` — `img/system`, `faceName` — `img/faces`).
- Редактор: режим «%» на панели — при перетаскивании позиция и размер сохраняются в процентах от контейнера (экран, родитель, строка шаблона); значения, уже заданные в `%`, остаются процентными и без него. Выбор режима запоминается.

### Fixed
- Редактор: Ctrl+Z / Ctrl+Y / Ctrl+S не срабатывали, пока фокус в панели (например, после выбора `parent` в списке) — изменение выглядело не попавшим в историю. Списки после выбора возвращают фокус игре.

### Changed
- Редактор шаблона: убрана синяя рамка вокруг превью (рамка окна списка и так видна).
- Шаблон `Window_MenuStatus` по умолчанию совпадает с отрисовкой MZ (отступы строки, «Ур» и уровень раздельно).
- `SV_TemplatePreview.setup(list, template, rowHeight, options)` — `options.row`, `options.rowBackground` для стандартных окон.
- Элементы `command` в режиме редактора показывают и скрытые по условию пункты.

## [0.4.1] + Editor [0.3.1] — 2026-09-23

### Fixed
- Редактор шаблона: строка обрезалась при увеличении (MZ обрезает содержимое окна по немасштабированным координатам). Превью теперь рисуется отдельным «художником» и показывается спрайтом, рамка списка — окном без содержимого.

### Added
- Превью шаблона: заглушки для отсутствующих данных — `{поле}` вместо пустого текста, рамки «icon» / «face» вместо пустой иконки/лица.
- Превью шаблона: видно, что к чему относится — синяя рамка окна списка (`padding`), серый фон строки, белая область содержимого (координаты шаблона); подпись с размерами.
- Объект «строка / рамка» в режиме шаблона: высота строки (нижний хэндл), фон строки, `cols` и стиль рамки списка (`padding`, `windowskin`, `opacity`, `backOpacity`, шрифт).
- `list.rowBackground`: `false` — строки без фона.

## [0.4.0] + Editor [0.3.0] — 2026-09-23

Этап 4: собственные меню.

### Added
- Своя сцена `Scene_Custom` (`scenes.<id>` с `"custom": true`): `background` (`blur` / `none` / картинка), `initialFocus`; общая разметка `Scene_Custom`.
- Элементы `command` (`items`, `cols`, `align`, `onCancel`) и `list` (`source`: предметы, герои, навыки, таблицы БД, свои данные; `ids`, `cols`, `rowHeight`, `emptyText`, `action`, `onCancel`).
- Шаблон строки `template`: `text`, `icon`, `face`, `gauge` с подстановками `{поле}`; привязка текста к выбранной строке `{list.name}`; подстановки из строки в действиях (`"value": "{id}"`).
- Действия `custom`, `focus`; фокус мышью.
- `menuCommands` — пункты главного меню (`after` / `before`, `enabled`, `visible`).
- Пресеты `presets` + команда плагина `SetLayoutPreset` (выбор в сохранении), команда `OpenCustomScene`.
- API: `openCustomScene`, `setPreset`, `Presets`, `Template`, `Sources`, `MenuCommands`, `Scene_Custom`, `Window_SVTemplatePreview`, `TEMPLATE_PROPS`, `LIST_SOURCES`; `LayoutStore.base()`, `customScenes()`, `menuCommands()`; `layoutFor()` возвращает `background`, `initialFocus`.
- Пример: `Custom_Bestiary` + пункт «Бестиарий» в меню.
- Редактор: редактирование шаблона строки `list` в изоляции поверх приостановленной сцены (сцена не закрывается, состояние сохраняется); создание и открытие своих сцен (редактор переоткрывается в новой сцене); свойства своей сцены; `menuCommands` как JSON; поля `command`/`list`.
- Редактор: параметры `panelWidth`, `growWindow`.

### Fixed
- Редактор: панель больше не перекрывает игру — канвас сдвигается в свободную область, окно NW.js расширяется на ширину панели.
- Методы классов элементов больше не перезаписываются общими (например, `rows` у `window`/`text` учитывается для `"height": "auto"`).

### Changed
- `LayoutStore.data()` учитывает активный пресет; исходные данные файла — `LayoutStore.base()`.

## [0.3.0] + Editor [0.2.0] — 2026-09-23

Этап 3: добавочные элементы.

### Added
- `scenes.<Scene_X>.elements`: типы `window`, `text`, `button`, `picture`, `gauge`; `anchor`, `z` (под/над окнами), `visible`, `condition`, `opacity`; наследование по классам сцен с перекрытием по `id`. Формат файла остаётся v1.
- `ActionRegistry`: `scene`, `back`, `commonEvent`, `switch`, `variable`, `se`, `script` (только с `allowScript`); массив действий; `MF.SimpleVisual.registerAction()`.
- API: `ElementFactory`, `ActionRegistry`, `ELEMENT_TYPES`, `ELEMENT_PROPS`, `setEditing()`, `isEditing()`, `LayoutStore.elements()`; `LayoutStore.get()` возвращает `elements`.
- Пример в `data/SimpleVisual.json`: фон-картинка, заголовок, текст по условию, шкала переменной, кнопка.
- Редактор: элементы в списке и на экране (зелёная рамка), выбор, перемещение и размер; кнопки «+ window/text/button/picture/gauge»; инспектор по типу элемента (`text` — многострочное поле, `condition`/`action`/`max` — JSON); Delete удаляет свой элемент, унаследованный — скрывает; «Сбросить изменения» для перекрытия унаследованного. Скрытые элементы в редакторе полупрозрачны, логика элементов (кнопки, условия) на паузе.

### Changed
- Editor: объекты адресуются ключами `"w:<поле>"` / `"e:<id>"` (`MF.SimpleVisual.Editor.select`).

## Editor [0.1.0] — 2026-09-23

Этап 2: `js/plugins/MF_SimpleVisualEditor.js`, требует `MF_Core` 1.0.0+ и `MF_SimpleVisual` 0.2.0+. Работает только в тест-режиме.

### Added
- F10 — открыть/закрыть редактор; логика сцены на паузе.
- Overlay: рамки окон, выбор кликом, перемещение, 8 хэндлов размера, привязка к краям экрана, центру и другим окнам (Alt — выключить), сетка (G), направляющие привязки, подпись с координатами. ПКМ отменяет перетаскивание.
- Панель (HTML): список окон сцены, инспектор всех свойств v1 с подсказками унаследованных значений, тема сцены, «Сбросить окно», «Сбросить сцену», сообщения.
- Undo/Redo (Ctrl+Z / Ctrl+Y / Ctrl+Shift+Z), стрелки — сдвиг 1/10 px, Delete — скрыть/показать.
- Сохранение Ctrl+S через `MF.Document`: проверка, атомарная запись, `SimpleVisual.bak.json`; повреждённый исходный файл перезаписывается только после подтверждения. Без NW.js — скачивание.
- Интерфейс RU/EN через `MF.I18n`. Параметры: `gridSize`, `snapThreshold`, `historyLimit`, `panelSide`.
- API `MF.SimpleVisual.Editor`: `open`, `close`, `toggle`, `isActive`, `document`, `select`.

## [0.2.0] — 2026-09-23

### Changed
- Применение разметки декларативное: свойство, пропавшее из разметки, возвращается к стандартному значению (запоминается перед первым изменением). Нужно для undo и сброса в редакторе.
- `LayoutStore.get()` всегда возвращает объект (раньше `null` без разметки); `SceneHook.apply()` возвращает число обработанных окон.

### Added
- `LayoutStore.sceneKey`, `scenes`, `themeNames`; `SceneHook.windowFields`, `windows`; `WindowPatcher.standardRect`, `isPatched`, `defaults`.
- `MF.SimpleVisual.WINDOW_PROPS`, `STYLE_PROPS`, `sanitize`, `resetWarnings`.

## [0.1.0] — 2026-09-23

Первый выпуск runtime (этап 1). Переименование проекта: UIEditor → SimpleVisual (`MF_UIEditor` → `MF_SimpleVisual`, `data/UILayouts.json` → `data/SimpleVisual.json`, `$dataUILayouts` → `$dataSimpleVisual`, документация — `docs/SimpleVisual`).

### Added
- `js/plugins/MF_SimpleVisual.js`, требует `MF_Core` 1.0.0+. Параметр `enabled` (безопасный режим).
- `LayoutStore`: необязательный `data/SimpleVisual.json` через `MF.Data`; при отсутствии/ошибке — стандартный вид. Миграция по `version` (ключ `"SimpleVisual"`), наследование разметки по классам сцен, темы с `extends`, корневой `theme`.
- `WindowPatcher`: `x`, `y`, `width`, `height` (пиксели, `%`, выражения, `"auto"` + `rows`), `anchor`, `visible`, `opacity`, `backOpacity`, `contentsOpacity`, `padding`, `windowskin`, `fontFace`, `fontSize`. Пересоздание `contents` и `refresh()` только при изменении размера/шрифта/отступа.
- `SceneHook`: применение после `scene.create()` и `scene.start()`.
- API `MF.SimpleVisual`: `reload()`, `applyScene()`, `layoutFor()`, модули `LayoutStore`, `WindowPatcher`, `SceneHook`.
- Предупреждение, если включены `AltMenuScreen` / `AltSaveScreen`.
- Пример `data/SimpleVisual.json` для `Scene_Menu`.
