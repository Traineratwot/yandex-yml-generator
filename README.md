Yml генератор на php
============

Генератор YML для быстрого и *безболезненного* подключения вашего магазина к Yandex.Market. Основные фишки:

**Ультимативно прост.** Состоит из двух файлов: [ymlDocument.php](ymlDocument.php) и [ymlOffer.php](ymlOffer.php). В них соответственно объявлены классы `ymlDocument` и `ymlOffer`, унаследованные от встроенных классов `DomDocument` и `DomElement`. Никакие сторонние библиотеки и фреймворки не требуются. Может пожаловаться на отсутствие `DomElement`, что решается установкой пакета `php-xml`, хотя обычно на хостингах он уже есть.

**Быстрая проверка актуальности.** Документация у Яндекса сделана преимущественно в виде таблиц. Соответственно я задокументировал свои классы такими же таблицами с аналогичным порядком следования полей, описав текущие правила и ограничения. Просто открываете рядом два окна браузера, с документацией Яндекса и моей, и ищете различия. Нет различий - хорошо, есть - пишете на *e-mail* в профиле.

Таблица Яндекса																			| 	Таблица моя		
----------------------- 																| ------------- 
[Общие поля магазина](https://yandex.ru/support/partnermarket/export/yml.html)			| [Общие поля магазина](docs/yml.md)
[Упрощенный тип](https://yandex.ru/support/partnermarket/offers.html)					| [Упрощенный тип](docs/simple.md)
[Произвольный тип](https://yandex.ru/support/partnermarket/export/vendor-model.html)	| [Произвольный тип](docs/arbitrary.md)
[Книги](https://yandex.ru/support/partnermarket/export/books.html)						| [Книги](docs/book.md)

Остальные типы пока на подходе. Кстати не обязательно их все проверять, используются обычно общие поля и какой-то один тип.

**Простота встраивания.** Для каждого типа описаний сделан свой пример использования по принципу "удали лишнее". Порядок следования полей такой же, как и в документации, за исключением вынесенных в конструктор. Примеры выглядят вот так:
```php
// model, vendor, id, price, currencyId, categoryId	, [price from - "цена от ххх руб." ]
$offer = $y->arbitrary( '3811', 'Brand', 'id01id1111', 900, "USD", 15 /* , true*/ );

$offer	->cbid(80)				//	Размер ставки на карточке товара. 0,8 у.е.
	->url("http://magaz.ru/tovar.html")	// !!!	условно обязательный. URL страницы товара 
	//->vat('VAT_10_110') отсутствует в схеме	// Ставка НДС для товара.
```
Здесь `arbitrary()` создает предложение типа «произвольный», в него вынесены гарантированно обязательные поля. Метод  `cbid()` уже не обязателен, его можно просто удалить, если он не нужен. Метод `url()` также можно удалить, но без него не будет работать модель «Переход на сайт» . Далее, метод `vat()` описан в документации, но отсутствует в xsd-схеме указанной в [технических требованиях](https://yandex.ru/support/webmaster/goods-prices/technical-requirements.html) и не проходит валидацию.
 Каждая строчка имеет комментарий, но тут возникают проблемы - в *Sublime Text* всё идеально, в встроенном в *linux Mint* блокноте изредка что-то уезжает, а вот на *github*-е уже прилично едет. Ну и с другими редакторами такое тоже бывает. Файлы примеров:

Тип предложения	| 	Пример												| Дата валидации
----------------| ------------- 										| -------
Упрощенный		| [examples/simple.php](examples/simple.php)			| 24.03.2018
Произвольный 	| [examples/arbitrary.php](examples/arbitrary.php)		| 24.03.2018
Книги			| [examples/book.php](examples/book.php)				| 24.03.2018

Остальные будут немного позже. Валидация проводилась с помощью [сервиса Яндекса](https://webmaster.yandex.ru/tools/xml-validator/), указывая тип *"Маркет"*.

Также был сделан нагрузочный тест [examples/stress-test.php](examples/stress-test.php). Не смотря на установленный memory_limit в 128M у компьютера заканчивалась память(4 Гб). При запуске на 100,000 товарных предложений занятая память поднималась с 1.3 до 2.7 Гб и больше не опускалась. При этом был сгенерирован xml-файл весом 157 Мб (разрешено 500 Мб). Это был максимальный объем, который получилось сгенерировать через браузер. При запуске из консоли на 200,000 товарных предложений потребление памяти выросло с 0.96 до 3.7 Гб, после окончания работы снова вернулось к 0.9 Гб. При этом был создан файл весом 314Мб. Рекомендую не устанавливать нагрузочный тест на рабочий сервер, т.к. он очень удобен для DOS-атаки.

**P.S.** Ставь звездочки, если считаешь, что проект должен быть в топе.