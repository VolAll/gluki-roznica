# Глюки и решения по 1с Розница

## Продажа в минуса

Эмурятор ситуации

1. приход на склад 1 штука
1. создаем заказ на 1 штуку
1. пробиваем заказ, создается Чек ККМ на 1 штуку
1. Закрываем смену, создается Отчет розничных продаж, на одну штуку
1. Распроводим созданный ранее заказ на 1 штуку
1. создаем заказ на 1 штуку
1. Продаем , создается чек ККМ на 1 штуку.

## Как починить

Бежим в модуль **ПроведениеСервер**,  Процедура ВыполнитьКонтрольРезультатовПроведения(Объект, Отказ) Экспорт

и меняем запрос

```bsl
ТекстЗапроса = "ВЫБРАТЬ
			|	ТоварыНаСкладахОстатки.Номенклатура КАК Номенклатура,
			|	ТоварыНаСкладахОстатки.Характеристика КАК Характеристика,
			|	ТоварыНаСкладахОстатки.Склад КАК Склад,
			|	ТоварыНаСкладахОстатки.Номенклатура.ЕдиницаИзмерения КАК ЕдиницаИзмерения,
			|	ТоварыНаСкладахОстатки.КоличествоОстаток,
			|	ТоварыНаСкладахОстатки.РезервОстаток,
			|	ТоварыНаСкладахОстатки.КоличествоОстаток - ТоварыНаСкладахОстатки.РезервОстаток  КАК Количество
			|ИЗ
			|	РегистрНакопления.ТоварыНаСкладах.Остатки(
			|			,
			|			(Номенклатура, Характеристика, Склад) В
			|				(ВЫБРАТЬ
			|					Таблица.Номенклатура,
			|					Таблица.Характеристика,
			|					Таблица.Склад
			|				ИЗ
			|					ДвиженияТоварыНаСкладахИзменение КАК Таблица)) КАК ТоварыНаСкладахОстатки
			|ГДЕ
			|	ТоварыНаСкладахОстатки.КоличествоОстаток - ТоварыНаСкладахОстатки.РезервОстаток < 0;
			|///////////////////////////////////////////////////////////////////
			|";
```

на 
```bsl
ТекстЗапроса = "ВЫБРАТЬ
		               |	ВложенныйЗапрос.Номенклатура КАК Номенклатура,
		               |	ВложенныйЗапрос.Характеристика КАК Характеристика,
		               |	ВложенныйЗапрос.Склад КАК Склад,
		               |	ВложенныйЗапрос.ЕдиницаИзмерения КАК ЕдиницаИзмерения,
		               |	ВложенныйЗапрос.КоличествоОстаток КАК КоличествоОстаток,
		               |	ВложенныйЗапрос.РезервОстаток КАК РезервОстаток,
					   |	ВложенныйЗапрос.КоличествоОстатокПоложительный - ВложенныйЗапрос.РезервОстатокПоложительный  КАК Количество
		               |ИЗ
		               |	(ВЫБРАТЬ
		               |		ТоварыНаСкладахОстатки.Номенклатура КАК Номенклатура,
		               |		ТоварыНаСкладахОстатки.Характеристика КАК Характеристика,
		               |		ТоварыНаСкладахОстатки.Склад КАК Склад,
		               |		ТоварыНаСкладахОстатки.Номенклатура.ЕдиницаИзмерения КАК ЕдиницаИзмерения,
		               |		ТоварыНаСкладахОстатки.КоличествоОстаток КАК КоличествоОстаток,
		               |		ТоварыНаСкладахОстатки.РезервОстаток КАК РезервОстаток,
		               |		ВЫБОР
		               |			КОГДА ТоварыНаСкладахОстатки.РезервОстаток < 0
		               |				ТОГДА - ТоварыНаСкладахОстатки.РезервОстаток
		               |			ИНАЧЕ ТоварыНаСкладахОстатки.РезервОстаток
		               |		КОНЕЦ КАК РезервОстатокПоложительный,
		               |		ВЫБОР
		               |			КОГДА ТоварыНаСкладахОстатки.КоличествоОстаток < 0
		               |				ТОГДА 0
		               |			ИНАЧЕ ТоварыНаСкладахОстатки.КоличествоОстаток
		               |		КОНЕЦ КАК КоличествоОстатокПоложительный
		               |	ИЗ
		               |		РегистрНакопления.ТоварыНаСкладах.Остатки(
		               |				,
		               |				(Номенклатура, Характеристика, Склад) В
		               |					(ВЫБРАТЬ
		               |						Таблица.Номенклатура,
		               |						Таблица.Характеристика,
		               |						Таблица.Склад
		               |					ИЗ
		               |						ДвиженияТоварыНаСкладахИзменение КАК Таблица)) КАК ТоварыНаСкладахОстатки) КАК ВложенныйЗапрос
		               |ГДЕ
		               |	ВложенныйЗапрос.КоличествоОстатокПоложительный - ВложенныйЗапрос.РезервОстатокПоложительный < 0;
					   |///////////////////////////////////////////////////////////////////
					   |";
```