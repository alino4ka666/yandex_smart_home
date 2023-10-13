## Цветовые профили { id=color-profile }
В УДЯ крайне ограничена возможность выбора цветов и температур для светильников. 
Нам доступны только предустановленные значения: например, режим "Мягкий белый" - это температура в 3400 кельвинов, а цвет "Красный" - это RGB значение `255, 10, 10`

Для переопределения значений цветов/температур можно использовать "цветовые профили". 

Вы можете создать несколько профилей и назначить их на разные светильники. Переопределяйте только те значения, которые вас не устраивают.

### Описание профилей { id=color-profile-config }
Профили настраиваются и назначаются на светильники через [YAML конфигурацию](../config/getting-started.md). Проще всего показать их определение на примере:

!!! example "configuration.yaml"
    ```yaml
    yandex_smart_home:
      color_profile:
        profile_1:
          green: ['0', '255', '0']
          red: ['255', '0', '0']
          warm_white: 3000
        profile_2:
          daylight: 5100
    ```

* `profile_1` и `profile_2`: названия цветовых профилей, могут быть любыми
* `green`, `red`: коды цветов в УДЯ, в значении - [желаемый цвет](#color-matching) в RGB
* `warm_white`, `daylight`: коды режимов/цветовых температур в УДЯ, в значении - [желаемая температура](#temperature-matching) в кельвинах

Список цветов:

| Цвет       | Код         |
|------------|-------------|
| Красный    | `red`       | 
| Коралловый | `coral`     | 
| Оранжевый  | `orange`    | 
| Желтый     | `yellow`    | 
| Салатовый  | `lime`      | 
| Зелёный    | `green`     | 
| Изумрудный | `emerald`   | 
| Бирюзовый  | `turquoise` | 
| Голубой    | `cyan`      | 
| Синий      | `blue`      | 
| Лунный     | `moonlight` | 
| Сиреневый  | `lavender`  | 
| Фиолетовый | `violet`    | 
| Пурпурный  | `purple`    | 
| Розовый    | `orchid`    | 
| Малиновый  | `raspberry` | 
| Лиловый    | `mauve`     | 

Список температур:

| Название        | Код              | По-умолчанию |
|-----------------|------------------|--------------|
| Огненный белый	 | `fiery_white`    | 1500         |
| Мягкий белый	   | `soft_white`     | 2700         |
| Теплый белый	   | `warm_white`     | 3400         |
| Белый           | `white`          | 4500         |
| Дневной белый	  | `daylight`       | 5600         |
| Холодный белый	 | `cold_white`     | 6500         |
| Туманный белый	 | `misty_white`    | 7500         |
| Небесный белый	 | `heavenly_white` | 9000         |

### Встроенные профили { id=internal-color-profile }
В компонент уже встроены цветовые профили, которые можно сразу назначать на светильники без необходимости определения.

| Профиль   | Описание                                                                                                |
|-----------|---------------------------------------------------------------------------------------------------------|
| `natural` | Коррекция цветов под RGB ленты ("реальные" RGB значения для красного, зелёного, синего и других цветов) |

### Назначение профилей { id=color-profile-selection }
Профили назначаются на светильники через параметр `color_profile` в [`entity_config`](../config/entity.md). Один и тот же профиль может использоваться для нескольких светильников.

!!! example "configuration.yaml"
    ```yaml
    yandex_smart_home:
      color_profile:
        profile_1:
          green: ['0', '255', '0']
        profile_2:
          daylight: 5100
      entity_config:
        light.lamp_1:
          color_profile: profile_1
        light.lamp_2:
          color_profile: profile_1
        light.lamp_3:
          color_profile: profile_2
        light.lamp_4:
          color_profile: natural  # профиль, встроенный в компонент
    ```

### Подбор цвета { id=color-matching }
Для получения RGB значения желаемого цвета:

1. Включите светильник
2. Выберите желаемый цвет через цветовой круг в карточке светильника
3. Найдите светильник в `Панель разработчика` --> `Состояния`, в атрибуте `rgb_color` будет указан выбранный цвет
4. При переносе значения из атрибута в цветовой профиль, возьмите каждое число в кавычки: `[10, 20, 30]` --> `['10', '20', '30]`

### Подбор цветовой температуры { id=temperature-matching }
Для получения желаемой температуры в кельвинах:

1. Включите светильник
2. Выберите желаемую температуру в карточке светильника
3. Найдите светильник в `Панель разработчика` --> `Состояния`, в атрибуте `color_temp` будет указан температура в миредах
4. Получите значение в кельвинах по формуле: `1000000/x`, где `x` - значение в миредах. Пример: `color_temp: 371` --> `1000000/371` --> `2695`
5. Округлите значение в кельвинах до ближайшей сотни. Примеры: `2695` --> `2700` или `1125` --> `1100`

## Режимы { id=scene }
Для возможности выбора режима светильника в УДЯ должно выполняться два условия:

1. Светильник в Home Assistant должен поддерживать режимы (эффекты) в атрибуте `effect_list`
2. Настроено соответствие режимов между УДЯ и Home Assistant. Компонент пытается сам связать режимы, но ему это не всегда удаётся из-за большой вариативности в названиях со стороны HA.

Связывание режимов выполняется через `entity_config` в [YAML конфигурации](../config/getting-started.md#yaml).

!!! example "Пример"
    ```yaml
    yandex_smart_home:
      entity_config:
        light.led_strip:
          modes:
            scene:
              sunrise:
                - Wake up
              alarm:
                - Blink
              fantasy:
                - Огоньки
        light.kitchen_lamp:
          modes:
            scene:
              ocean: 
                - Много воды
    ```

* `sunrise`, `alarm`, `fantasy` - значение режима со стороны УДЯ
* `Wake up`, `Blink`, `Огоньки` - названия режимов (эффектов) светильника со стороны Home Assistant (атрибут `effect_list`)

### Доступные режимы  { id=scene-list }
| Значение | Название в УДЯ |
|----------|----------------|
| alarm    | Тревога        |
| alice    | Алиса          |
| candle   | Свеча          |
| dinner   | Ужин           |
| fantasy  | Фантазия       |
| garland  | Гирлянда       |
| jungle   | Джунгли        |
| movie    | Кино           |
| neon     | Неон           |
| night    | Ночь           |
| ocean    | Океан          |
| party    | Вечеринка      |
| reading  | Чтение         |
| rest     | Отдых          |
| romance  | Романтика      |
| siren    | Сирена         |
| sunrise  | Рассвет        |
| sunset   | Закат          |