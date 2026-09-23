# Noizy

**English** | **[Русский](https://github.com/Nord1cWarr1or/noizy/blob/master/README.ru.md)**

Noizy — приложение в системном трее, которое зацикленно проигрывает фоновые звуки: шум, дождь, океан, ветер, гром и другие. Это форк [dwisiswant0/noizy](https://github.com/dwisiswant0/noizy) Дви Сисванто — замены функции Background Sounds из приложения Apple Hearing для Windows, Linux и macOS. Форк существует потому, что апстрим не активен с ноября 2023 года: issue #15 и несколько обновлений зависимостей там остались непринятыми, а исправления живут здесь.

## Отличия от апстрима

- **Reset теперь снимает галочки со звуков.** По нажатию Reset все звуки останавливались, но в меню галочки оставались выставленными ([dwisiswant0/noizy#15](https://github.com/dwisiswant0/noizy/issues/15)). Причина — в предрелизной `fyne.io/systray` от июля 2023: на Linux библиотека отправляла только сигнал dbusmenu `LayoutUpdated`, который десктопные панели считают структурным и по нему не перечитывают свойства пунктов. Форк обновляет `fyne.io/systray` до v1.12.2, где дополнительно отправляется `ItemsPropertiesUpdated` — именно этот сигнал панели применяют ([fyne-io/systray#116](https://github.com/fyne-io/systray/pull/116), [fyne-io/systray#72](https://github.com/fyne-io/systray/issues/72)).

Больше ничего относительно master апстрима не менялось и не выбрасывалось.

## Требования

- [Go](https://go.dev/doc/install) 1.21+ с включённым CGO и компилятором C для целевой платформы.
- Библиотека ALSA на Linux: `libasound2-dev` на дистрибутивах на базе Debian, `alsa-lib-devel` — на базе RedHat.
- Фреймворк Audio Toolbox на macOS (ставится вместе с Xcode).
- На Linux — трей с поддержкой StatusNotifier/AppIndicator; подробности в [заметках о платформах systray](https://github.com/fyne-io/systray#platform-notes).

Для остальных платформ смотрите [требования oto](https://github.com/ebitengine/oto#prerequisite).

## Установка

[Путь Go-модуля](https://pkg.go.dev/github.com/dwisiswant0/noizy) по-прежнему указывает на апстрим, поэтому `go install` этот форк не установит. Сборка из исходников:

```bash
git clone https://github.com/Nord1cWarr1or/noizy.git
cd noizy
make build
```

Бинарь появится в `./bin/noizy`. Чтобы поставить апстрим (там `go install` работает):

```bash
CGO_ENABLED=1 go install github.com/dwisiswant0/noizy@latest
```

В Windows (cmd) переменная задаётся отдельно:

```bat
set CGO_ENABLED=1 && go install github.com/dwisiswant0/noizy@latest
```

### Цели Makefile

| Цель | Результат |
|------|-----------|
| `make build` | `./bin/noizy` под текущую платформу; версия подставляется через ldflags, путь обрезается |
| `make build-windows` | то же, но с `-H=windowsgui`, чтобы не открывалось окно консоли |
| `make clean` | удаляет `./bin` |

## Использование

Запустите бинарь. Приложение целиком живёт в системном трее, окна у него нет.

| Пункт меню | Действие |
|------------|----------|
| Background Sounds | дерево подменю: десять категорий, 30 звуков |
| Noizy &lt;версия&gt; | строка версии (неактивна) |
| Reset | останавливает все играющие звуки и снимает галочки |
| Quit | останавливает все звуки и выходит |

Клик по звуку включает или выключает его; одновременно может играть сколько угодно звуков. По `SIGHUP` приложение делает сброс, по `SIGINT`/`SIGTERM` — выходит. Диагностика пишется в stderr, включая debug-вывод `slog`.

| Категория | Звуки |
|-----------|-------|
| Noise Sounds | Brown Noise, Pink Noise, White Noise |
| Water Sounds | Brook, Creek, Stream, Close Waterfall, Distant Waterfall |
| Coastal & Ocean | Calm Shore, Shore, Wild Shore, Ocean Waves, Large Waves |
| Rain Sounds | Rain Drops, Pouring Rain |
| Thunderstorms | Distant Thunder, Closer Thunder |
| Types of Winds | Coastal Wind, Forest Wind, Autumn Breeze |
| Nature Sounds | Birds, Frogs, Summer Night, Heat Wave |
| Social Settings | Coffee House, Cocktail Voices |
| Relaxation | Meditation Time, Wind Chimes |
| Other | Bonfire, Fan Noise |

Бинарь большой (около 170 МБ), потому что внутрь вшиты все 30 OGG-файлов; OGG выбран вместо MP3 ради качества при том же размере. [Демо](https://github.com/dwisiswant0/noizy/assets/25837540/4be24840-436f-49ed-9397-af93eac23516), записанное на апстриме, показывает трей-меню в работе.

## Участие в разработке

1. Сделайте форк репозитория.
2. Создайте ветку (`git checkout -b fix/my-fix`).
3. Закоммитьте изменения.
4. Запушьте и откройте Pull Request в [Nord1cWarr1or/noizy](https://github.com/Nord1cWarr1or/noizy).

CI гоняет [golangci-lint](https://golangci-lint.run) (`golangci-lint run --timeout=5m`) и semgrep (`semgrep ci` с набором правил `auto`); перед пушем прогоните их локально.

## Лицензия

Noizy распространяется под [GNU GPL v3](/LICENSE). Форк — под той же лицензией, в соответствии с GPL v3 апстрима.

Звуки, вшитые в репозиторий, принадлежат [Стефану Пижону](https://stephanepigeon.com) и предназначены исключительно для личного использования.

## Благодарности

- **Дви Сисванто ([dwisiswant0](https://github.com/dwisiswant0))** — автор оригинального Noizy.
- **Стефан Пижон** — автор звуков.
