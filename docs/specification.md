# Спецификация Rus Codes

## Входные данные

Rus Codes переводит два числа в одну короткую строку.

Широта и долгота должны быть в фортмате WGS84. Если используются данные в других форматах, то должно быть установлено и четко сформулировано, что это произведет другие местополжения при построении.

## Набор символов

Далее определяется набор сиволов, используемых в Rus Codes. Последовательности, которые содержат прочие символы, по определению не могут быть верными Rus Cod-ами.

### Символы

Символы, используемые в Rus Codes были выбраны так, чтобы их можно было использовать как людям использующим латинский алфавит, так и использующим кириллицу, а так же уменьшить вероятность ошибки при написании.
Ниже приведены символы и их цифровые значения.

|Символ|1|2|3|4|5|6|7|8|9|A|E|K|M|H|O|P|C|T|Y|X|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|Значение|0|1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19|

### Разделитель форматирования

Символ "+" (U+002B) используется как незначащий символ для улучшения форматирования.

### Добавочный символ

Символы "0" (U+0030) используются как добавочные символы перед разделителем форматирования.

## Кодирование

Символы кода и их порядок не меняются в языках, где письмо происходит справа налево.

Широта должна быть обрезана, чтобы ее значение было в диапазоне от -90 до 90.

Долгота должна быть нормализована, чтобы быть в диапазоне от -180 до 180.

### Наиболее значимые 10 символов

Резьме:
Прибавьте 90 к широте и 180 к долготе, чтобы они стали положительными.
Закодируйте широту и долготу по основанию 20, используя символы выше, 5-тью сиволами каждый, т.е. получив значение размерностью 0.000125.
Начиная с широты, чередуйте символы.

Ниже приведен алгоритм кодирования значиний начиная от наименее значимых символов до наиболее значимых:
1. Прибавьте 90 к широте и 180 к долготе, умнождте оба значения на 8000 и возьмите целую часть от широты и долготы соответственно
1. Приставьте к существующему коду символ, который соответствует целой части широты, взятой по модулю 20 
1. Приставьте к существующему коду символ, который соответствует целой части долготы, взятой по модулю 20
1. Разделите обе, широту и долготу на 20
1. Повторите с шага 2 четырежды

### Наименее значимые 5 символов

Этот способ отличается от приведенного выше тем, что каждый шаг порождает один символ.
Широта кодируется по основанию 5 и долгота -- по основанию 4 и затем символы для каждой позиции комбинируются.

Ниже приведен алгоритм кодирования значений от наименне значимых символов до наиболее значимых:
1. Прибавьте 90 к широте и умножьте дробную часть на 2.5e7 и возьмите целую часть за широту.
1. Прибавьте 180 к долготе, умнождте дробную часть на 8.192e6 и возьмите целую часть за долготу.
1. Возьмите целую часть широты по модулю 5. Умножте полученное значение на 4 и прибавьте целую часть долготы, взятую по модулю 4.
1. Приставьте к существующему коду символ со значением выше.
1. Поделите долготу на 4 и широту на 5
1. Повторите с шага 2 четырежды

### Длина кода

Минимально допустимая длина Rus Cod-a -- два символа.
Максимальная длина Rus Cod-a -- 15 символов.

Коды, короче 10 символов, допустимы только четной длины.

Длина по умолчанию для большинства целей равна 10.

### Форматирование

Разделитель форматирования, должен быть вставлен после 8-ми символов.
Если длина кода меньше 8-ми символов, оставшиеся символы перед разделителем форматирования должны состояить из добавочных символов.

### Точность кода

The following table gives the precision of the valid code lengths in degrees and in meters. Where the precisions differ between latitude and longitude both are shown:

| Code length | Precision in degrees | Precision        |
| :---------: | :------------------: | :--------------: |
| 2           | 20                   | 2226 km          |
| 4           | 1                    | 111.321 km       |
| 6           | 1/20                 | 5566 meters      |
| 8           | 1/400                | 278 meters       |
| 10          | 1/8000               | 13.9 meters      |
| 11          | 1/40000 x 1/32000    | 2.8 x 3.5 meters |
| 12          | 1/200000 x 1/128000  | 56 x 87 cm       |
| 13          | 1/1e6 x 1/512000     | 11 x 22 cm       |
| 14          | 1/5e6 x 1/2.048e6    | 2 x 5 cm         |
| 15          | 1/2.5e7 x 1/8.192e6  | 4 x 14 mm        |

NB: This table assumes one degree is 111321 meters, and that all distances are calculated at the equator.

## Decoding

The coordinates obtained when decoding are the south-west corner.
(The north-east corner and center coordinates can be obtained by adding the precison values.)

This implies that the north-east coordinates are not included in the area of the code, with the exception of codes whose northern latitude is 90 degrees.

## Short Codes

Short codes are used relative to a reference location.
They allow the code part to be shorter, easier to use and easier to remember.

Short codes have at least two and a maximum of six digits removed from the beginning of the code.
The resulting code must include the "+" character (the format separator).

Codes that include padding characters must not be shortened.

Digits can be removed from the code, until the precision of the position is less than twice the maximum of the latitude or longitude offset between the code center and the reference location.
Recovery of the original code must meet the same criteria.

For example, 8FVC9G8F+6W has the center 47.365562,8.524813. The following table shows what it can be shortened to, relative to various locations:

| Reference Location | Latitude offset | Longitude offset | Twice max offset | Code can be shortened to |
| ------------------ | --------------: | ---------------: | ---------------: | -----------------------: |
| 47.373313,8.537562 | 0.008           | 0.013            | 0.025            | 8F+6W                    |
| 47.339563,8.556687 | 0.026           | 0.032            | 0.064            | 9G8F+6W                  |
| 47.985187,8.440688 | 0.620           | 0.084            | 1.239            | VC9G8F+6W                |
| 38.800562,-9.064937| 0.620           | 8.565            | 17.590           | 8FVC9G8F+6W              |

Note: A code that has been shortened will not necessarily have the same initial four digits as the reference location.

## Library Implementation Requirements

Open Location Code library implementations must provide:
* a method to convert a latitude and longitude into a 10 digit Open Location Code
* a method to decode a 10 digit Open Location Code into, at a minimum, the latitude and longitude of the south-west corner and the height and width
* a method to determine if a string is a valid sequence of Open Location Code characters
* a method to determine if a string is a valid full Open Location Code
* decode and validation methods must not be case-sensitive

Open Location Code library implementations can provide:
* a method to convert a latitude and longitude into any valid length Open Location Code
* a method to decode any valid length Open Location Code, providing additional information such as center coordinates
* a method to to convert a valid Open Location Code into a short code, given a reference location
* a method to recover a full Open Location Code from a short code and a reference location.
* a method to determine if a string is a valid short Open Location Code

