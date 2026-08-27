# Factory must meowing - PrismLauncher 1.21.1 NeoForge 21.1.248

Полная сборка для PrismLauncher. Включает все \minecraft/mods/*.jar\ (~80 МБ).

## Быстрый старт (разработчик)

1. Клонируй репу рядом с PrismLauncher:
\\\ash
git clone <repo> "Factory must meowing"
\\\
2. В PrismLauncher: Add Instance -> Import -> выбери папку \Factory must meowing\ (определится по \mmc-pack.json\ / \instance.cfg\).
3. Запусти - Java 21 берётся из \PrismLauncher/java/java-runtime-delta/bin/javaw.exe\ (\instance.cfg:JavaPath\), \Xmx 8096\ (\instance.cfg:MaxMemAlloc\).

## Структура

\\\
Factory must meowing/
├── mmc-pack.json          # Minecraft 1.21.1 + NeoForge 21.1.248 + LWJGL 3.3.3
├── instance.cfg           # PrismLauncher настройки (Java, RAM, окно)
├── .gitignore
├── README.md
└── minecraft/
    ├── mods/              # 17 jars (см. ниже) - коммитится полностью
    ├── config/            # 25 конфигов (create, modernfix, sodium, sable...)
    ├── kubejs/            # startup_scripts/main.js, client_scripts/main.js (примеры)
    ├── defaultconfigs/    # дефолты модов
    └── options.txt, icon.png
\\\

Не коммитится: \minecraft/logs/\, \crash-reports/\, \saves/\, \screenshots/\, \cache/\, \downloads/\.

## Список модов (minecraft/mods)

| Мод | Файл | Версия | Назначение |
|-----|------|--------|------------|
| Create | \create-1.21.1-6.0.10.jar\ | 6.0.10 | Ядро |
| Create Aeronautics (bundled) | \create-aeronautics-bundled-1.21.1-1.3.1.jar\ | 1.3.1 | \eronautics\+\simulated\+\offroad\ jarJar |
| Sable | \sable-neoforge-1.21.1-2.0.5.jar\ | 2.0.5 | Либа для Aeronautics (\PolyForm Shield\) |
| Greate (патч) | \greate-0.0.46.jar\ | 0.0.46-1.21.1 | См. ниже |
| KubeJS | \kubejs-neoforge-2101.7.2-build.374.jar\ | 374 | Скрипты |
| KubeJS Create | \kubejs-create-neoforge-2101.3.1-build.18.jar\ | 18 | Интеграция |
| JEI | \jei-1.21.1-neoforge-19.44.0.406.jar\ | 406 | Рецепты |
| Sodium | \sodium-neoforge-0.8.13-beta.2+mc1.21.1.jar\ | beta.2 | Рендер |
| Lithium | \lithium-neoforge-0.15.4+mc1.21.1.jar\ | 0.15.4 | Тики |
| FerriteCore | \erritecore-7.0.3-neoforge.jar\ | 7.0.3 | Память |
| ModernFix | \modernfix-neoforge-5.27.20+mc1.21.1.jar\ | 5.27.20 | Загрузка |
| EntityCulling | \entityculling-neoforge-1.10.5-mc1.21.1.jar\ | 1.10.5 | Culling |
| ImmediatelyFast | \ImmediatelyFast-NeoForge-1.6.12+1.21.1.jar\ | 1.6.12 | Batch |
| Architectury | \rchitectury-13.0.11-neoforge.jar\ | 13.0.11 | Либа KubeJS |
| Rhino | \hino-2101.2.8-build.91.jar\ | 91 | JS движок |
| Cloth Config | \cloth-config-15.0.140-neoforge.jar\ | 15.0.140 | Конфиг |
| Configuration | \configuration-neoforge-1.21.1-3.1.1.jar\ | 3.1.1 | Конфиг Greate/Sable |

Транзитивно (внутри): \eil 4.3.2\, \	ransition 1.0.21\, \	render 1.0.15\, \lywheel 1.0.6\, \ponder 1.0.82\.

## Dev гайд - как патчить Greate

Исходник: https://github.com/GreateBeyondTheHorizon/Greate ветка \
eo-1.21.1\.

Патчи для 1.21.1 без GTCEu и без крафтов уже применены в \greate-0.0.46.jar\:

1. Фикс версии конфига:
   \gradle.properties:35\ \3.1.0-neoforge -> 3.1.1-neoforge\ (repsy.io: \configuration-1.21.1/3.1.1-neoforge/\)

2. Сделать GTCEu optional:
   - \src/main/templates/META-INF/neoforge.mods.toml:29\ \	ype=required -> optional\
   - \uild.gradle:149\ \implementation gtceu/ldlib -> compileOnly\
   - \Greate.java:64\ ленивый \GT_REGISTRATE = ModList.isLoaded("gtceu") ? GTRegistrate.create() : null\
   - \Greate.java:98\ \FormattingUtil.toLowerCaseUnderscore -> path.toLowerCase().replaceAll\
   - \Greate.java:102\ иконка \MILLSTONES[GTValues.UHV] -> AllBlocks.BRASS_CASING\
   - \GreateAddon.java\ stub без \@GTAddon\
   - \GreateRegistries.java:17\ ранний \eturn\ если \!isLoaded("gtceu")\

3. Убрать крафты:
   - \GreateRecipes.java:10\ \egister -> return\
   - \GreateRecipeRemoval.java:17\ \egister -> return\
   - \GreateItemApplicationRecipeGen.java:32\ \woodCasingIngredient -> null\
   - \MixinBeltBlock.java\ удалены \greate\\/\greate\\ (\ChemicalHelper\), \MixinSteamEngineBlock.java:65\/\PlacementHelper:38\ stub

4. Сборка:
\\\ash
git clone https://github.com/GreateBeyondTheHorizon/Greate.git
git checkout neo-1.21.1
# применить патчи выше + gradle.properties фикс
./gradlew build --no-configuration-cache -x test
# jar -> minecraft/mods/greate-0.0.46.jar
\\\
   Также нужен \mods/configuration-neoforge-1.21.1-3.1.1.jar\ (423 КБ) - иначе \NoClassDefFoundError: ConfigFormats\.

5. Добавление мода:
   - Скачай с Modrinth для \1.21.1 NeoForge\ (проверь \loaders=[neoforge]\, \game_versions=[1.21.1]\)
   - Положи в \minecraft/mods/\, \git add minecraft/mods/новый.jar\, \git commit -m "add mod X 1.2.3"\

6. Проверка:
   - Удали \minecraft/logs/latest.log\, запусти Prism, проверь \latest.log:ERROR\ (бывшие падения: \ChemicalHelper\, \FormattingUtil\, \ConfigFormats\).

## Примечания

- \create-aeronautics-bundled\ уже содержит \simulated\+\offroad\, \sable\ отдельный обязателен (API: \oWaK0Q19:1.3.1 -> requires sable T9PomCSv\).
- Sodium 0.8.13-beta конфликтует при \roken mod state\ - реальная причина в \Greate\, не в Sodium.
